# غرفة إدارة الجلسات (Session Management) - التوثيق الفني الشامل

---

## 1. نظرة عامة على الغرفة والبنية الهيكلية (Room Overview & Architecture)

تُعد إدارة الجلسات (Session Management) الحجر الأساس في أمان تطبيقات الويب الحديثة؛ فهي الآلية الهندسية الوحيدة التي تسد الفجوة بين الطبيعة عديمة الحالة (Stateless) لبروتوكول HTTP وبين متطلبات التطبيقات التفاعلية التي تفرض التعرف المستمر على هوية المستخدم وصلاحياته.

تقدم هذه الغرفة تشريحاً متكاملاً لمفاهيم إدارة الجلسات، بدءاً من دورة حياة الجلسة (Session Lifecycle) وحالاتها الأربع، مروراً بنموذج التحكم في الوصول الأمني IAAA (Identification, Authentication, Authorisation, Accountability)، وصولاً إلى المقارنة المعمارية بين أنظمة ملفات تعريف الارتباط (Cookie-based Sessions) وأنظمة الرموز الرقمية (Token-based / JWT). يختتم المسار بمختبر عملي تطبيقي يحاكي بوابة جامعية متكاملة تخزن بيانات الجلسات بطريقة غير آمنة ومفتوحة للتلاعب، مما يسمح بتصعيد الصلاحيات وكشف سجلات الاختبارات المسربة.

```
+-----------------------------------------------------------------------------------+
|                        SESSION MANAGEMENT ARCHITECTURE                            |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|   Client (Browser)                                    Server (App Engine)         |
|   ================                                    ===================         |
|                                                                                   |
|   1. POST /login (Credentials) ---------------------> Authenticate Identity       |
|                                                       Generate CSPRNG Session ID  |
|   2. Receive Set-Cookie / Token <-------------------- Store state in Redis/Cache  |
|                                                                                   |
|   3. GET /protected (With Cookie/Bearer) -----------> Validate Token/Lookup Redis |
|                                                       Check Permissions (AuthZ)   |
|   4. Receive Authorized Resource <------------------- Log Action (Accountability) |
|                                                                                   |
|   5. POST /logout ----------------------------------> Destroy Server Session      |
|   6. Purge Client-side Cookie <---------------------- Send Expiry Response Header |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

---

## 2. الجمهور المستهدف والمتطلبات المسبقة (Target Audience & Prerequisites)

* **الجمهور المستهدف:**
  - مختبرو اختراق تطبيقات الويب (Web Application Penetration Testers).
  - مهندسو أمان البرمجيات (AppSec Engineers) ومطورو الواجهات الخلفية (Backend Developers).
  - محللو المراكز الأمنية (SOC Analysts) والفرق الحمراء والزرقاء (Purple Teams).
* **المتطلبات المسبقة:**
  - إتقان أساسيات بروتوكول HTTP (الطلبات، الاستجابات، بنية الترويسات Headers).
  - فهم أساسيات أداة Burp Suite (اعتراض وتعديل الحزم والترويسات).
  - معرفة عامة بمفاهيم التشفير والترميز (Encoding vs Encryption vs Hashing).

---

## 3. أهداف التعلم والمفاهيم الجوهرية (Learning Objectives & Core Concepts)

1. استيعاب القيد الجوهري لبروتوكول HTTP كبروتوكول عديم الحالة وكيفية تجاوز ذلك عبر معرفات الجلسات (Session IDs).
2. استيعاب نموذج IAAA الأمني والتمييز الدقيق بين إثبات الهوية (Authentication) ومنح الأذونات (Authorisation).
3. إتقان دورة حياة الجلسة بمراحلها الأربع: الإنشاء (Creation)، التتبع (Tracking)، الانتهاء (Expiry)، والإنهاء (Termination).
4. المقارنة التقنية بين جلسات الخادم المعتمدة على الكوكيز وبين رموز JWT المستقلة.
5. استغلال ثغرات التلاعب بالقيم المخزنة لدى العميل (Client-Side Tampering) لتصعيد الصلاحيات واستخراج البيانات المحمية.

---

## 4. الأسس النظرية والتشريح التقني (Theoretical Foundations & Technical Deep Dive)

### 4.1 نموذج IAAA في أنظمة الويب
* **Identification (تحديد الهوية):** ادعاء المستخدم لهويته بتقديم اسم المستخدم أو البريد.
* **Authentication (المصادقة):** إثبات صحة الادعاء (بكلمة مرور، رمز OTP، أو مفتاح أمني FIDO2). يترتب على نجاح هذه الخطوة **إصدار معرف الجلسة**.
* **Authorisation (التفويض):** فحص الصلاحيات الممنوحة للمستخدم للتحقق مما إذا كان مسموحاً له بتنفيذ العملية المطلوبة (مثل قراءة درجات الاختبار أو حذف حساب).
* **Accountability (المساءلة):** ربط العمليات بالهوية عبر سجلات التدقيق والمراقبة لمنع الإنكار.

### 4.2 مقارنة الكوكيز والرموز الرقمية (Cookies vs Tokens)
* **ملفات تعريف الارتباط (Cookies):**
  - تُخزن وتُدار وتُرسل تلقائياً بواسطة المتصفح مع كل طلب HTTP لنفس النطاق.
  - تعتمد على ترويسة الاستجابة `Set-Cookie` وترويسة الطلب `Cookie`.
  - تتطلب سمات حماية صارمة: `Secure` (نقل مشفر فقط)، `HttpOnly` (حجب عن جافا سكريبت لمنع سرقتها عبر XSS)، و `SameSite=Strict` (حماية تامة من CSRF).
* **الرموز الرقمية (Tokens كـ JWT):**
  - بيانات اعتماد مشفرة وموقعة رقمياً يرسلها العميل يدوياً عبر ترويسة `Authorization: Bearer <token>`.
  - لا تحتاج لمخزن جلسات في الخادم (Stateless Scalability)، ولكن إلغاء صلاحيتها الفوري عند الطوارئ يمثل تحدياً تقنياً معقداً.

---

## 5. طوبولوجيا المعمل وسطح الهجوم (Practical Lab Topology & Attack Surface)

* **الهدف:** بوابة إلكترونية جامعية داخلية تعمل عبر المنفذ 80 (`http://MACHINE_IP`).
* **سطح الهجوم (Attack Surface):**
  - نظام المصادقة وتبادل الكوكيز.
  - كوكي الجلسة المرسل بصيغة كائن JSON مرموز بـ Base64 يفتقر إلى التوقيع التشفيري (Cryptographic Signature).
  - ترويسات الصلاحيات المعتمدة على بيانات غير موثوقة من طرف العميل (`"role": "student"`).
* **الأدوار المتاحة:** `student` (طالب)، `lecturer` (محاضر)، `administrator` (مدير نظام).

---

## 6. الأدوات والتقنيات والبروتوكولات (Tools, Technologies & Protocols)

* **الأدوات:**
  - `Burp Suite Community / Professional` (Proxy, Repeater, Decoder).
  - متصفح الويب وأدوات المطورين (Chrome / Firefox DevTools - Storage & Application tabs).
  - أدوات سطر الأوامر: `curl`, `base64`.
* **البروتوكولات:**
  - HTTP/1.1 (Set-Cookie, Cookie, Authorization headers).
  - JSON Web Tokens (JWT) / Base64 Data serialization.

---

## 7. مصفوفة تنفيذ المهام خطوة بخطوة (Step-by-Step Task Execution Matrix)

| المهمة | الموضوع | الإجراء المنفذ | النتيجة الموثقة |
| :---: | :--- | :--- | :---: |
| **01** | Introduction | قراءة المفاهيم التأسيسية لبروتوكول HTTP والجلسات | `No answer needed` |
| **02** | What is Session Management? | تحليل الحالات الأربع لدورة حياة الجلسة | حل 4 أسئلة مفاهيمية |
| **03** | Authentication vs Authorisation | تحليل نموذج IAAA والربط مع إدارة الجلسات | حل 3 أسئلة مفاهيمية |
| **04** | Cookies vs Tokens | تشريح سمات الكوكيز وترويسات Bearer | حل 3 أسئلة تقنية |
| **05** | Securing the Session Lifecycle | مطابقة نقاط الضعف والفشل مع مراحل دورة الحياة | حل 4 سيناريوهات أمنية |
| **06** | Exploiting Insecure Session Management | التلاعب بكوكي الجلسة وتصعيد الدور واستخراج السجلات | استخراج العلم و 5 إجابات |
| **07** | Conclusion | مراجعة المكتسبات المعرفية واستشراف المسار التالي | `No answer needed` |

---

## 8. دليل العمليات الهجومية والتنفيذ العملي (Offensive Security Playbook)

### المرحلة 1: اعتراض وفحص بنية الكوكي
1. بعد تسجيل الدخول كطالب في معمل المهمة 6، نلتقط طلب التصفح في Burp Suite ونفحص كوكي الجلسة:
   ```http
   GET /home HTTP/1.1
   Host: 10.10.x.x
   Cookie: session=eyJyb2xlIjogInN0dWRlbnQiLCAidXNlcm5hbWUiOiAic3R1ZGVudDEifQ==
   ```
2. فك ترميز القيمة في Burp Decoder:
   ```json
   {"role": "student", "username": "student1"}
   ```

### المرحلة 2: التلاعب بالقيمة وتصعيد الدور (Privilege Escalation)
1. تعديل الدور إلى مدير:
   ```json
   {"role": "administrator", "username": "admin"}
   ```
2. إعادة ترميز النص إلى Base64:
   ```bash
   echo -n '{"role": "administrator", "username": "admin"}' | base64
   # النتيجة: eyJyb2xlIjogImFkbWluaXN0cmF0b3IiLCAidXNlcm5hbWUiOiAiYWRtaW4ifQ==
   ```
3. استبدال الكوكي في المتصفح وتحديث الصفحة للدخول المباشر إلى لوحة الإدارة.

### المرحلة 3: جمع البيانات واستخراج الأعلام
1. استعراض قائمة الطلاب في صفحة `Manage Students` لكشف اسم المستخدم للطالب ذي الاسم "X": العلم **`THM{Got.the.User}`**.
2. فحص قائمة المحاضرين: تسجيل محاضر واحد (`1`).
3. حصر الأدوار الإجمالية المسجلة: 3 أدوار (`student`, `lecturer`, `administrator`).
4. فحص جدول المحاولات (Test Attempts): حصر 4 محاولات مسجلة (`4`).
5. قراءة أعلى نتيجة للطالب `student1`: حقق علامة كاملة في اختبار قواعد البيانات (`3`).
6. استخراج مفتاح إجابات اختبار "Database Types": التسلسل هو **`y,n,n`**.

---

## 9. مخطط الدفاع والتحصين والمعالجة الجذرية (Defensive & Remediation Blueprint)

1. **الاعتماد على معرفات الخادم العشوائية (Server-Side Opaque Tokens):**
   - منع تخزين أدوار المستخدم وصلاحياته في كوكيز العميل بنص صريح أو بترميز Base64.
   - استخدام مولد عشوائي آمن تشفيرياً (CSPRNG) يولد 128 بت على الأقل من الإنتروبيا.
2. **ضبط سمات الكوكيز الصارمة:**
   ```http
   Set-Cookie: __Host-sid=a9b8c7d6e5...; Secure; HttpOnly; SameSite=Strict; Path=/
   ```
3. **تطبيق مهل زمنية صارمة:**
   - مهلة ركود (Idle Timeout) لا تتجاوز 15 إلى 30 دقيقة.
   - مهلة قصوى مطلقة (Absolute Timeout) لا تتجاوز 8 إلى 12 ساعة بغض النظر عن النشاط.
4. **تدمير الجلسة في الخادم فوراً عند الخروج (Server-Side Destruction):**
   - حذف مفتاح الجلسة من Redis/Database عند تسجيل الخروج أو عند تغيير كلمة المرور.

---

## 10. الأمن العملياتي وفنون الريد تيم (OpSec & Red Team Operational Tradecraft)

* **إدارة وتداول رموز الجلسات أثناء العمليات:**
  - لا تقم أبداً بمشاركة رموز الجلسات الحية المستخرجة عبر قنوات غير مشفرة أو سجلات نصية عامة.
  - عند استخدام معرف جلسة مسروق لمحاكاة حركة المستخدم (User Emulation)، تأكد من تطابق الـ `User-Agent` و `Accept-Language` مع بيانات الضحية لتجنب إطلاق إنذارات أنظمة كشف السلوك الشاذ (UEBA).

---

## 11. استخبارات التهديدات الواقعية وحالات دراسية (Real-World Threat Intelligence & Case Studies)

* **هجمات سرقة الجلسات عبر البرمجيات الخبيثة (Infostealers):**
  - تركز برمجيات مثل RedLine و Lumma Stealer على قراءة ملفات قواعد بيانات SQLite الخاصة بالمتصفحات واستخراج كوكيز الجلسات الحية لتجاوز المصادقة الثنائية (MFA) بالكامل.
* **حادثة خرق SolarWinds وحملات Golden SAML:**
  - استغلال المهاجمين لشهادات التوقيع لتوليد رموز جلسات موثوقة وموقعة ذاتياً، مما سمح لهم بالوصول إلى أي حساب في السحابة كمديرين دون المرور ببوابة تسجيل الدخول.

---

## 12. استكشاف الأخطاء ومعالجة الحالات المعقدة (Comprehensive Troubleshooting & Edge Cases)

* **المشكلة:** بعد تعديل الكوكي في Burp Suite، يقوم الخادم بإرجاع خطأ `500 Internal Server Error` أو إعادة توجيه إلى `/login`.
  - **السبب:** قد يكون هناك حقل إضافي مطلوب في كائن JSON، أو أن مسافات الترميز في Base64 لم تُضبط بدقة.
  - **الحل:** تأكد من أن صيغة JSON صحيحة تماماً عبر استخدام `jq` أو أداة التحقق من JSON، وتأكد من عدم وجود أسطر فارغة جديدة (`\n`) عند استخدام أمر `base64`.

---

## 13. التحقق المعرفي والتقييم الذاتي (Knowledge Verification & Self-Assessment)

1. ما الفرق التقني الدقيق بين مرحلتي `Session Expiry` و `Session Termination`؟
2. لماذا لا يُعد ترميز Base64 وسيلة أمان كافية لحماية رموز الجلسات المخزنة لدى العميل؟
3. ما هو الدور الأمني لسمة الكوكي `HttpOnly`؟ وما هي الثغرة التي تحمي منها تحديداً؟
4. إذا تمكن فاعل تهديد من تخمين معرف جلسة المستخدم التالي، ففي أي مرحلة من مراحل دورة حياة الجلسة يكمن الخلل؟

---

## 14. المراجع والقراءات المتقدمة (References & Further Reading)

* [ملف المراجع والتوثيق المعتمد للغرفة](../../01-SESSION-MANAGEMENT/REFERENCES.md)
* [الملاحظات التقنية والشروح المعمقة](../../01-SESSION-MANAGEMENT/ADDITIONAL-NOTES.md)
* [مصفوفة مهام وحلول الغرفة](../../01-SESSION-MANAGEMENT/TASKS.md)
* [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
