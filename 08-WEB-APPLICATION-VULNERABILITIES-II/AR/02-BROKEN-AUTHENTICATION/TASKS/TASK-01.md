# Task 01 - Introduction (المقدمة ومدخل كسر آليات المصادقة)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Welcome to the Broken Authentication room!

Authentication is the process of verifying that a user is who they claim to be. In web applications, this typically involves checking credentials (such as a username and password combination) against a database and establishing an authenticated session.

An Authentication Bypass occurs when an attacker can access restricted areas or perform actions reserved for authenticated users without possessing valid credentials. This vulnerability represents a critical breakdown in the application's security posture, often leading to full account takeover or administrative compromise.

In this room, you will learn how to:
- Identify and exploit username enumeration vulnerabilities using response differentials.
- Brute force login forms using high-speed fuzzing tools like ffuf.
- Exploit business logic flaws and HTTP Parameter Pollution in password reset functionality.
- Inspect and manipulate client-side cookies (plain text, hashed, and Base64-encoded).

Deploy the target machine attached to this task. The web application represents the "Acme IT Support" portal and will be available at http://MACHINE_IP.
```

### 2. Arabic Pedagogical Translation
```text
مرحباً بكم في غرفة كسر آليات المصادقة (Broken Authentication)!

المصادقة (Authentication) هي العملية التقنية المسؤولة عن التحقق من أن المستخدم هو بالفعل صاحب الهوية التي يدعيها. في تطبيقات الويب، يتضمن ذلك عادةً التحقق من بيانات الاعتماد (مثل ثنائية اسم المستخدم وكلمة المرور) بمطابقتها مع قاعدة البيانات وتأسيس جلسة عمل موثقة (Authenticated Session).

يحدث تجاوز المصادقة (Authentication Bypass) عندما يتمكن المهاجم من الوصول إلى أقسام مقيدة أو تنفيذ إجراءات مخصصة للمستخدمين المصرح لهم دون امتلاك بيانات اعتماد صالحة. تمثل هذه الثغرة انهياراً خطيراً في المنظومة الأمنية للتطبيق، وغالباً ما تؤدي إلى الاستيلاء الكامل على الحسابات (Account Takeover) أو السيطرة الإدارية الشاملة على النظام.

في هذه الغرفة، ستتعلم كيفية:
- اكتشاف واستغلال ثغرات حصر أسماء المستخدمين (Username Enumeration) عبر استغلال الفوارق في استجابات الخادم.
- كسر نماذج تسجيل الدخول بالتخمين عالي السرعة (Brute Force) باستخدام أداة ffuf.
- استغلال العيوب المنطقية (Business Logic Flaws) وتلويث معلمات بروتوكول HTTP (Parameter Pollution) في وظيفة استعادة كلمة المرور.
- فحص والتلاعب بملفات تعريف الارتباط الخاصة بالعميل (سواء كانت نصاً صريحاً، أو تجزئة مشفرة Hashed، أو مرمزة بـ Base64).

قم بتشغيل الآلة المستهدفة المرفقة بهذه المهمة؛ يمثل التطبيق بوابة "Acme IT Support" وسيكون متاحاً عبر الرابط: http://MACHINE_IP.
```

### 3. Core Objectives & Prerequisites
* استيعاب الفارق بين فشل المصادقة (Authentication Failure) وتجاوز المصادقة (Authentication Bypass).
* فهم بيئة الاختبار المستهدفة لبوابة الدعم الفني لشركة Acme IT Support.
* إعداد الأدوات اللازمة للمعمل: `ffuf`, `curl`, `Burp Suite`, وقوائم `SecLists`.

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Conceptual Attack Landscape
```
                     +---------------------------------------+
                     |      AUTHENTICATION BYPASS MODES      |
                     +---------------------------------------+
                                         |
         +-------------------------------+-------------------------------+
         |                               |                               |
         v                               v                               v
[Information Leakage]          [Credential Guessing]           [Logic & State Flaws]
- Username Enumeration         - Password Brute Force          - Parameter Pollution
- Differential Responses       - Multi-wordlist Fuzzing        - Cookie Tampering
- Timing Attacks               - Weak Lockout Policies         - Token Forgery
```

### 2. Operational Security (OpSec) & Pentesting Guardrails
* **في العمليات الحقيقية الميدانية:**
  - هجمات التخمين (Brute-force) تُعد من أكثر الأنشطة صخباً وكشفاً في سجلات الـ SOC وأنظمة WAF.
  - يجب مراعاة عتبات قفل الحسابات (Account Lockout Thresholds) لتجنب حرمان الموظفين الحقيقيين من العمل (Denial of Service).
  - عند اكتشاف ثغرة تلاعب بالكوكي (Cookie Tampering)، لا تقم بإجراء تغييرات تدميرية على حسابات الإنتاج، بل التزم بإثبات الأثر (PoC) على حسابات الاختبار المخصصة.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** المهمة الأولى هي نقطة البداية وتشغيل بيئة المعمل الافتراضية، ولا تحتوي على أسئلة تقنية أو أعلام.
>
> **المستوى الثاني (توجيهي):** اضغط على زر "Start Machine" للحصول على عنوان الـ IP المستهدف، ثم اقرأ الأهداف العامة للغرفة.
>
> **المستوى الثالث (الحل المباشر):** انقر على زر `Completed` للانتقال مباشرة إلى المهمة الثانية.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Target Infrastructure Overview
* **نظام التشغيل:** Linux Server يستضيف خادم ويب Apache / PHP.
* **البوابة المستهدفة:** `http://MACHINE_IP/`
  - `/customers/signup` ➔ بوابة تسجيل العملاء الجدد (تحتوي على تسريب أسماء المستخدمين).
  - `/customers/login` ➔ بوابة الدخول (مفتوحة للتخمين وتفتقر لمعدل الطلبات المحدود).
  - `/customers/reset` ➔ بوابة إعادة تعيين كلمة المرور (تحتوي على ثغرة تلويث المعلمات HTTP Parameter Pollution).
  - `/cookie-test` ➔ معمل اختبار التلاعب بملفات الارتباط.

### 2. Verification Command
قبل البدء في المهام، نتحقق من اتصال الشبكة بالآلة الافتراضية عبر المنفذ 80:
```bash
curl -I http://MACHINE_IP/
```
الاستجابة المتوقعة: `HTTP/1.1 200 OK` مع ظهور ترويسات الخادم والبوابة.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال / المتطلب | الإجابة النموذجية |
| :--- | :--- |
| **Deploy the target machine and read the introduction.** | `No answer needed` (تم التشغيل وإكمال القراءة) |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من تشغيل الآلة والتأكد من إمكانية الوصول إلى خدمة الويب على المنفذ 80.
* تم إنجاز المهمة بنجاح على منصة TryHackMe بنسبة 100%.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **المصادقة هي خط الدفاع الأول:** إذا سقطت آلية المصادقة أو أمكن تجاوزها، فإن جميع طبقات الحماية اللاحقة والتفويض تفقد قيمتها، لأن المهاجم يتحرك بهوية شرعية موثقة داخل النظام.
2. **التكامل بين الثغرات:** تجاوز المصادقة نادراً ما يكون خطوة مفردة، بل هو غالباً سلسلة تبدأ بجمع المعلومات وحصر المستخدمين (Enumeration) مروراً بالتخمين (Brute-Force) أو التلاعب بالمنطق وصولاً إلى الاستيلاء التام (Account Takeover).
