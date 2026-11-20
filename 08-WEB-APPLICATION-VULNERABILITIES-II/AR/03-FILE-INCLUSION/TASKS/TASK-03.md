# Task 03 - Path Traversal (اجتياز المسارات واختراق التسلسل الهرمي)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Path Traversal (also known as Directory Traversal) is a vulnerability that allows an attacker to read arbitrary files on the web server running an application. This might include application code, credentials, sensitive system files, or operating system configuration files.

When web applications dynamically load resources—such as user avatars, downloadable PDFs, or language templates—developers often concatenate user-supplied input directly into file-system calls:
<?php
    $file = $_GET['file'];
    echo file_get_contents('/var/www/app/uploads/' . $file);
?>

If the application does not validate or sanitize the $file parameter, an attacker can supply directory traversal sequences (such as ../ on Linux/Unix or ..\ on Windows) to step out of the designated directory and climb up to the root directory /.

High-Value Linux Target Files:
- /etc/passwd: Contains user account details, UIDs, GIDs, and home directories.
- /etc/shadow: Contains salted password hashes (accessible if the server runs as root).
- /etc/issue or /etc/os-release: Contains OS release and distribution details.
- /proc/version: Contains Linux kernel version and build architecture.
- /var/log/apache2/access.log: Web server access log (useful for log poisoning).
- /root/.ssh/id_rsa: Private SSH key for the root user.

Question:
- What function causes path traversal vulnerabilities in PHP?
```

### 2. Arabic Pedagogical Translation
```text
اجتياز المسارات (Path Traversal أو Directory Traversal) هي ثغرة أمنية تمكن المهاجم من قراءة ملفات عشوائية مخزنة على الخادم الذي يستضيف التطبيق. يمكن أن تشمل هذه الملفات الكود المصدري للتطبيق، أو بيانات الاعتماد، أو ملفات النظام الحساسة، أو ملفات تكوين نظام التشغيل.

عندما تقوم تطبيقات الويب بتحميل الموارد ديناميكياً - مثل الصور الرمزية للمستخدمين، أو ملفات PDF القابلة للتنزيل، أو قوالب اللغات - يقوم المطورون غالباً بدمج المدخلات القادمة من المستخدم مباشرة في دوال قراءة نظام الملفات:
<?php
    $file = $_GET['file'];
    echo file_get_contents('/var/www/app/uploads/' . $file);
?>

إذا لم يقم التطبيق بالتحقق الصارم أو تنقية المتغير `$file`، يستطيع المهاجم تمرير تتابعات اجتياز المجلدات (مثل `../` في أنظمة لينكس/يونكس أو `..\` في ويندوز) للخروج من المجلد المحدد والصعود إلى المجلد الجذري للنظام `/`.

أبرز الملفات المستهدفة عالية القيمة في أنظمة لينكس:
- `/etc/passwd`: يحتوي على تفاصيل حسابات المستخدمين ومعرفاتهم ومسارات مجلداتهم ومفسرات أوامرهم.
- `/etc/shadow`: يحتوي على تجزئات كلمات المرور المشفرة (متاح للقراءة فقط إذا كان الخادم يعمل بصلاحيات root).
- `/etc/issue` أو `/etc/os-release`: يوضح تفاصيل توزيعة نظام التشغيل وإصدارها.
- `/proc/version`: يكشف إصدار نواة لينكس (Kernel) ومعمارية البناء.
- `/var/log/apache2/access.log`: سجل وصول خادم الويب (مدخل رئيسي لهجمات Log Poisoning).
- `/root/.ssh/id_rsa`: مفتاح SSH الخاص بحساب المستخدم الجذري root.

السؤال:
- ما هي الدالة المسببة لثغرات اجتياز المسارات في لغة PHP؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The Directory Traversal Mechanics
```
Application Root: /var/www/html/uploads/
User Input:       ../../../../etc/passwd

Step 1: /var/www/html/uploads/../   -> /var/www/html/
Step 2: /var/www/html/uploads/../../ -> /var/www/
Step 3: /var/www/html/uploads/../../../ -> /var/
Step 4: /var/www/html/uploads/../../../../ -> / (Root Directory)
Resolved Path: /etc/passwd
```
* في أنظمة التشغيل الحديثة، حتى لو قام المهاجم بكتابة 15 تتابعاً (`../../../../../../../../../../../../etc/passwd`)، فإن نواة النظام تتوقف عند الجذر `/` وتكمل المسار من هناك دون إحداث خطأ.

### 2. Operational Security & Sensitive File Discovery
* عند التحقق من وجود ثغرة Path Traversal، اختر دائماً `/etc/passwd` على لينكس أو `C:\Windows\win.ini` على ويندوز؛ فهما ملفان متاحان للقراءة لجميع المستخدمين في النظام، مما يضمن أن نجاح أو فشل القراءة مرتبط بوجود الثغرة وليس بقيود صلاحيات الأذونات (Permissions).

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** ابحث في الكود البرمجي الموضح في نص المهمة عن دالة PHP المستخدمة لقراءة وطباعة محتويات الملف بالكامل.
>
> **المستوى الثاني (توجيهي):** الدالة تبدأ بكلمة `file_` وتتكون من ثلاثة مقاطع باللغة الإنجليزية تعني "جلب محتويات الملف".
>
> **المستوى الثالث (الحل المباشر):** اسم الدالة هو `file_get_contents`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Code-Level Dissection of Vulnerable PHP Functions
تتعدد الدوال في لغة PHP التي يمكن استغلالها لقراءة الملفات الخام:
1. `file_get_contents($filename)`: تقرأ الملف بالكامل في سلسلة نصية واحدة (String).
2. `readfile($filename)`: تقرأ الملف وتخرجه مباشرة إلى مخرج الاستجابة (Output Buffer).
3. `fopen($filename, 'r')` متبوعة بـ `fread()`: معالجة وقراءة الملف كـ Stream.
4. `file($filename)`: تقرأ الملف في مصفوفة من الأسطر.

إذا كانت المدخلات مدمجة بدون تنقية مثل:
```php
<?php
    $document = $_GET['doc'];
    echo file_get_contents("/var/www/html/documents/" . $document);
?>
```
فإن تمرير `?doc=../../../../etc/passwd` يجعل المسار النهائي المعالج بواسطة نواة نظام التشغيل هو `/etc/passwd` مباشرة.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | الوظيفة البرمجية |
| :--- | :--- | :--- |
| **What function causes path traversal vulnerabilities in PHP?** | `file_get_contents` | دالة قراءة محتويات الملفات في PHP عند دمجها بمدخلات غير مفحوصة. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابة واعتمادها في نظام TryHackMe بنسبة نجاح 100%.
* تم اختبار تأثير الدالة في قراءة الملفات المحلية عبر المعامل التفاعلية اللاحقة.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **الفرق بين دوال القراءة ودوال التضمين:** دالة `file_get_contents()` تقرأ وتطبع محتوى الملف فقط حتى لو كان ملف PHP (مما يسرب الكود المصدري)، بينما دالة `include()` تقوم بتنفيذ كود الـ PHP الموجود داخل الملف.
2. **الحل البرمجي الآمن:** استخدام دالة `basename()` لتجريد أي مسارات مجلدات من مدخلات العميل، أو استخدام دالة `realpath()` والتأكد من أن المسار الناتج يبدأ بمجلد التطبيق المسموح به حصراً.
