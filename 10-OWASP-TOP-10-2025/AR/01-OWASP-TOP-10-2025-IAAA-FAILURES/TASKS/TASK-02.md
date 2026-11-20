# Task 02 · What is IAAA? (تفكيك عناصر نموذج IAAA)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
IAAA is a fundamental conceptual model for verifying users and their actions within software applications. Each pillar plays an essential role, and it is impossible to skip a level:
- Identity: The unique identifier (e.g., username, user ID, email address) that distinguishes an actor or service.
- Authentication: Proving that claimed identity (via passwords, multi-factor OTP tokens, biometrics, or passkeys).
- Authorisation: Determining what the authenticated identity is permitted to access or execute.
- Accountability: Maintaining immutable audit logs and alerting systems detailing who performed which action, when, and from where.

Weaknesses in any pillar permit threat actors to bypass controls, escalate privileges, or access other users' data without attribution.

Question:
- What does IAAA stand for?
```

### 2. Arabic Pedagogical Translation
```text
يُمثل نموذج IAAA إطاراً مفاهيمياً أساسياً للتحقق من هوية المستخدمين وتصرفاتهم داخل التطبيقات البرمجية. يلعب كل عنصر من العناصر الأربعة دوراً محورياً، ولا يمكن من الناحية الهندسية تجاوز أي مستوى من المستويات:
- الهوية (Identity): المعرّف الفريد (مثل اسم المستخدم، أو رقم المعرّف الرقمي، أو البريد الإلكتروني) الذي يمثل الشخص أو الخدمة.
- المصادقة (Authentication): إثبات صحة تلك الهوية المدعاة (عبر كلمات المرور، أو رموز OTP الثنائية، أو السمات الحيوية، أو مفاتيح المرور Passkeys).
- التفويض (Authorisation): تحديد ما يُسمح لتلك الهوية المؤكدة بالوصول إليه أو تنفيذه من عمليات.
- المساءلة (Accountability): التوثيق غير القابل للتلاعب وتنبيهات المراقبة التي تسجل "من قام بماذا، ومتى، ومن أي مكان".

تؤدي نقاط الضعف في أي ركيزة من هذه الركائز إلى تمكين المهاجمين من تجاوز ضوابط الوصول، أو تصعيد الصلاحيات، أو سرقة بيانات المستخدمين دون إمكانية تعقبهم جنائياً.

السؤال:
- إلى ماذا يرمز الاختصار IAAA؟ (اكتب الكلمات الأربع باللغة الإنجليزية مفصولة بفواصل)
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:**
  - What does IAAA stand for?
- **Pedagogical Goal:** Master the precise terminology and English spelling of the four IAAA security pillars, recognizing their interdependent operational sequence.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. The Architectural Strictness of IAAA
- Note the British English spelling conventions used in TryHackMe curriculum: `Authorisation` with an `s` instead of the American `z`.
- An application that logs IP addresses without tying them to an authenticated identity fails Accountability.
- An application that authenticates users but lets them view arbitrary account IDs via parameter tampering fails Authorisation.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Spelling and Formatting
> الكلمات الأربع المطلوبة بالترتيب: الهوية، المصادقة، التفويض (بالهجاء الإنجليزي بالـ s)، المساءلة:
> `Identity, Authentication, Authorisation, Accountability`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### 1. The Four Pillars Defined
```text
I ──> Identity       (Who are you claiming to be?)
A ──> Authentication (Can you prove you own that identity?)
A ──> Authorisation  (What resources are you permitted to view/modify?)
A ──> Accountability (Can we reconstruct a tamper-evident audit trail of your actions?)
```

---

## Stage E: Final Answer & Key Values (الإجابة النهائية)

- **Question:** What does IAAA stand for?
- **Verified Value:** `Identity, Authentication, Authorisation, Accountability`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

### Verification
String submitted and verified with 100% precision on the platform.

---

## Stage G: Key Educational Takeaway (الدروس المستفادة والخبرة العملية)

1. **Non-Repudiation Depends on Accountability:** Without robust logging and alerting, an application cannot enforce legal non-repudiation during incident response.
