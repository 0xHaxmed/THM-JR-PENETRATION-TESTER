# غرفة: OWASP Top 10: 2025 - إخفاقات الهوية والمصادقة والمساءلة (IAAA Failures)

---

## 1. نظرة عامة على الغرفة وهندستها المعمارية (Room Overview & Architecture)

تُعد غرفة **OWASP Top 10: 2025 - IAAA Failures** (`owasptopten2025one`) المدخل التأسيسي الأول لـ **الوحدة العاشرة: OWASP Top 10 2025** ضمن مسار Jr Penetration Tester على منصة TryHackMe.
تركز الغرفة على استكشاف ثلاثة تصنيفات رئيسية من تصنيفات أمان تطبيقات الويب الحديثة، تتمحور جميعها حول نموذج الثقة الرباعي **IAAA**:
- **A01: Broken Access Control (انهيار ضوابط الوصول):** يمثل التصنيف رقم واحد في قائمة OWASP، حيث يفشل التطبيق في التحقق من أحقية المستخدم في الوصول للكائنات الحساسة.
- **A07: Authentication Failures (إخفاقات المصادقة):** عيوب المنطق في التحقق من هوية المستخدمين، بما يشمل تضارب الحسابات وتجاوز آليات التحقق.
- **A09: Logging & Alerting Failures (إخفاقات التسجيل والتنبيه):** العجز عن رصد الهجمات النشطة (مثل التخمين العنيف) والوصول غير المصرح به للمسارات الإدارية.

```
       +-------------------------------------------------------------+
       |               THE IAAA SECURITY FOUNDATION                  |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
+------------------+         +------------------+         +------------------+
|   IDENTITY &     |         |  AUTHENTICATION  |         |   LOGGING &      |
|  AUTHORISATION   |         |     FAILURES     |         |    ALERTING      |
|    (A01:2025)    |         |    (A07:2025)    |         |   (A09:2025)     |
+------------------+         +------------------+         +------------------+
| - IDOR Exploitation        | - Account Collision        | - Brute-Force Log
| - Horizontal Escalation    | - Case Sensitivity Logic   | - Compromise Trail
| - Unauthorized Data Read   | - Administrative Takeover  | - Secret Endpoints
+------------------+         +------------------+         +------------------+
```

---

## 2. المتطلبات الأساسية وإعداد المعمل (Prerequisites & Lab Setup)

### 2.1 المتطلبات التقنية والمعرفية
- فهم بروتوكول HTTP وأساليب الطلب (`GET`, `POST`) واستجابات الخادم (`200 OK`, `401 Unauthorized`, `403 Forbidden`).
- الإلمام ببروتوكولات الجلسات وإدارة الكوكيز (Cookies).
- معرفة أساسية بكيفية عمل قواعد البيانات ومطابقة النصوص وحساسية حالة الأحرف (Case Sensitivity).
- القدرة على استخدام أدوات سطر الأوامر الأساسية مثل `curl` و `grep` و `awk` لتحليل سجلات الويب.

### 2.2 بنية المعمل الافتراضي
- **الهدف الافتراضي الأول:** تطبيق لإدارة الملاحظات المالية والنصوص (`Notes App`) مصاب بثغرة IDOR في معرّفات الملاحظات.
- **الهدف الافتراضي الثاني:** بوابة تسجيل دخول ومصادقة (`Auth Portal`) تعاني من خلل منطقي في مطابقة أسماء المستخدمين بحالة الأحرف غير الحساسة (`aDmiN` vs `admin`).
- **الهدف الافتراضي الثالث:** سجلات خادم الويب (`access.log`) ومحاكي التحليل الجنائي لرصد هجمات القوة الغاشمة.

---

## 3. الأهداف التعليمية وتصنيف بلوم (Learning Objectives & Bloom's Taxonomy)

بنهاية دراسة هذه الغرفة، يكتسب الدارس الكفاءات التالية:
1. **التذكر والفهم (Remembering & Understanding):** استيعاب تسلسل نموذج IAAA (Identity $\rightarrow$ Authentication $\rightarrow$ Authorisation $\rightarrow$ Accountability) وكيف يؤدي انهيار أي ركيزة إلى سقوط المنظومة كاملة.
2. **التطبيق والتحليل (Applying & Analyzing):** تطبيق تقنيات استغلال ثغرات الإسناد المباشر غير الآمن للكائنات (IDOR) للوصول الأفقي لبيانات مستخدمين آخرين؛ وتحليل عيوب منطق التسجيل (Account Confusion).
3. **التقييم والاستنتاج (Evaluating & Synthesizing):** فحص سجلات الوصول لخوادم الويب واستخراج مؤشرات الاختراق (IoCs)، وتحديد هوية المهاجمين وعناوين الـ IP المستخدمة في الهجوم.
4. **التصميم والدفاع (Creating & Defending):** وضع حلول هندسية جذرية تمنع IDOR بواسطة Indirect Reference Maps وتمنع Account Confusion بفرض معايير Unicode NFC وتطبيع الأسماء.

---

## 4. العمق المفاهيمي والتقني: نموذج IAAA وتصنيفات OWASP (Technical Concept Deep Dive)

### 4.1 خط أنابيب نموذج IAAA
- **1. الهوية (Identity):** الإعلان الصريح للكيان عن نفسه (مثل اسم المستخدم `admin` أو المعرف `user_101`).
- **2. المصادقة (Authentication):** التحقق البرهاني من صحة الادعاء (مثل كلمة المرور أو الشهادة الرقمية أو بصمة FIDO2).
- **3. التفويض (Authorisation):** فحص الصلاحيات الممنوحة لهذا الكيان المصادق عليه (هل يحق له قراءة الملاحظة رقم 4؟).
- **4. المساءلة (Accountability):** التوثيق والتدقيق الجنائي للأفعال لمنع الإنكار (Non-repudiation) وضمان الرصد الفوري.

### 4.2 تفكيك ثغرات التصنيفات
- **A01: Broken Access Control:** يحدث عندما يقبل الخادم معرّف الكائن مباشرة من العميل (`id=3`) دون التحقق من أن المستخدم صاحب الجلسة الحالية يمتلك هذا الكائن فعلياً في جدول قاعدة البيانات (`WHERE id = 3 AND owner_id = session.user_id`).
- **A07: Authentication Failures:** يحدث عندما تتفاوت الحساسية لحالة الأحرف بين واجهة التسجيل وقاعدة البيانات. عند تسجيل `aDmiN`، يقبل التطبيق التسجيل كحساب جديد، ولكن عند جلب بيانات لوحة التحكم، يقوم استعلام قاعدة البيانات أو التخزين المؤقت بمطابقة غير حساسة لحالة الأحرف (`admin`)، مما يمنح المهاجم صلاحيات الحساب الأصلي.
- **A09: Logging & Alerting Failures:** غياب أنظمة رصد معدل الفشل (Rate-limiting) وغياب التنبيهات الفورية عند تكرار استجابات `401 Unauthorized` يتيح للمهاجم تنفيذ مئات محاولات التخمين دون أن يكتشفه أحد.

---

## 5. سطح الهجوم ونمذجة التهديدات (Attack Surface & Threat Modeling)

```
[Attacker] 
   |
   +---> (1) Fuzz Note IDs: /notes?note_id=3 ---> [IDOR Vulnerability] ---> Reads Millionaire Note
   |
   +---> (2) Register User: 'aDmiN' ---------> [Account Confusion]   ---> Usurps 'admin' Dashboard
   |
   +---> (3) Brute Force: POST /login --------> [Logging Failure]     ---> Hits /supersecretadminstuff
```

- **نقطة الدخول الأولى:** معاملات الطلبات البرمجية في واجهات برمجة التطبيقات (API Query Parameters) المرتبطة بمعرفات الملاحظات.
- **نقطة الدخول الثانية:** حقول إدخال أسماء المستخدمين في استمارة التسجيل وإنشاء الحسابات.
- **نقطة الدخول الثالثة:** واجهات المصادقة المكشوفة بدون قيود معدل الطلبات أو آليات الحظر الذاتي (Fail2ban).

---

## 6. منهجية الاستغلال والحل العملي المفصل خطوة بخطوة (Exploitation Methodology)

### الخطوة 1: استغلال ثغرة IDOR وتصعيد الصلاحيات الأفقي (Task 03)
1. بعد تسجيل الدخول بحساب المستخدم العادي، نلاحظ أن رابط استعراض الملاحظات يستخدم المعرف المباشر:
   ```http
   GET /view_note?id=1 HTTP/1.1
   Host: target.thm
   Cookie: session=ey...
   ```
2. نقوم بتعديل المعرف رقمياً أو استخدام هجوم تجريب الأرقام (Enumeration / Intruder) لفحص الأرقام المتتالية:
   ```bash
   for id in {1..10}; do
     curl -s -b "session=user_cookie" "http://target.thm/view_note?id=$id" | grep -i "THM{"
   done
   ```
3. عند الوصول إلى المعرف الخاص بالمليونير، تظهر الملاحظة السرية وبداخلها الراية المعتمدة:
   ```text
   Flag: THM{Found.the.Millionare!}
   ```
4. نوع تصعيد الصلاحيات هنا هو: **Horizontal** (أفقي: مستخدم يصل لبيانات مستخدم آخر بنفس المستوى).

### الخطوة 2: استغلال تضارب الحسابات وحالة الأحرف (Task 04)
1. النظام يمنع تسجيل اسم المستخدم `admin` لأن الحساب موجود مسبقاً.
2. نقوم بإنشاء حساب جديد بالاسم مع خلط حالة الأحرف: `aDmiN`.
3. يقبل الخادم التسجيل ويقوم بإنشاء سجل للمستخدم.
4. عند تسجيل الدخول بهذا الحساب، يقوم خادم الويب بمعالجة اسم المستخدم بطريقة غير حساسة لحالة الأحرف في لوحة التحكم، مما يعرض بيانات حساب المدير `admin` وراية التحدي:
   ```text
   Flag: THM{Account.confusion.FTW!}
   ```

### الخطوة 3: التحقيق الجنائي الرقمي في السجلات (Task 05)
1. فحص سجلات الوصول `access.log` للبحث عن المحاولات المتكررة لطلب مسار تسجيل الدخول المصحوبة برموز خطأ `401`:
   ```bash
   grep "POST /login" access.log | grep " 401 " | awk '{print $1}' | sort | uniq -c | sort -nr
   ```
   **النتيجة:** عنوان الـ IP المهاجم هو: `203.0.113.45`.
2. فحص المحاولة الناجحة الأخيرة برمز استجابة `200` أو `302` التي تلت محاولات التخمين:
   ```bash
   grep "203.0.113.45" access.log | grep -E " 200 | 302 "
   ```
   **النتيجة:** اسم المستخدم المخترق هو: `admin`.
3. فحص المسارات الحساسة التي زارها المهاجم عقب المصادقة الناجحة:
   ```bash
   grep "203.0.113.45" access.log | grep "GET "
   ```
   **النتيجة:** المسار السري هو: `/supersecretadminstuff`.

---

## 7. التحليل الجنائي وتفتيش السجلات (Forensic Analysis & Log Inspection)

نموذج لسجلات الخادم المستخرجة أثناء التحقيق:
```text
203.0.113.45 - - [19/Nov/2026:10:14:02 +0000] "POST /login HTTP/1.1" 401 142 "user=admin"
203.0.113.45 - - [19/Nov/2026:10:14:05 +0000] "POST /login HTTP/1.1" 401 142 "user=admin"
203.0.113.45 - - [19/Nov/2026:10:14:09 +0000] "POST /login HTTP/1.1" 401 142 "user=admin"
203.0.113.45 - - [19/Nov/2026:10:14:14 +0000] "POST /login HTTP/1.1" 302 450 "user=admin"
203.0.113.45 - - [19/Nov/2026:10:14:18 +0000] "GET /supersecretadminstuff HTTP/1.1" 200 4812
```

الملاحظات الجنائية المستنبطة:
- الفاصل الزمني بين الطلبات قصير جداً (3-5 ثوانٍ)، مما يشير إلى أداة تخمين مؤتمتة (Automated Brute-Forcer).
- عدم وجود حظر مؤقت لعنوان الـ IP بعد 3 محاولات فاشلة يثبت غياب ضوابط Rate Limiting.
- نجاح الطلب الرابع وظهور الرمز `302` يشير إلى اكتشاف كلمة المرور الصحيحة.

---

## 8. الهندسة الدفاعية والحلول الجذرية (Defensive Engineering & Remediation)

### 8.1 حماية ضوابط الوصول ومنع ثغرات IDOR
- **استخدام Indirect Object Reference Maps:** عدم تمرير المفاتيح الأساسية لقواعد البيانات في المتغيرات الخارجية.
- **فرض التحقق على مستوى الكائن (Object-Level Authorization):**
  ```python
  # تطبيق آمن للتحقق من ملكية الملاحظة
  def get_user_note(note_id, current_user):
      note = Note.query.filter_by(id=note_id, user_id=current_user.id).first()
      if not note:
          raise PermissionDenied("Access Denied or Resource Not Found")
      return note
  ```

### 8.2 منع تضارب الحسابات وتطبيع الأسماء (Account Confusion Remediation)
- تحويل كافة أسماء المستخدمين إلى أحرف صغيرة وموحدة فور استلامها (`username.strip().lower()`).
- تطبيق معيار **Unicode Normalization Form C (NFC)** لمنع هجمات الحروف المتشابهة (Homoglyphs).
- ضبط الفهارس في قواعد البيانات لتكون حساسة للثنائيات (`BINARY` أو `utf8mb4_bin`) مع منع التكرار.

### 8.3 بناء منظومة مراقبة وتسجيل متينة (Defensible Logging)
- تسجيل كافة محاولات الدخول الفاشلة وربطها بعنوان IP ومعرف الجلسة واسم الحساب المستهدف.
- إرسال السجلات فوراً عبر بروتوكول مشفر (Syslog over TLS / RFC 5424) إلى نظام SIEM خارجي غير قابل للتعديل (WORM Storage).
- تفعيل آليات الدفاع التلقائي (Automatic Account Lockout / Adaptive Rate Limiting) عند تجاوز العتبة الآمنة (مثل 5 محاولات فاشلة).

---

## 9. مرجع الأوامر والأدوات (Tools & Command Reference)

| الأداة | الأمر النموذجي | الغرض العملياتي |
| :--- | :--- | :--- |
| `curl` | `curl -s -b "cookie" "http://target/view?id=2"` | اختبار معاملات الـ IDOR يدوياً |
| `grep` | `grep "POST /login" access.log \| grep "401"` | فلترة محاولات تسجيل الدخول الفاشلة |
| `awk` | `awk '{print $1}' access.log \| sort \| uniq -c` | إحصاء الطلبات حسب عنوان IP المهاجم |
| `sort -nr` | `sort -nr` | ترتيب العناوين الأكثر إرسالاً للطلبات تنازلياً |

---

## 10. الخلاصة المعرفية والخريطة الذهنية (Key Takeaways & Mind Map)

```
                 +-----------------------------------------+
                 |    IAAA VULNERABILITY TAXONOMY 2025     |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
[A01: Broken Access Control]                              [A07: Authentication]
- Root: Missing ownership validation                      - Root: Insensitive string logic
- Exploit: Incremental numeric IDOR                       - Exploit: Mixed-case 'aDmiN' collision
- Fix: Strict DB owner matching                           - Fix: Lowercase NFC canonicalization
                                      |
                                      v
                         [A09: Logging & Alerting]
                         - Root: Silent brute-force failures
                         - Exploit: Undetected credential guess
                         - Fix: SIEM anomaly rules & WORM storage
```

---

## 11. مصفوفة المخاطر والثغرات (Vulnerability & Risk Matrix)

| الثغرة (Vulnerability) | التصنيف (OWASP 2025) | الشدة (Severity) | معرّف CWE | الأثر التجاري والتقني (Impact) |
| :--- | :---: | :---: | :---: | :--- |
| Insecure Direct Object References (IDOR) | **A01:2025** | **عالية جداً (HIGH)** | CWE-639 | تسريب بيانات المستخدمين والملاحظات الخاصة للمليونير. |
| Account Collision Logic Flaw | **A07:2025** | **حرجة (CRITICAL)** | CWE-178 | الاستيلاء الكامل على حساب المدير ولوحة التحكم الإدارية. |
| Insufficient Security Logging & Alerting | **A09:2025** | **متوسطة (MEDIUM)** | CWE-778 | تمكين المهاجمين من التخمين المستمر دون رصد أو حظر. |

---

## 12. التحقق وإثبات الإنجاز (Verification & Proof of Work)

تم إكمال كافة مهام الغرفة بنسبة 100% ومطابقتها مع الحساب المعتمد على المنصة:
- **المهمة 01:** `No answer needed`
- **المهمة 02:** `Identity, Authentication, Authorisation, Accountability`
- **المهمة 03:** `Horizontal` و `THM{Found.the.Millionare!}`
- **المهمة 04:** `THM{Account.confusion.FTW!}`
- **المهمة 05:** `203.0.113.45` و `admin` و `/supersecretadminstuff`
- **المهمة 06:** `No answer needed`

---

## 13. الأسئلة الشائعة والأخطاء المحتملة (Frequently Asked Questions)

### س1: ما الفرق بين تصعيد الصلاحيات الأفقي (Horizontal) والرأسي (Vertical)؟
- **الأفقي (Horizontal):** الوصول لبيانات أو صلاحيات مستخدم آخر في نفس المستوى (مثل مستخدم عادي يقرأ ملاحظات مستخدم عادي آخر).
- **الرأسي (Vertical):** الانتقال من مستوى أدنى إلى مستوى أعلى (مثل تحول مستخدم عادي إلى مدير عام `admin`).

### س2: لماذا نجح تسجيل المستخدم `aDmiN` رغم وجود `admin`؟
- لأن التحقق أثناء إنشاء الحساب استخدم مقارنة نصوص حساسة لحالة الأحرف في الكود البرمجي فاعتبرهما مختلفين، بينما استعلام لوحة التحكم أو قاعدة البيانات استخدم مطابقة غير حساسة لحالة الأحرف (`case-insensitive`) مما أدى إلى دمج الحسابين.

---

## 14. المراجع وروابط التوثيق الرسمية (References & External Links)

- [OWASP Top 10: 2025 Official Documentation](https://owasp.org/Top10/)
- [NIST Special Publication 800-63B: Digital Identity Guidelines](https://csrc.nist.gov/publications/detail/sp/800-63/3/final)
- [CWE-639: Insecure Direct Object References (IDOR)](https://cwe.mitre.org/data/definitions/639.html)
- [TryHackMe Room: OWASP Top 10: 2025 - IAAA Failures](https://tryhackme.com/room/owasptopten2025one)
