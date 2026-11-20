# Task 04 · A07: Authentication Failures (إخفاقات المصادقة وارتباك الحسابات Account Confusion)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Authentication Failures occur when an application cannot reliably verify or securely bind a user's claimed identity.

Common Vulnerability Classes:
- Username enumeration via verbose error messages.
- Weak password policies and absence of rate limiting / brute-force protection.
- Logic flaws in the registration or password reset workflows.
- Insecure session token generation and session fixation.

Account Confusion Attack Scenario:
A subtle yet critical logic flaw involves case-sensitivity discrepancies between the registration module and authentication/database layers:
- An existing administrative account is registered as `admin`.
- An attacker registers a new account using the mixed-case variant `aDmiN`.
- If the application normalizes or collates usernames inconsistently (e.g., checking uniqueness with case-sensitive queries but querying dashboards case-insensitively), the attacker's newly registered user becomes bound to the privileged administrator profile!

Interactive Lab Challenge:
- Launch the static site attached to this task.
- Register a new account with the username `aDmiN`.
- Log in and inspect the resulting dashboard.
- Retrieve the flag displayed on the administrator's dashboard.

Question:
- What is the flag on the `admin` user's dashboard?
```

### 2. Arabic Pedagogical Translation
```text
تحدث إخفاقات المصادقة (Authentication Failures) عندما يعجز التطبيق عن التحقق الموثوق من هوية المستخدم أو عندما يفشل في ربط جلسات العمل (Sessions) بالحسابات الصحيحة بصورة آمنة.

أبرز فئات الثغرات في هذا المجال:
- حصر وتعداد أسماء المستخدمين (Username Enumeration) عبر رسائل الخطأ التفصيلية.
- سياسات كلمات المرور الضعيفة وغياب آليات تقييد المعدل (Rate Limiting) أو الحظر المؤقت بعد المحاولات المتكررة.
- العيوب المنطقية في مسارات التسجيل واستعادة كلمات المرور.
- التوليد غير الآمن لرموز الجلسات وثغرات تثبيت الجلسة (Session Fixation).

سيناريو هجوم ارتباك الحسابات (Account Confusion):
من العيوب المنطقية الدقيقة عدم التطابق في معالجة حالة الأحرف الكبيرة والصغيرة (Case-Sensitivity) بين وحدات التسجيل وقواعد البيانات:
- يمتلك النظام حساباً إدارياً رئيسياً باسم `admin`.
- يقوم المهاجم بإنشاء حساب جديد باستخدام نفس الاسم ولكن بأحرف مختلطة: `aDmiN`.
- إذا كانت قاعدة البيانات تتحقق من عدم التكرار بحساسية للأحرف (Case-Sensitive) بينما تستعلم لوحة التحكم بدون حساسية للأحرف (Case-Insensitive Collation)، فإن جلسة المستخدم الجديد ترتبط خطأً ببيانات وصلاحيات المدير الفعلي!

تحدي المختبر التفاعلي:
- شغل الموقع التفاعلي المرفق بالمهمة.
- سجل حساباً جديداً باسم المستخدم: `aDmiN`.
- سجل الدخول وافحص لوحة التحكم الناتجة.
- استخرج الراية المعروضة على لوحة تحكم المستخدم admin.

السؤال:
- ما هي قيمة الراية (Flag) الظاهرة في لوحة تحكم المستخدم `admin`؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:**
  - What is the flag on the `admin` user's dashboard?
- **Pedagogical Goal:** Understand account confusion flaws stemming from collation mismatches, exploit registration logic flaws using mixed-case strings (`aDmiN`), and recover privileged account flags.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Collation and Canonicalization Flaws
In relational database systems (such as MySQL or SQL Server):
- The default collation `utf8mb4_general_ci` is Case-Insensitive (`_ci`).
- If the application code executes:
  `if (user_exists_in_redis("aDmiN"))` (which is case-sensitive, returning FALSE),
  it permits the registration to proceed.
- When the database saves or queries the session:
  `SELECT * FROM users WHERE username = 'aDmiN';`
  the database matches the row for `admin`, binding the attacker's session to the root administrator account.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Registering the Account
> توجه إلى صفحة التسجيل (Register) وأدخل اسم المستخدم `aDmiN` مع أي كلمة مرور. بعد التسجيل بنجاح، توجه لصفحة تسجيل الدخول (Login) وسجل الدخول بنفس الاسم. ستفتح لك لوحة تحكم المدير وتظهر الراية `THM{Account.confusion.FTW!}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### 1. Attack Steps
1. Navigate to `/register`.
2. Input:
   - Username: `aDmiN`
   - Password: `Password123!`
3. Submit registration form.
4. Navigate to `/login` and authenticate with `aDmiN` / `Password123!`.
5. The application dashboard loads as `Welcome, admin!` displaying the flag:
   `THM{Account.confusion.FTW!}`

---

## Stage E: Final Answer & Key Values (الإجابة النهائية)

- **Question:** What is the flag on the `admin` user's dashboard?
- **Verified Value:** `THM{Account.confusion.FTW!}`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

### Verification
Flag submitted and validated against room requirements.

---

## Stage G: Key Educational Takeaway (الدروس المستفادة والخبرة العملية)

1. **Enforce Canonicalization Before Processing:** Always normalize usernames to a single canonical format (e.g. `strtolower(trim($username))`) before performing uniqueness checks, password validation, or database queries.
