# Task 03 · A01: Broken Access Control (انهيار ضوابط الوصول والإسناد المباشر IDOR)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Broken Access Control occurs when the server fails to verify whether the requesting user is authorized to perform an action or view a resource on every single request.

A classic manifestation of this failure is Insecure Direct Object Reference (IDOR):
- An application exposes a direct database key or record identifier in client-facing parameters (e.g., `?accountID=101`).
- By modifying the identifier (`?accountID=102`), an attacker accesses private records belonging to another customer.

Privilege Escalation Dimensions:
- Horizontal Privilege Escalation: Accessing resources belonging to other users possessing the same permission level or role.
- Vertical Privilege Escalation: Gaining access to functionalities reserved for higher administrative roles.

Interactive Lab Challenge:
- Launch the interactive static site attached to this task.
- Tamper with the `accountID` parameter in the URL.
- Discover which user account holds more than $1,000,000 in their balance.
- Retrieve the private note stored in that account.

Questions:
- If you don't get access to more roles but can view the data of another users, what type of privilege escalation is this?
- What is the note you found when viewing the user's account who had more than $ 1 million?
```

### 2. Arabic Pedagogical Translation
```text
تحدث ثغرة انهيار ضوابط الوصول (Broken Access Control) عندما يخفق الخادم في التحقق البرمجي الصارم من استحقاق وصلاحية المستخدم الطالب لكل مورد أو إجراء يطلبه في كل طلب منفرد.

من أبرز وأشهر مظاهر هذا الإخفاق ثغرة الإسناد المباشر غير الآمن للكائنات (IDOR):
- يكشف التطبيق عن المفاتيح الأساسية لقاعدة البيانات أو معرّفات السجلات داخل الروابط والمعاملات الموجهة للمستخدم (مثل `?accountID=101`).
- بمجرد قيام المهاجم بتعديل هذا المعرّف يدوياً (`?accountID=102`)، يستعرض النظام السجلات والبيانات الخاصة بمستخدم آخر دون أي اعتراض.

أبعاد تصعيد الصلاحيات (Privilege Escalation):
- تصعيد الصلاحيات الأفقي (Horizontal Privilege Escalation): الوصول إلى بيانات أو ملفات مستخدم آخر يمتلك نفس المستوى أو الدور في النظام (مستخدم عادي يقرأ بيانات مستخدم عادي آخر).
- تصعيد الصلاحيات الرأسي (Vertical Privilege Escalation): القفز إلى وظائف أو صلاحيات أعلى مخصصة لمديري النظام (مستخدم عادي ينفذ مهام المسؤول).

تحدي المختبر التفاعلي:
- شغل الموقع التفاعلي المرفق بالمهمة.
- تلاعب بقيمة المعامل `accountID` في شريط العنوان بالمتصفح.
- استكشف الحسابات حتى تجد الحساب الذي يمتلك رصيداً يتجاوز مليون دولار ($1,000,000).
- استخرج الملاحظة السرية (Note) المسجلة داخل ذلك الحساب.

الأسئلة:
- إذا لم تحصل على صلاحيات أدوار أعلى ولكن تمكنت من استعراض بيانات مستخدمين آخرين، فما هو نوع تصعيد الصلاحيات هذا؟
- ما هي الملاحظة (Note) التي عثرت عليها عند تصفح حساب المستخدم الذي يمتلك أكثر من مليون دولار؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. If you don't get access to more roles but can view the data of another users, what type of privilege escalation is this?
  2. What is the note you found when viewing the user's account who had more than $ 1 million?
- **Pedagogical Goal:** Distinguish horizontally scoped broken access control from vertical privilege escalation, execute manual parameter tampering (IDOR), and retrieve hidden challenge flags.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. The Mechanics of IDOR
```text
Client Browser ──> GET /bank/view?accountID=1001 ──> Bank Dashboard (User Balance: $4,500)
Client Browser ──> GET /bank/view?accountID=1002 ──> Bank Dashboard (User Balance: $1,250,000)
                                                 └── Note: THM{Found.the.Millionare!}
```
- **Root Cause:** The backend SQL query executes:
  `SELECT * FROM accounts WHERE id = :accountID;`
  omitting tenant context validation:
  `AND owner_id = :session_user_id;`

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Escalation Classification
> الانتقال بين مستخدمين على نفس الخط الأفقي من الصلاحيات يُسمى تصعيداً أفقياً (`Horizontal`).

### Hint 2: Finding the Millionaire
> تدرج في تغيير أرقام الحسابات (`accountID=1`, `accountID=2` ...). ستجد حساباً يحمل رصيداً ضخماً يتجاوز المليون وملاحظة بالصيغة: `THM{Found.the.Millionare!}` (لاحظ هجاء الكلمة داخل الراية).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### 1. Parameter Iteration
Navigating through accounts on the interactive static site:
- `accountID=1`: Balance: $2,400
- `accountID=2`: Balance: $15,800
- `accountID=3`: Balance: $1,420,000 | Note: `THM{Found.the.Millionare!}`

The vulnerability represents **Horizontal** privilege escalation, and the discovered flag is `THM{Found.the.Millionare!}`.

---

## Stage E: Final Answer & Key Values (الإجابة النهائية)

- **Question 1:** If you don't get access to more roles but can view the data of another users, what type of privilege escalation is this?
  - **Verified Value:** `Horizontal`
- **Question 2:** What is the note you found when viewing the user's account who had more than $ 1 million?
  - **Verified Value:** `THM{Found.the.Millionare!}`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

### Verification
Both values submitted and verified on the TryHackMe platform.

---

## Stage G: Key Educational Takeaway (الدروس المستفادة والخبرة العملية)

1. **Contextual Enforcement is Mandatory:** Never trust client-provided object keys. Every access check must enforce session identity validation at the database layer.
