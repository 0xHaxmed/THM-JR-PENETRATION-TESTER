# Task 02 - Discovering Command Injection (اكتشاف حقن الأوامر في الكود البرمجي)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Command injection vulnerabilities typically exist where developers use system calls to execute administrative utilities, network diagnostics, or file manipulations directly from the underlying operating system.

Let's review two vulnerable code implementations:

1. PHP Implementation:
<?php
$songs = "/var/www/html/songs";
if (isset($_GET["title"])) {
    $title = $_GET["title"];
    $command = "grep $title /var/www/html/songtitle.txt";
    $search = exec($command);
    if ($search == "") {
        $return = "<p>The requested song</p><p> $title does </p><b>not</b><p> exist!</p>";
    } else {
        $return = "<p>The requested song</p><p> $title does </p><b>exist!</b>";
    }
    echo $return;
}
?>

In this snippet:
- The script checks if a parameter named "title" is passed via an HTTP GET request.
- The input is assigned to the variable $title.
- It concatenates $title directly into the command string: "grep $title /var/www/html/songtitle.txt".
- It calls the exec() function, executing the command via the system shell.

2. Python Flask Implementation:
import subprocess
from flask import Flask
app = Flask(__name__)

def execute_command(shell):
    return subprocess.Popen(shell, shell=True, stdout=subprocess.PIPE).stdout.read()

@app.route('/<shell>')
def command_server(shell):
    return execute_command(shell)

In this snippet:
- The Flask route captures anything passed after the forward slash in the URL as the variable shell.
- It passes this variable directly into subprocess.Popen() with shell=True enabled.

Questions:
- What variable stores the user's input in the PHP code snippet in this task?
- What HTTP method is used to retrieve data submitted by a user in the PHP code snippet?
- If I wanted to execute the id command in the Python code snippet, what route would I need to visit?
```

### 2. Arabic Pedagogical Translation
```text
تتواجد ثغرات حقن أوامر نظام التشغيل عادةً عندما يعتمد المطورون على استدعاءات النظام لتشغيل أدوات إدارية، أو أدوات تشخيص شبكية (كـ ping أو nslookup)، أو معالجة الملفات مباشرة عبر نظام التشغيل المضيف.

دعنا نراجع نموذجين برمجيين مصابين:

1. نموذج لغة PHP:
<?php
$songs = "/var/www/html/songs";
if (isset($_GET["title"])) {
    $title = $_GET["title"];
    $command = "grep $title /var/www/html/songtitle.txt";
    $search = exec($command);
    if ($search == "") {
        $return = "<p>The requested song</p><p> $title does </p><b>not</b><p> exist!</p>";
    } else {
        $return = "<p>The requested song</p><p> $title does </p><b>exist!</b>";
    }
    echo $return;
}
?>

في هذا الكود:
- يتحقق السكربت مما إذا كانت المعلمة "title" قد تم تمريرها عبر طلب HTTP GET.
- يتم تخزين المدخل القادم من المستخدم داخل المتغير `$title`.
- يقوم الكود بدمج `$title` مباشرة داخل نص الأمر: `"grep $title /var/www/html/songtitle.txt"`.
- يستدعي دالة `exec()`، مما يؤدي لتنفيذ السلسلة النصية عبر شل النظام.

2. نموذج لغة Python (إطار عمل Flask):
import subprocess
from flask import Flask
app = Flask(__name__)

def execute_command(shell):
    return subprocess.Popen(shell, shell=True, stdout=subprocess.PIPE).stdout.read()

@app.route('/<shell>')
def command_server(shell):
    return execute_command(shell)

في هذا الكود:
- يقوم مسار Flask بالتقاط أي نص ممرر بعد الشرطة المائلة في الرابط كمتغير باسم `shell`.
- يمرر هذا المتغير مباشرة إلى دالة `subprocess.Popen()` مع تفعيل خيار `shell=True`.

الأسئلة:
- ما هو المتغير الذي يخزن مدخلات المستخدم في كود PHP في هذه المهمة؟
- ما هي طريقة HTTP المستخدمة لاسترجاع البيانات المقدمة من المستخدم في كود PHP؟
- إذا أردت تنفيذ أمر id في كود بايثون، فما هو المسار الذي أحتاج إلى زيارته؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Code Review & Vulnerability Identification Pattern
* **في مراجعة الأكواد (Static Code Analysis - SAST):**
  نبحث عن "مصادر المدخلات" (Sinks & Sources):
  - **Source (المصدر):** المدخلات غير الموثوقة: `$_GET["title"]`, `req.query`, `sys.argv`, مسارات الروابط `<shell>`.
  - **Sink (المصب):** الدوال التي تمرر المدخلات للشل: `exec()`, `system()`, `passthru()`, `shell_exec()`, `subprocess.Popen(..., shell=True)`.
  إذا وجدنا مساراً مباشراً يربط الـ Source بالـ Sink دون تنقية أو فصل وسائط، فهناك ثغرة Command Injection مؤكدة.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - في كود PHP: انظر إلى السطر الرابع الذي يقوم بإسناد القيمة من مصفوفة `$_GET`.
> - طريقة الـ HTTP واضحة من اسم المصفوفة الفائقة `$_GET`.
> - في كود Flask: لاحظ أن الديكوريتور `@app.route('/<shell>')` يلتقط المسار بعد الشرطة المائلة.
>
> **المستوى الثاني (توجيهي):**
> - اسم المتغير في PHP يبدأ بعلامة الدولار: `$title`.
> - طريقة HTTP تتكون من 3 أحرف كبيرة: `GET`.
> - المسار المطلوب يبدأ بشرطة مائلة متبوعة باسم الأمر: `/id`.
>
> **المستوى الثالث (الحل المباشر):**
> 1. `$title`
> 2. `GET`
> 3. `/id`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Exploiting the PHP grep Example
إذا مرر المستخدم القيمة التالية عبر GET:
```text
?title=Test; cat /etc/passwd
```
يقوم محرك PHP بتركيب الأمر كالتالي:
```bash
grep Test; cat /etc/passwd /var/www/html/songtitle.txt
```
1. ينفذ الشل الأمر الأول: `grep Test` (يفشل أو ينتهي سريعاً).
2. الفاصلة المنقوطة (`;`) تخبر الشل ببدء أمر جديد تماماً ومستقل.
3. ينفذ الشل الأمر الثاني: `cat /etc/passwd`، مما يقرأ ملف المستخدمين بالكامل ويطبعه!

### 2. Exploiting the Python Flask Route
عند زيارة:
```http
GET /id HTTP/1.1
Host: flaskapp.thm
```
يقوم مفسر بايثون باستدعاء:
```python
subprocess.Popen("id", shell=True, stdout=subprocess.PIPE).stdout.read()
```
وتُرجع الدالة مخرجات أمر `id` كنص صريح للمتصفح:
`uid=1000(flask) gid=1000(flask) groups=1000(flask)`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | السطر البرمجي المرجعي |
| :--- | :--- | :--- |
| **What variable stores the user's input in the PHP code snippet in this task?** | `$title` | `$title = $_GET["title"];` |
| **What HTTP method is used to retrieve data submitted by a user in the PHP code snippet?** | `GET` | مصفوفة `$_GET` الفائقة. |
| **If I wanted to execute the id command in the Python code snippet, what route would I need to visit?** | `/id` | المسار الديناميكي `@app.route('/<shell>')`. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابات الثلاث ومطابقتها حرفياً مع قاعدة تقييم المنصة بنسبة نجاح 100%.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **دوال استدعاء النظام هي مصائد أمنية:** لا ينبغي للمطور اللجوء لاستدعاء أوامر نظام التشغيل الخارجية لتنفيذ مهام تتوفر لها دوال برمجية داخلية مدمجة (مثل استخدام دالة `preg_grep()` أو `file()` بدلاً من استدعاء أمر `grep`).
2. **خيار `shell=True` في بايثون خطيئة معمارية:** تفعيل `shell=True` يجبر بايثون على إطلاق بيئة `/bin/sh` كاملة، مما يسمح بتفسير جميع الرموز الخاصة واستغلال الحقن.
