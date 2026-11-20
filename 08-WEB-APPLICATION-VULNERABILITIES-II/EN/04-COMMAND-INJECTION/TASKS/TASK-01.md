# Task 01 - Introduction (المقدمة ومدخل حقن أوامر نظام التشغيل)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Welcome to the Command Injection room!

Command injection (also known as OS Command Injection) is a vulnerability that occurs when an application executes arbitrary operating system commands on the server hosting the application. This typically happens when user-supplied input is concatenated directly into system shell calls without proper validation, sanitisation, or parameterisation.

Key Concepts:
- Privilege Context: Injected commands execute with the exact same privileges as the web server process (such as www-data or joe). While this may not initially grant root access, an attacker can use this initial foothold to inspect the filesystem, exfiltrate credentials, and escalate privileges locally.
- Command Injection vs Remote Code Execution (RCE): RCE is the broad vulnerability category describing any flaw that allows code execution on a remote machine. Command Injection is a specific attack vector within RCE where the payload directly interacts with the host operating system's command interpreter (e.g., bash, sh, cmd.exe, PowerShell).
- Standards Alignment:
  - OWASP Top 10: A03:2021 - Injection
  - CWE-78: Improper Neutralization of Special Elements used in an OS Command
```

### 2. Arabic Pedagogical Translation
```text
مرحباً بكم في غرفة حقن أوامر نظام التشغيل (Command Injection)!

حقن الأوامر (المعروف أيضاً بـ OS Command Injection) هي ثغرة أمنية تقع عندما يقوم تطبيق الويب بتنفيذ أوامر نظام تشغيل عشوائية على الخادم المستضيف للتطبيق. يحدث هذا عادةً عندما يتم دمج المدخلات المقدمة من المستخدم مباشرة في استدعاءات شل النظام (Shell Calls) دون إجراء تحقق، أو تنقية، أو فصل للمدخلات عن المعاملات البرمجية.

المفاهيم الجوهرية:
- سياق الصلاحيات (Privilege Context): يتم تنفيذ الأوامر المحقونة بنفس صلاحيات عملية خادم الويب تحديداً (مثل المستخدم `www-data` أو `joe`). ورغم أن هذا قد لا يمنح صلاحيات المستخدم الجذري (root) فوراً، إلا أنه يوفر موطئ قدم أولي (Foothold) يتيح للمهاجم فحص نظام الملفات، وسرقة بيانات الاعتماد، والبدء في تصعيد الصلاحيات محلياً (Privilege Escalation).
- حقن الأوامر مقابل تنفيذ الأكواد عن بُعد (RCE): تنفيذ الأكواد عن بُعد هو التصنيف المظلي الأوسع الذي يصف أي ثغرة تسمح بتنفيذ تعليمات برمجية على آلة بعيدة. بينما يمثل حقن الأوامر تكتيكاً محدداً ضمن RCE يتفاعل فيه المهاجم مباشرة مع مفسر أوامر نظام التشغيل المضيف (مثل bash أو sh في لينكس، أو cmd.exe و PowerShell في ويندوز).
- الارتباط بالمعايير العالمية:
  - إطار OWASP Top 10: التصنيف A03:2021 - ثغرات الحقن (Injection).
  - إطار CWE: التصنيف CWE-78 - المعالجة غير السليمة للعناصر الخاصة في أوامر نظام التشغيل.
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The Execution Boundary & Threat Model
```
[User Browser Input] ➔ "8.8.8.8; whoami"
                                |
                                v
                [Vulnerable Application Script]
          exec("ping -c 4 " . $_GET['ip']);
                                |
                                v
               [Operating System Shell: /bin/sh]
          ping -c 4 8.8.8.8 ; whoami
               /                 \
       (Executes ping)       (Executes whoami!)
                                |
                                v
                [Returns: www-data to Attacker]
```

### 2. Operational Security (OpSec) Considerations
* **الفرق بين أوامر الاستطلاع والتنفيذ التخريبي:** في عمليات الفريق الأحمر واختبار الاختراق المعتمد، تجنب إطلاق أوامر تدميرية أو تستهلك الموارد (مثل `rm -rf` أو `fork bombs`). ابدأ بأوامر خفيفة غير ضارة لإثبات الأثر مثل `whoami`, `id`, `hostname`.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** المهمة الأولى هي مدخل نظري يقدم تعريفات الثغرة وارتباطاتها بمعايير OWASP و CWE.
>
> **المستوى الثاني (توجيهي):** اضغط على زر `Completed` أو زر المتابعة للانتقال إلى دراسة كيفية اكتشاف الثغرة في كود التطبيقات.
>
> **المستوى الثالث (الحل المباشر):** لا توجد إجابة نصية مطلوبة (`No answer needed`).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Root Cause Analysis: Shell Metacharacters
السبب الجذري لثغرة Command Injection هو استدعاء مفسر الأوامر (System Shell) لتمرير سلسلة نصية تحتوي على "رموز خاصة" (Metacharacters) دون عزل المدخلات:
* رموز الفصل والتسلسل في لينكس: `;`, `&`, `&&`, `|`, `||`, `\n` (Newline).
* رموز التوجيه والفرز: `>`, `>>`, `<`, `|`.
* رموز التضمين الفرعي: `$(command)`, `` `command` ``.

إذا لم تقم الدالة بفصل الأمر عن وسائطه (مثل استخدام `subprocess.run(["ping", "-c", "4", user_input])` بدون `shell=True`)، فإن الشل يقوم بتفسير الرموز الخاصة كأوامر إضافية جديدة ويقوم بتنفيذها فوراً.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال / المتطلب | الإجابة النموذجية |
| :--- | :--- |
| **Click me to proceed to the next tasks.** | `No answer needed` (تم إكمال القراءة بنجاح) |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من استيعاب المفاهيم التأسيسية وتأكيد إكمال المهمة بنسبة 100% على منصة TryHackMe.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **حقن الأوامر يكسر طبقة العزل (Sandboxing):** يخرج التطبيق من بيئة تنفيذ الويب المحصورة (Web Runtime Sandbox) إلى نظام التشغيل المباشر.
2. **صلاحيات العملية تحدد الأثر:** المهاجم يرث صلاحيات خادم الويب (`www-data`). ورغم أنها صلاحيات محدودة، إلا أنها تكفي لقراءة ملفات الإعدادات والاتصال بقواعد البيانات واستغلال ثغرات تصعيد الصلاحيات في النواة (Kernel Exploits).
