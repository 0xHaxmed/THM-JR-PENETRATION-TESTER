# Task 05 - Remediation (التحصين الهندسي وتطهير المدخلات)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Preventing command injection requires a multi-layered defensive strategy prioritizing parameterized system APIs and rigorous server-side controls.

Core Defensive Principles:
1. Avoid Direct Shell Invocation:
   Whenever possible, use built-in programming language APIs instead of executing OS shell commands. For instance, rather than executing grep via system(), use native regular expression libraries (preg_match in PHP or re in Python).

2. Parameterisation & Argument Separation:
   If system commands are unavoidable, pass arguments as discrete array elements rather than concatenating them into a single shell string.
   - Secure Python:
     subprocess.run(["ping", "-c", "4", target_ip], shell=False)
     Setting shell=False ensures the operating system treats target_ip strictly as a literal data argument, preventing metacharacters like ; or && from being interpreted as commands.

3. Input Validation vs Input Sanitisation:
   - Input Validation: Checking that input conforms strictly to expected formats (e.g., verifying that an IP address consists of 4 octets between 0-255 using filter_var or strict regex).
   - Input Sanitisation: The process of "cleaning" or stripping out hazardous characters and metacharacters (such as ;, &, |, >, <, $, `) before passing the input to any downstream functions.

4. Client-Side vs Server-Side Validation:
   Client-side controls (such as HTML5 pattern attributes or JavaScript checks) provide user experience feedback but provide ZERO security, as an attacker can bypass them trivially using curl or Burp Suite. Validation and sanitisation must ALWAYS be enforced on the server.

Question:
- What is the term for the process of "cleaning" user input that is provided to an application?
```

### 2. Arabic Pedagogical Translation
```text
تتطلب الوقاية من ثغرات حقن أوامر نظام التشغيل استراتيجية دفاعية متعددة الطبقات تعطي الأولوية لواجهات برمجة التطبيقات المفصولة عن الشل (Parameterized APIs) والضوابط الصارمة من جانب الخادم.

المبادئ الهندسية الوقائية:
1. تجنب استدعاء الشل المباشر:
   كلما كان ذلك ممكناً، استخدم الدوال والواجهات المدمجة في لغة البرمجة بدلاً من تشغيل أوامر نظام التشغيل الخارجية. على سبيل المثال، بدلاً من تشغيل أمر `grep` عبر دالة `system()`، استخدم مكتبات التعبيرات النمطية المدمجة (`preg_match` في PHP أو `re` في Python).

2. تمرير المعاملات كوسائط منفصلة (Parameterisation):
   إذا كان تنفيذ أوامر النظام أمراً لا مفر منه، مرر المدخلات كعناصر مصفوفة منفصلة بدلاً من دمجها في سلسلة نصية واحدة عبر الشل.
   - الكود الآمن في بايثون:
     subprocess.run(["ping", "-c", "4", target_ip], shell=False)
     يضمن ضبط خيار `shell=False` أن يتعامل نظام التشغيل مع `target_ip` كقيمة بيانات نصية مجردة، مما يمنع الشل من تفسير أي رموز خاصة مثل `;` أو `&&` كأوامر جديدة.

3. التحقق من المدخلات مقابل تطهير المدخلات:
   - التحقق من المدخلات (Input Validation): التأكد من أن المدخل يطابق تماماً الصيغة المتوقعة (مثل التأكد من أن عنوان IP يتكون من 4 خانات رقمية صحيحة).
   - تطهير وتنقية المدخلات (Input Sanitisation): عملية "تنظيف" أو تجريد المدخلات المقدمة للتطبيق من الرموز الخطيرة والمحارف الخاصة (مثل `;`, `&`, `|`, `>`, `<`, `$`, `` ` ``) قبل تمريرها لأي دوال معالجة.

4. فحص العميل مقابل فحص الخادم:
   ضوابط جانب العميل (مثل خاصية `pattern` في HTML5 أو كود جافا سكريبت) توفر تجربة مستخدم سلسة ولكنها تقدم صفراً من الأمان، حيث يستطيع المهاجم تجاوزها بسهولة متناهية باستخدام `curl` أو `Burp Suite`. يجب أن يتم التحقق والتطهير دائماً على الخادم حصراً.

السؤال:
- ما هو المصطلح الذي يطلق على عملية "تنظيف" مدخلات المستخدم المقدمة إلى التطبيق؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The Validation & Sanitisation Pipeline
```
[User Input: "127.0.0.1; rm -rf /"]
                 |
                 v
     +-----------------------+
     |   1. VALIDATION       | -> Does it match filter_var(..., FILTER_VALIDATE_IP)?
     +-----------------------+    NO -> Reject immediately! (Exit / 400 Bad Request)
                 |
                 v (If passed)
     +-----------------------+
     |   2. SANITISATION     | -> Strip or escape shell metacharacters:
     +-----------------------+    escapeshellcmd() / escapeshellarg()
                 |
                 v
     +-----------------------+
     |   3. EXECUTION        | -> Execute with shell=False & Argument Lists
     +-----------------------+
```

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** ابحث في النص عن المصطلح الإنجليزي المعبر عن عملية "تنقية" أو "تطهير" المدخلات.
>
> **المستوى الثاني (توجيهي):** الكلمة تبدأ بحرف `s` وتنتهي بـ `tion` وتكتب بالتهجئة البريطانية `sanitisation`.
>
> **المستوى الثالث (الحل المباشر):** المصطلح هو `sanitisation`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Secure PHP Implementation
في لغة PHP، إذا كان المطور مضطراً لتمرير مدخل لأمر نظام:
```php
<?php
// Secure approach: Validation + escapeshellarg
$ip = $_GET['ip'];

// 1. Strict Validation
if (!filter_var($ip, FILTER_VALIDATE_IP)) {
    die("Invalid IP address format.");
}

// 2. Strict Escaping
$safe_ip = escapeshellarg($ip);

// 3. Execution with bounded parameters
system("ping -c 4 " . $safe_ip);
?>
```
* دالة `escapeshellarg()` تقوم بوضع علامات تنصيص مفردة حول السلسلة النصية وتهريب أي علامات تنصيص موجودة، مما يمنع كسر سياق الأمر نهائياً.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | المفهوم الأمني |
| :--- | :--- | :--- |
| **What is the term for the process of "cleaning" user input that is provided to an application?** | `sanitisation` | تطهير وتنقية المدخلات من المحارف والرموز الخاصة غير الآمنة. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابة واعتمادها بنجاح تام 100% على منصة TryHackMe بالتهجئة المعتمدة (`sanitisation`).

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **لا تثق في التحقق من جانب العميل:** فحص المدخلات في المتصفح هو مجرد مساعدة للمستخدم، بينما فحص وتطهير المدخلات على الخادم هو الدرع الأمني الحقيقي.
2. **عزل الوسائط (Parameterisation) هو الحل الأقوى:** تماماً كما تحل الاستعلامات المجهزة (Prepared Statements) ثغرات SQL Injection، فإن تمرير مصفوفات الوسائط بدون استدعاء الشل المباشر (`shell=False`) يقضي على ثغرات Command Injection جذرياً.
