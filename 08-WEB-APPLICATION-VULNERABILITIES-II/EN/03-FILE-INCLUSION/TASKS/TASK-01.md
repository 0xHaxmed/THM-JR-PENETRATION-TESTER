# Task 01 - Introduction (المقدمة ومدخل تضمين الملفات)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Welcome to the File Inclusion room!

File inclusion vulnerabilities occur when web applications dynamically include external or local files without proper sanitisation and validation. This allows attackers to manipulate file-path references, enabling unauthorized reading of sensitive server files, source code disclosure, or even full Remote Code Execution (RCE).

In this room, we will explore:
- Path Traversal (Directory Traversal): Navigating the file system hierarchy using dot-dot-slash sequences to read files outside the web root.
- Local File Inclusion (LFI): Forcing the web application execution engine (such as PHP's include/require) to execute or display local server files.
- Remote File Inclusion (RFI): Injecting externally hosted URLs to execute malicious code remotely.
- Practical filter bypasses: Null byte injection (%00), path truncation, double encoding, non-recursive stripping, and directory validation bypasses.
- Defense and remediation: Strict allowlisting, disabling risky PHP settings, and securing file handling.

OWASP Top 10 Alignment:
- A01:2021 - Broken Access Control
- A03:2021 - Injection
- A05:2021 - Security Misconfiguration (e.g. allow_url_include = On)
```

### 2. Arabic Pedagogical Translation
```text
مرحباً بكم في غرفة تضمين الملفات (File Inclusion)!

تحدث ثغرات تضمين الملفات عندما تقوم تطبيقات الويب بتضمين ملفات محلية أو خارجية بشكل ديناميكي دون إجراء تنقية وتحقق صارم للمدخلات. يتيح ذلك للمهاجمين التلاعب بمسارات الملفات، مما يمكنهم من قراءة ملفات النظام الحساسة غير المصرح بها، أو كشف الكود المصدري للتطبيق، أو حتى الوصول إلى تنفيذ الأوامر عن بُعد بالكامل (Remote Code Execution - RCE).

في هذه الغرفة، سنقوم باستكشاف:
- اجتياز المسارات (Path Traversal / Directory Traversal): التنقل عبر التسلسل الهرمي لنظام الملفات باستخدام تتابعات النقاط والشرطة (`../`) لقراءة الملفات خارج مسار الويب الرئيسي (Web Root).
- التضمين المحلي للملفات (Local File Inclusion - LFI): إجبار محرك تنفيذ تطبيق الويب (مثل دوال `include` و `require` في لغة PHP) على تنفيذ أو عرض ملفات محلية من الخادم.
- التضمين البعيد للملفات (Remote File Inclusion - RFI): حقن عناوين URL خارجية لاستدعاء وتنفيذ أكواد برمجية خبيثة من خادم المهاجم عن بُعد.
- الالتفاف العملي على الفلاتر: حقن البايت الفارغ (`%00`)، وتخطي الفلاتر غير التكرارية (Non-recursive stripping)، واستغلال بادئات المجلدات الإلزامية.
- المعالجة والتحصين الهندسي: القوائم البيضاء الصارمة (Allowlisting)، وتعطيل الإعدادات الخطرة في ملف `php.ini`، وتأمين دوال التعامل مع الملفات.

الارتباط بإطار OWASP Top 10:
- A01:2021 - انهيار التحكم في الوصول (Broken Access Control).
- A03:2021 - ثغرات الحقن (Injection).
- A05:2021 - التهيئة الأمنية الخاطئة (Security Misconfiguration كـ `allow_url_include = On`).
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Conceptual Distinction Matrix
```
+-----------------------------------------------------------------------------------+
|                        PATH TRAVERSAL vs LFI vs RFI                               |
+-------------------+---------------------------------------------------------------+
| Path Traversal    | - Goal: Read arbitrary files on the local operating system.   |
|                   | - Functions: file_get_contents(), readfile(), fopen().        |
|                   | - Result: Raw file content returned as plain text.            |
+-------------------+---------------------------------------------------------------+
| Local File        | - Goal: Execute local files within the application runtime.   |
| Inclusion (LFI)   | - Functions: include(), require(), include_once().            |
|                   | - Result: PHP tags (<?php ?>) inside target file ARE EXECUTED!|
+-------------------+---------------------------------------------------------------+
| Remote File       | - Goal: Fetch and execute code from an external URL.         |
| Inclusion (RFI)   | - Precondition: allow_url_include = On & allow_url_fopen = On.|
|                   | - Result: Instant RCE via attacker-hosted webshell payload.   |
+-------------------+---------------------------------------------------------------+
```

### 2. Operational Security (OpSec) & Testing Guardrails
* **عدم قراءة ملفات غير مصرح بها أثناء اختبار الاختراق:** يجب الاكتفاء بقراءة ملفات عامة غير سرية كإثبات أثر (Proof of Concept) مثل `/etc/passwd` أو `/etc/issue` أو `win.ini`، وتجنب قراءة ملفات التجزئة السرية `/etc/shadow` أو المفاتيح الخاصة `id_rsa` دون موافقة خطية صريحة في قواعد الاشتباك (Rules of Engagement).

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** المهمة الأولى تمهيدية تعرض الإطار النظري ومخطط الغرفة.
>
> **المستوى الثاني (توجيهي):** اضغط على زر `Completed` للانتقال إلى تشغيل البيئة الافتراضية.
>
> **المستوى الثالث (الحل المباشر):** لا توجد إجابة نصية مطلوبة (`No answer needed`).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. The Dynamic File Access Vulnerability Lifecycle
1. المطور يبني موقعاً يدعم لغات متعددة:
   ```php
   // Vulnerable PHP Code
   $language = $_GET['lang'];
   include($language);
   ```
2. الاستخدام الشرعي:
   `http://example.thm/index.php?lang=english.php`
3. التلاعب الهجومي (LFI):
   `http://example.thm/index.php?lang=/etc/passwd`
4. استجابة الخادم:
   يفتح محرك PHP مسار `/etc/passwd`، ولأن الملف لا يحتوي على وسوم PHP، يقوم الخادم بطباعة محتواه بالكامل كنص خام في الصفحة، مما يسرب أسماء جميع مستخدمي النظام ومساراتهم الرئيسية ومفسرات الأوامر الخاصة بهم (Default Shells).

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال / المتطلب | الإجابة النموذجية |
| :--- | :--- |
| **Let's continue to the next section to deploy the attached VM.** | `No answer needed` (تم إكمال القراءة بنجاح) |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من مراجعة المفاهيم النظرية واعتماد المهمة على منصة TryHackMe بنسبة 100%.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **الفرق الحاسم بين القراءة والتنفيذ:** Path Traversal يقرأ الملفات كنصوص عادية، بينما LFI/RFI يقوم بتنفيذ أي كود برمجي وارد داخل الملف إذا مرر عبر دوال التضمين (`include`).
2. **سلسلة الهجوم المتقدمة:** يبدأ المهاجم بـ LFI لقراءة `/etc/passwd`، ثم يبحث عن ملفات التكوين وقواعد البيانات، أو ينتقل إلى تسميم السجلات (Log Poisoning) لتحويل ثغرة قراءة الملفات إلى سيطرة كاملة وتنفيذ أوامر تفاعلي (RCE).
