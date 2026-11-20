# Task 02 - Types of Authentication Bypass (أنواع تجاوز آليات المصادقة)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Authentication bypass vulnerabilities can manifest in various forms, depending on how the application handles identity verification and session establishment.

The most common categories include:
1. Username Enumeration:
   Analyzing differences in server responses (such as error messages, HTTP status codes, or response times) to identify valid usernames registered on the platform.
2. Brute Force Attacks:
   Systematically attempting combinations of usernames and passwords until a valid pair is discovered. When threat actors use lists of leaked username/password combinations obtained from previous data breaches against multiple unrelated services, this is known as Credential Stuffing or Credential Reuse.
3. Logical Flaws:
   Exploiting errors in the application's underlying code or business workflow (e.g. flawed password recovery mechanisms, insecure parameter handling, or authentication flow skipping).
4. Cookie Manipulation:
   Tampering with client-side cookies or tokens when the server trusts client-provided identity attributes without cryptographic verification or server-side state checks.

Question:
- What is the name for the practice of reusing credentials recovered from one application against unrelated applications?
```

### 2. Arabic Pedagogical Translation
```text
يمكن أن تظهر ثغرات تجاوز المصادقة (Authentication Bypass) بأشكال وأنماط متعددة، اعتماداً على كيفية معالجة التطبيق لعمليات التحقق من الهوية وتأسيس الجلسات.

تشمل أبرز هذه الفئات ما يلي:
1. حصر وتعداد أسماء المستخدمين (Username Enumeration):
   تحليل الفروق الدقيقة في استجابات الخادم (مثل رسائل الخطأ المختلفة، أو رموز حالة HTTP، أو الفروق الزمنية في المعالجة Timing Differences) لتحديد أسماء المستخدمين الصالحة والمسجلة فعلياً في النظام.
2. هجمات التخمين الشامل (Brute Force Attacks):
   المحاولة المنظمة والمكثفة لآلاف التوليفات من أسماء المستخدمين وكلمات المرور حتى العثور على زوج صالح. عندما يستخدم المهاجمون قوائم مسربة من بيانات الاعتماد تم الحصول عليها من اختراقات سابقة لاختبارها ضد تطبيقات وخدمات أخرى غير مرتبطة، يُعرف هذا التكتيك بإعادة استخدام بيانات الاعتماد (Credential Reuse) أو حشو بيانات الاعتماد (Credential Stuffing).
3. العيوب المنطقية (Logical Flaws):
   استغلال أخطاء في الكود البرمجي الأساسي للتطبيق أو في تسلسل تدفق الأعمال (مثل آليات استعادة كلمة المرور المعيبة، أو المعالجة غير الآمنة للمُعاملات، أو تخطي خطوات المصادقة).
4. التلاعب بملفات تعريف الارتباط (Cookie Manipulation):
   التلاعب بملفات الارتباط أو الرموز المخزنة لدى العميل عندما يثق الخادم في بيانات الهوية المرسلة من المتصفح دون تحقق تشفيري أو مطابقة مع سجلات الخادم.

السؤال:
- ما هو الاسم الذي يطلق على ممارسة إعادة استخدام بيانات الاعتماد المستخرجة من تطبيق ما ضد تطبيقات أخرى غير مرتبطة؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The Anatomy of Credential Reuse / Stuffing
```
+-----------------------------------------------------------------------------------+
|                            CREDENTIAL REUSE CYCLE                                 |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  [Breach at Site A] ➔ Extracted Combo: "alice@example.com : Spring2024!"          |
|                                |                                                  |
|                                v                                                  |
|  [Underground Dump / Breach DB / Pastebin]                                        |
|                                |                                                  |
|                                v                                                  |
|  [Threat Actor / Botnet] ➔ Replays Combo against:                                 |
|         |--------> [Acme IT Support Portal]   (Success: Alice uses same password!)|
|         |--------> [Corporate Office 365]     (Success: Account Compromise!)      |
|         |--------> [Personal Banking / PayPal] (Blocked: MFA Active)              |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

### 2. Operational Tradecraft & Real-World Detection
* **المؤشرات الميدانية (IoCs):**
  - في سجلات الـ SIEM، يظهر هجوم الـ Credential Stuffing كآلاف محاولات تسجيل الدخول الفاشلة قادمة من مئات عناوين الـ IP المختلفة (عبر شبكات Tor أو Residential Proxies) بمعدل طلب واحد أو اثنين لكل IP لتجاوز آليات حظر الـ IP البسيطة.
* **الفرق الجوهري بين Brute Force و Credential Reuse:**
  - التخمين التقليدي (Brute Force): مهاجمة حساب مستخدم واحد بتجربة آلاف كلمات المرور من قائمة شائعة (RockYou).
  - حشو وإعادة استخدام البيانات (Credential Reuse / Stuffing): مهاجمة آلاف المستخدمين بتجربة كلمات المرور المسربة الخاصة بهم تحديداً من قواعد بيانات سابقة.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** ابحث في النص عن المصطلح الذي يصف استغلال عادة المستخدمين السيئة في استخدام نفس كلمة المرور عبر مواقع متعددة.
>
> **المستوى الثاني (توجيهي):** المصطلح يتكون من كلمتين باللغة الإنجليزية: "Credential ..." ويعني إعادة الاستخدام.
>
> **المستوى الثالث (الحل المباشر):** الإجابة هي `Credential Reuse`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Deep Technical Comparison of Bypass Mechanisms

| نوع الثغرة | آلية الحدوث في الكود | مثال تقني | الأثر المباشر |
| :--- | :--- | :--- | :--- |
| **Username Enumeration** | رسائل خطأ تفصيلية تكشف وجود الحساب | `"User not found"` مقابل `"Incorrect password"` | تقليص مساحة هجوم التخمين إلى 50% |
| **Credential Reuse** | ضعف الوعي الأمني وغياب التحقق متعدد العوامل | تجربة تسريبات RockYou / Combos | الاستيلاء التلقائي على حسابات الموظفين |
| **Logic Flaws** | افتراضات خاطئة في تدفق البيانات | فحص الترويسة في جهة وتوجيه البريد لجهة أخرى | تجاوز المصادقة بضغطة زر واحدة |
| **Cookie Manipulation** | الاعتماد على ثقة العميل (Client-Trust) | `Cookie: admin=1` | تصعيد الصلاحيات الفوري إلى Root/Admin |

### 2. Why Credential Reuse Remains a Top Threat
وفقاً لتقارير Verizon Data Breach Investigations Report (DBIR)، فإن أكثر من 80% من حوادث خرق تطبيقات الويب تنطوي على استخدام بيانات اعتماد مسروقة أو متوقعة. يميل أكثر من 65% من المستخدمين إلى إعادة استخدام كلمة المرور نفسها (أو تعديلات طفيفة عليها) عبر حساباتهم الشخصية والمهنية.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | التفسير الأمني الدقيق |
| :--- | :--- | :--- |
| **What is the name for the practice of reusing credentials recovered from one application against unrelated applications?** | `Credential Reuse` | ممارسة إعادة استخدام بيانات الاعتماد المستخرجة من موقع ضد مواقع وخدمات أخرى للاستفادة من تكرار كلمات المرور. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابة واعتمادها في نظام منصة TryHackMe بنسبة نجاح 100%.
* تم توثيق المصطلح ومطابقته لتصنيفات إطار عمل MITRE ATT&CK للتقنية `T1110.004 - Credential Stuffing`.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **كلمات المرور المعقدة لا تحمي ضد إعادة الاستخدام:** لو كانت كلمة مرور المستخدم تحتوي على 20 حرفاً ورمزاً معقداً، ولكن تم تسريبها من موقع تسوق ضعيف، فإن المهاجم سيستخدمها بنجاح ضد حسابه المصرفي أو بريد عمله طالما لم يتم تفعيل المصادقة الثنائية (MFA).
2. **المعالجة التنظيمية والهندسية:**
   - فرض المصادقة متعددة العوامل (MFA / 2FA) كشرط إلزامي لجميع الحسابات.
   - فحص كلمات المرور عند تعيينها مقابل قواعد بيانات الاختراقات المعروفة (مثل واجهة API لـ HaveIBeenPwned).
