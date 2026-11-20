# التوثيق الشامل: المرجع المباشر غير الآمن للكائنات (IDOR)

---

## القسم 01: نظرة عامة وبيانات وصفية (Room Overview & Metadata)

| البند (Item) | التفاصيل والمواصفات التقنية (Technical Specification) |
| :--- | :--- |
| **اسم الغرفة (Room Title)** | IDOR (Insecure Direct Object Reference) |
| **المسار التعليمي (Path)** | Jr Penetration Tester (المستوى الوظيفي لمختبر الاختراق المبتدئ - معتمد PT1) |
| **الوحدة التعليمية (Module)** | Web Application Vulnerabilities I (الوحدة 07: ثغرات تطبيقات الويب 1) |
| **معرف الغرفة (Room Slug)** | `idor` (الرابط المباشر: `https://tryhackme.com/room/idor`) |
| **مستوى الصعوبة (Difficulty)** | سهل (Easy) |
| **نوع الغرفة (Room Type)** | إرشادية وتطبيقية (Walkthrough & Practical Lab) |
| **الوقت التقديري للإنجاز (Est. Completion Time)** | 30 دقيقة |
| **إجمالي عدد المهام (Total Tasks)** | 7 مهام تفاعلية (7 Tasks) |
| **التطبيق المستهدف (Target App)** | موقع الأمثلة التفاعلي وبوابة الدعم الفني Acme IT Support |
| **حالة إنجاز الحساب (Completion Status)** | 100% مكتمل (تم التحقق والمطابقة التامة مع حساب المنصة) |

---

## القسم 02: الأهداف التعليمية والعملياتية (Educational & Operational Objectives)

1. **الاستيعاب المفاهيمي لثغرة IDOR وارتباطها بكسر التحكم في الوصول:** فهم كيف تصنف الثغرة في المرتبة الأولى بقائمة OWASP Top 10 (A01: Broken Access Control) وتحت مسمى BOLA في أمن واجهات البرمجة (OWASP API1:2023).
2. **التمييز الدقيق بين المصادقة (Authentication) والتفويض (Authorization):** إدراك أن التطبيقات المصابة بـ IDOR تملك نظام تسجيل دخول سليم، لكنها تفتقر تماماً للتحقق من ملكية السجل المطلوب.
3. **التعامل مع الأشكال المختلفة لمعرفات الكائنات:**
   - **المعرفات الصريحة (Plaintext IDs):** الأرقام التسلسلية المباشرة في الروابط.
   - **المعرفات المرمزة (Encoded IDs):** السلاسل المرمزة بخوارزميات قابلة للفك مثل `base64`.
   - **المعرفات المعالجة بالهاش (Hashed IDs):** السلاسل المبتورة بدوال مثل `md5` والقابلة للاسترجاع عبر جداول البحث المسبقة.
   - **المعرفات العشوائية (Unpredictable UUIDs):** المعرفات صعبة التخمين التي تكسر عبر منهجية الحسابين (`Two-Account Technique`).
4. **اكتشاف مواقع ظهور المعرفات في الطلبات الخلفية:** فحص حركة الشبكة في الـ Network Tab واكتشاف مسارات الـ REST API واستخدام تقنيات تعدين المعاملات (Parameter Mining).
5. **الاستغلال العملي وسحب سجلات المستخدمين الحساسة:** تعديل معاملات استدعاء العملاء `/api/v1/customer?id=X` واستخراج بيانات الحسابين 1 و 3.
6. **تطبيق الحلول الهندسية الجذرية:** الانتقال إلى استنتاج الهوية المباشر من الجلسة (Session-based Resolution)، وربط الاستعلامات بملكية المستخدم (Ownership Scoping).

---

## القسم 03: البنية المعمارية وتخطيط الشبكة (Target Architecture & Network Topology)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    BILINGUAL NETWORK TOPOLOGY                           |
|                                                                                         |
|  [ Authenticated Penetration Tester ]                                                   |
|  Session Identity: User ID 13 (pentester)                                               |
|                 │                                                                       |
|                 │ 1. Asynchronous Background Request:                                   |
|                 │    GET /api/v1/customer?id=1                                          |
|                 ▼                                                                       |
|  +──────────────────────────────────────────────────+                                   |
|  |           Acme IT Support Web Application        |                                   |
|  |──────────────────────────────────────────────────|                                   |
|  | - Authentication Layer: Validates Session Cookie | ➔ Valid Session!                  |
|  | - Authorization Layer: Checks if User 13 owns ID | ➔ MISSING CHECK!                  |
|  +──────────────────────────────────────────────────+                                   |
|                 │                                                                       |
|                 │ 2. Unchecked Database Query:                                          |
|                 │    SELECT * FROM customers WHERE id = 1;                              |
|                 ▼                                                                       |
|  +──────────────────────────────────────────────────+                                   |
|  |                 Backend Database                 |                                   |
|  | Holds Records for: User 1 (adam84), User 3 (john)|                                   |
|  +──────────────────────────────────────────────────+                                   |
|                 │                                                                       |
|                 ▼ 3. Returns User 1's Private PII Data                                  |
|  [ Attacker receives adam84's data -> IDOR COMPLETED! ]                                 |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 04: المتطلبات الأساسية وإعداد البيئة (Prerequisites & Environment Setup)

### 1. المتطلبات المعرفية (Knowledge Prerequisites):
- فهم بنية طلبات HTTP وطرق إرسال واستقبال بيانات الـ JSON في واجهات الـ REST API.
- الإلمام بآليات ترميز النصوص والبيانات الثنائية عبر `base64`.
- معرفة خصائص دوال الهاش التشفيرية (`md5`, `sha1`, `sha256`) ومفاهيم الجداول المسبقة (Rainbow / Lookup Tables).

### 2. الأدوات المستخدمة في الاختبار (Tooling):
- أدوات المطورين في المتصفح (Developer Tools - Network Tab).
- بروكسي Burp Suite لإعادة إرسال وتعديل حزم الطلب (Repeater / Intruder).
- موجه الأوامر لفحص الهاشات وفك التشفير (`base64 -d`, `hash-identifier`).

---

## القسم 05: التحليل النظري والعمق المفاهيمي (Theoretical Deep Dive)

### أ. ما هي ثغرة المرجع المباشر غير الآمن للكائنات (IDOR)؟
تحدث ثغرة IDOR عندما يتيح التطبيق للمستخدم تزويد معرّف مباشر يشير إلى كائن داخلي في النظام (مثل رقم ملف، رقم فاتورة، معرّف مستخدم)، ويقوم الخادم بجلب أو تعديل هذا الكائن استناداً إلى المعرّف المعطى دون التحقق مما إذا كان المستخدم يملك الصلاحية الشرعية للتعامل مع هذا الكائن بالذات.

### ب. الفرق الجوهري بين المصادقة والتفويض:
- **المصادقة (Authentication):** "من أنت؟" يتم التحقق منها عبر اسم المستخدم وكلمة المرور وجلسة الـ Cookie.
- **التفويض (Authorization):** "ما الذي يحق لك فعله؟" هل يحق لصاحب هذه الجلسة قراءة ملف المستخدم رقم 1؟
- في ثغرات IDOR، المصادقة تنجح دائماً، ولكن التفويض غائب تماماً.

### ج. أشكال المعرفات وتصنيفاتها:
1. **الصريحة (Plaintext):** أرقام سهلة التخمين والتسلسل (`?id=100`, `?id=101`).
2. **المرمزة (Encoded):** نصوص Base64 يسهل فكها والتعديل عليها (`MTIz` -> `123`).
3. **المبتورة (Hashed):** سلاسل سداسية عشرية مثل MD5 قابلة للعكس إن كانت لمدخلات تسلسلية قصيرة.
4. **العشوائية (Unpredictable UUIDs):** سلاسل بطول 128 بت، لا يمكن تخمينها ولكنها تتسرب وتستغل بنفس الطريقة بمجرد الحصول عليها.

---

## القسم 06: الاستطلاع ورسم خريطة سطح الهجوم (Reconnaissance & Attack Surface)

أثناء اختبار تطبيقات الويب الحديثة، يتم رصد ثغرات IDOR في الأماكن التالية:
1. **معاملات روابط الاستعلام:** `?user_id=1`, `?invoice=550`, `?doc_id=12`.
2. **أجزاء مسار الـ REST API:** `/api/v1/users/15/messages`, `/api/orders/992`.
3. **أجسام طلبات POST و PUT بصيغة JSON:**
   ```json
   { "account_id": 13, "email": "test@thm.com" }
   ```
4. **الطلبات الخلفية (Background AJAX Requests):** مراقبة تدفق بيانات الـ XHR عبر تبويب Network أثناء التفاعل مع الصفحات الثابتة.

---

## القسم 07: تفكيك السبب الجذري للثغرة (Vulnerability Root Cause Analysis)

### 1. في المختبر الأولي (المهمة 02):
- **السبب الجذري:** الاعتماد على المعامل الصريح من الرابط واستعلام قاعدة البيانات به دون شروط مقترنة بالجلسة:
  ```php
  $user_id = $_GET['user_id'];
  $result = $db->query("SELECT * FROM profiles WHERE id = '$user_id'");
  ```

### 2. في تطبيق Acme IT Support (المهمة 07):
- **السبب الجذري:** واجهة برمجة التطبيقات الخلفية `/api/v1/customer?id={user_id}` تتجاهل التحقق من هوية الجلسة المصاحبة للطلب، وتتعامل مع قيمة `id` كأمر قطعي لجلب السجل المطابق من قاعدة البيانات دون التحقق من ملكية الحساب.

---

## القسم 08: الاستغلال العملي واستخراج الأعلام (Step-by-Step Exploitation)

### السيناريو 1: استغلال المعرف الصريح في مختبر الأمثلة (المهمة 02)
1. فتح الموقع التفاعلي عبر زر **View Site**.
2. ملاحظة الرابط الحالي للحساب المسند:
   `http://online-service.thm/profile?user_id=1305`
3. تعديل المعامل في شريط المتصفح إلى:
   `http://online-service.thm/profile?user_id=1000`
4. يعرض التطبيق ملف المستخدم 1000 محتوياً على العلم:
   > **العلم 1:** `THM{IDOR-VULN-FOUND}`

---

### السيناريو 2: استغلال الـ REST API في Acme IT Support (المهمة 07)
1. إنشاء حساب عميل جديد والدخول إلى صفحة **Your Account**.
2. فتح أدوات المطورين (`F12`) وتحديد تبويب **Network**.
3. تحديث الصفحة ورصد الطلب الخلفي:
   `GET /api/v1/customer?id=13`
4. النقر بزر الفأرة الأيمن واختيار **Edit and Resend** (أو إرسال الطلب عبر Burp Repeater أو `curl`).
5. تعديل المعامل إلى `id=1`:
   ```bash
   curl -s "https://LAB_URL.p.thmlabs.com/api/v1/customer?id=1" -H "Cookie: session=..."
   ```
   - الرد:
     ```json
     { "id": 1, "username": "adam84", "email": "adam@fakemail.thm" }
     ```
   > **اسم مستخدم الحساب 1:** `adam84`

6. تعديل المعامل إلى `id=3`:
   ```bash
   curl -s "https://LAB_URL.p.thmlabs.com/api/v1/customer?id=3" -H "Cookie: session=..."
   ```
   - الرد:
     ```json
     { "id": 3, "username": "john", "email": "j@fakemail.thm" }
     ```
   > **البريد الإلكتروني للحساب 3:** `j@fakemail.thm`

---

## القسم 09: استراتيجيات الدفاع متعدد الطبقات (Defense-in-Depth & Hardening)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           DEFENSE-IN-DEPTH REMEDIATION MODEL                            |
|                                                                                         |
|  [ الطبقة الأولى: استنتاج الهوية المباشر من الجلسة (Implicit Session Context) ]        |
|  - عدم طلب معرّف المستخدم من العميل مطلقاً في العمليات الخاصة بحسابه.                   |
|  - استخراج هوية المستخدم داخلياً من الجلسة الموثوقة: `$_SESSION['user_id']`.            |
|                                                                                         |
|  [ الطبقة الثانية: تقييد الاستعلامات بملكية المستخدم (Ownership Scoping) ]              |
|  - دمج فحص الملكية في استعلام قاعدة البيانات نفسه:                                      |
|    `SELECT * FROM orders WHERE order_id = :id AND user_id = :session_id`                |
|                                                                                         |
|  [ الطبقة الثالثة: خرائط المراجع غير المباشرة (Indirect Reference Maps) ]              |
|  - تحويل المعرفات الحقيقية إلى مفاتيح مؤقتة محصورة في جلسة المستخدم الحالية.             |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 10: الكشف وصيد التهديدات وقواعد SIEM (Detection & SIEM Rules)

### 1. الكشف عن هجمات التعداد التسلسلي لـ IDOR في السجلات:
البحث عن عناوين IP أو جلسات تقوم بطلب معرفات رقمية متتالية بسرعة غير طبيعية:
```bash
awk '$7 ~ /\/api\/v1\/customer\?id=/ {print $1, $7}' /var/log/nginx/access.log | sort | uniq -c | sort -nr
```

### 2. قاعدة Sigma لاكتشاف التعداد المشبوه لمعرفات الكائنات:
```yaml
title: Potential Sequential IDOR Enumeration Pattern
id: 7a8b9c0d-1e2f-3a4b-5c6d-7e8f9a0b1c2d
status: stable
description: Detects multiple sequential object identifier requests from a single client session within a short time window.
logsource:
  category: webserver
detection:
  selection:
    cs-method: 'GET'
    cs-uri-stem|contains:
      - '/api/v1/customer'
      - '/profile'
      - '/invoices'
  condition: selection | count() by c-ip > 50
timeframe: 1m
level: high
```

---

## القسم 11: الأخطاء الشائعة واستكشاف المشكلات وإصلاحها (Troubleshooting)

1. **الاعتقاد بأن الترميز أو الهاش يوفر حماية كافية:**
   - *الخطأ:* تحويل المعرف إلى Base64 أو MD5 والظن بأنه أصبح آمناً.
   - *الحل:* فك المعرف وإعادة ترميزه يستغرق ثوانٍ معدودة؛ الحماية الوحيدة هي فحص الصلاحية في الخادم.
2. **الاعتماد على إخفاء الروابط في الواجهة الأمامية:**
   - *الخطأ:* إخفاء زر التعديل أو الرابط عن المستخدمين العاديين.
   - *الحل:* المهاجم يستخدم أدوات مثل Burp Suite ويرسل طلبات الـ API المباشرة متجاوزاً الواجهة كلياً.
3. **إهمال اختبار دوال الـ PUT و DELETE:**
   - *الخطأ:* فحص التفويض على طلبات العرض (`GET`) وإهمال حماية طلبات التعديل والحذف.
   - *الحل:* تطبيق سياسات التفويض الصارمة على كافة دوال الـ HTTP.

---

## القسم 12: دراسات حالة واقعية ومطابقة مع معايير CVE (Case Studies & CVE Mapping)

1. **تسريب شركة First American Financial (2019):**
   - ثغرة IDOR أدت لتسريب أكثر من **885 مليون** وثيقة رهن عقاري وسجلات ضريبية حساسة امتدت لـ 16 عاماً نتيجة تغيير رقم تسلسلي في الرابط.
2. **ثغرة حذف ألبومات فيسبوك ($12,500 Reward):**
   - ثغرة IDOR في واجهة Graph API سمحت بحذف أي ألبوم صور لأي مستخدم أو صفحة عبر إرسال معرّف الألبوم لدالة الحذف دون فحص الملكية.
3. **التصنيف المعياري:**
   - **CWE-639:** Authorization Bypass Through User-Controlled Key.
   - **OWASP Top 10-2021:** A01:2021 - Broken Access Control.
   - **OWASP API Top 10:** API1:2023 - Broken Object Level Authorization (BOLA).

---

## القسم 13: الملخص العام ومصفوفة الإجابات (Summary & Answers Matrix)

| المهمة (Task) | محور التركيز (Focus Area) | السؤال التقني المعتمد (Verified Question) | الإجابة النهائية (Final Answer) |
| :--- | :--- | :--- | :--- |
| **Task 01** | التعريف النظري | What does IDOR stand for? | `Insecure Direct Object Reference` |
| **Task 02** | استغلال المعرفات الصريحة | Flag from IDOR example website? | `THM{IDOR-VULN-FOUND}` |
| **Task 03** | المعرفات المرمزة | Common type of encoding used by websites? | `base64` |
| **Task 04** | المعرفات المبتورة بالهاش | Common algorithm used for hashing IDs? | `md5` |
| **Task 05** | المعرفات العشوائية | Minimum accounts to check for IDORs? | `2` |
| **Task 06** | مواقع ظهور الثغرة | Read the above. | `No answer needed` |
| **Task 07** | التطبيق العملي للـ API | Username for user id 1? | `adam84` |
| | استخراج البريد الإلكتروني | Email address for user id 3? | `j@fakemail.thm` |

---

## القسم 14: تمارين عملية وتوسعات للتحدي (Practical Exercises & Extensions)

1. **تمرين 1: إعداد وتفعيل إضافة Burp Autorize:**
   - قم بتثبيت إضافة Autorize في Burp Suite، واضبط ملفات تعريف الارتباط للمستخدم العادي، ثم تصفح الموقع بحساب المدير وراقب كيف تكشف الإضافة ثغرات الـ IDOR تلقائياً.
2. **تمرين 2: كتابة أداة فحص آلية لـ BOLA في بايثون:**
   - برمج سكربت بايثون يقوم بتسجيل الدخول بحسابين وتجربة استدعاء معرفات الحساب الأول باستخدام جلسة الحساب الثاني للتحقق من وجود حماية برمجية.
3. **تمرين 3: تطبيق سياسات التفويض في أطر العمل الحديثة:**
   - أنشئ تطبيقاً بسيطاً باستخدام Laravel Policies أو Django Permissions وطبق قاعدة `user_id == session.user_id` وأثبت بالدليل القاطع فشل كافة محاولات التلاعب بالمعرفات.
