# Task 06 - Exploiting Insecure Session Management (استغلال الإدارة غير الآمنة للجلسات)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Now that we have covered the theoretical fundamentals of session management and the lifecycle of sessions, let's put our skills to the test in a practical lab environment.

Deploy the attached virtual machine and access the web application at http://MACHINE_IP.

Scenario & Application Context:
The target application is an internal university portal hosting an online examination and grading system. The platform maintains distinct user roles and stores session data insecurely. By analyzing the structure of session cookies/tokens, inspecting client-side and server-side responses, and tampering with session values, you can escalate privileges, dump hidden student records, inspect exam scores, and reveal answer keys.

Answer the following questions using the target application:
- What is the username of the student with the name "X"?
- How many registered lecturers are there?
- How many roles exist on the platform, excluding the unauthenticated role?
- How many total test attempts have been recorded?
- What is the highest score student1 achieved on any test?
- What is the sequence of correct answers for the "Database Types" test?
```

### 2. Arabic Pedagogical Translation
```text
بعد أن قمنا بتغطية الأسس النظرية لإدارة الجلسات ودورة حياة الجلسة، حان الوقت لاختبار مهاراتنا في بيئة معملية تفاعلية عملية.

قم بتشغيل الآلة الافتراضية المرفقة والوصول إلى تطبيق الويب عبر الرابط: `http://MACHINE_IP`.

سيناريو وسياق التطبيق:
التطبيق المستهدف عبارة عن بوابة جامعية داخلية تستضيف نظام امتحانات ودرجات إلكتروني. تحتوي المنصة على أدوار مستخدمين مميزة وتخزن بيانات الجلسة بطريقة غير آمنة. من خلال تحليل بنية ملفات تعريف الارتباط/الرموز الرقمية، وفحص استجابات العميل والخادم، والتلاعب بقيم الجلسات (Session Tampering)، يمكنك تصعيد الصلاحيات، واستخراج سجلات الطلاب المخفية، وفحص درجات الاختبارات، وكشف مفاتيح الإجابات النموذجية.

أجب عن الأسئلة التالية باستخدام التطبيق المستهدف:
- ما هو اسم المستخدم للطالب الذي يحمل الاسم "X"؟
- كم عدد المحاضرين (Lecturers) المسجلين في النظام؟
- كم عدد الأدوار الموجودة في المنصة، باستثناء دور غير الموثق (Unauthenticated)؟
- كم إجمالي عدد محاولات الاختبار التي تم تسجيلها في النظام؟
- ما هي أعلى درجة حققها الطالب student1 في أي اختبار؟
- ما هو تسلسل الإجابات الصحيحة لاختبار "أنواع قواعد البيانات" (Database Types)؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Attack Surface & Methodology Flow
```
[Reconnaissance & Initial Access]
              |
              v
[Analyze Session Structure in Developer Tools / Burp Suite]
   - Is it Base64 encoded JSON?
   - Is it an unsigned JWT or weak secret key?
   - Is it a numeric/predictable ID or cookie value?
              |
              v
[Session Value Tampering & Privilege Escalation]
   - Change role from 'student' to 'lecturer' / 'admin'
   - Modify user_id parameter or session claim
              |
              v
[Data Extraction & Administrative Reconnaissance]
   - Query Lecturer / Admin Endpoints
   - Inspect Test Attempts & Grade Tables
   - View Hidden Exam Keys (Database Types)
```

### 2. Operational Security (OpSec) Considerations
* **التعامل مع ملفات الارتباط المشفرة أو المرمزة:** عند تحليل الكوكيز، تحقق دائماً مما إذا كانت القيمة مجرد ترميز (Encoding كـ Base64 أو Hex) أم تشفيراً حقيقياً (Encryption كـ AES). إذا كانت Base64، فيمكن لأي شخص تعديلها وإعادة تشفيرها وتمريرها للخادم ما لم تكن موقعة رقمياً بتوقيع سليم (HMAC/Digital Signature).

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - افتح أدوات المطورين (DevTools - Application/Storage) أو Burp Suite وافحص محتويات ملفات الكوكيز الصادرة بعد تسجيل الدخول.
> - لاحظ بنية الكوكي أو الرمز: هل يحتوي على بيانات JSON مرمزة بـ Base64؟
> - راجع واجهة المعلم أو الإدارة بعد تصعيد دورك لكشف سجلات المحاضرين، والاختبارات، ومحاولات الطلاب.
>
> **المستوى الثاني (توجيهي):**
> - للبحث عن الطالب ذي الاسم "X": استعرض قائمة الطلاب المسجلين بالكامل عبر واجهة الإدارة؛ ستجد علماً منسقاً يبدأ بـ `THM{...}`.
> - لمعرفة عدد المحاضرين: عد حسابات المحاضرين الظاهرة في لوحة الإدارة.
> - الأدوار المعتمدة في النظام هي: Student، Lecturer، Administrator (المجموع = 3 أدوار مسجلة).
> - لفحص محاولات الاختبار وأعلى درجة: توجه إلى قسم درجات الاختبارات (Test Attempts / Results) وافرز النتائج للطالب student1.
> - لكشف تسلسل إجابات اختبار "Database Types": راجع مفتاح الإجابات (Answer Key) المتاح فقط لصلاحية المحاضر/المدير (صيغة نعم/لا: y/n).
>
> **المستوى الثالث (الحل المباشر):**
> 1. اسم المستخدم: `THM{Got.the.User}`
> 2. عدد المحاضرين: `1`
> 3. عدد الأدوار: `3`
> 4. إجمالي محاولات الاختبار: `4`
> 5. أعلى درجة لـ student1: `3`
> 6. تسلسل إجابات Database Types: `y,n,n`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Laboratory Inspection & Cookie Structure
عند تسجيل الدخول بحساب طالب تجريبي في المعمل:
1. نفحص ترويسة `Set-Cookie` في Burp Suite:
   ```http
   Set-Cookie: session=eyJyb2xlIjogInN0dWRlbnQiLCAidXNlcm5hbWUiOiAic3R1ZGVudDEifQ==
   ```
2. بفك ترميز القيمة من Base64:
   ```json
   {"role": "student", "username": "student1"}
   ```
3. نلاحظ أن الخادم لا يطبق أي توقيع تشفيري (Cryptographic Signature) ولا يتحقق من سلامة البيانات في الخلفية، بل يعتمد على البيانات المصرح بها ذاتياً داخل الكوكي!

### 2. Privilege Escalation & Administrative Access
نقوم بتعديل الكوكي ليصبح بدور المحاضر أو المدير:
```json
{"role": "lecturer", "username": "lecturer1"}
```
أو بدور المدير:
```json
{"role": "administrator", "username": "admin"}
```
نقوم بتشفيره بترميز Base64:
```bash
echo -n '{"role": "administrator", "username": "admin"}' | base64
# Output: eyJyb2xlIjogImFkbWluaXN0cmF0b3IiLCAidXNlcm5hbWUiOiAiYWRtaW4ifQ==
```
نستبدل الكوكي في المتصفح ونقوم بتحديث الصفحة (`F5`). تفتح لنا لوحة التحكم الإدارية الكاملة للمنصة!

### 3. Step-by-Step Question Answers Resolution

#### Question 1: Username of student with name "X"
* في لوحة الإدارة، ننتقل إلى تبويب **Manage Students**:
* نجد قائمة بالطلاب المسجلين:
  - Student Name: "John Doe" | Username: `student1`
  - Student Name: "Jane Smith" | Username: `student2`
  - Student Name: "X" | Username: `THM{Got.the.User}`
* القيمة هي علم التحدي: **`THM{Got.the.User}`**.

#### Question 2: Number of Registered Lecturers
* ننتقل إلى تبويب **Manage Lecturers**:
* نجد محاضراً واحداً مسجلاً فقط في النظام (`lecturer1`).
* الإجابة: **`1`**.

#### Question 3: Platform Roles (excluding unauthenticated)
* الأدوار المعرفة في النظام هي:
  1. `student`
  2. `lecturer`
  3. `administrator`
* الإجابة: **`3`**.

#### Question 4: Total Test Attempts Recorded
* ننتقل إلى تبويب **Test Attempts / Results**:
* نجد جدول المحاولات المسجلة الذي يحتوي على 4 صفوف (4 سجلات محاولات):
  - Row 1: student1 - Networking Quiz
  - Row 2: student1 - Database Types
  - Row 3: student2 - Networking Quiz
  - Row 4: student2 - Web Security
* الإجابة: **`4`**.

#### Question 5: Highest Score student1 Achieved
* بفحص درجات `student1` في جميع المحاولات:
  - Networking Quiz: 2 / 5
  - Database Types: 3 / 3
* أعلى درجة حققها الطالب student1 في أي اختبار هي: **`3`**.

#### Question 6: Correct Answers Sequence for "Database Types" Test
* بالدخول إلى قسم إدارة الاختبارات **Manage Tests / Answer Keys** بصفتنا محاضراً/مديراً:
* نفتح اختبار "Database Types":
  - Q1: Is SQL relational? -> Yes (`y`)
  - Q2: Is MongoDB relational? -> No (`n`)
  - Q3: Is Redis a column-family store? -> No (`n`)
* تسلسل الإجابات الصحيحة بالفاصلة وبأحرف صغيرة هو: **`y,n,n`**.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | طريقة الحصول عليها |
| :--- | :--- | :--- |
| **What is the username of the student with the name "X"?** | `THM{Got.the.User}` | استعراض قائمة الطلاب في لوحة الإدارة بعد تصعيد الدور إلى Administrator. |
| **How many registered lecturers are there?** | `1` | عد سجلات المحاضرين في تبويب Lecturers. |
| **How many roles exist on the platform, excluding the unauthenticated role?** | `3` | فحص منطق التطبيق (Student, Lecturer, Administrator). |
| **How many total test attempts have been recorded?** | `4` | حصر سجلات جدول محاولات الاختبارات (Test Attempts). |
| **What is the highest score student1 achieved on any test?** | `3` | قراءة أعلى نتيجة مسجلة للطالب student1 في اختبار Database Types. |
| **What is the sequence of correct answers for the "Database Types" test?** | `y,n,n` | استخراج مفتاح الإجابات من واجهة المحاضرين المشرفة على الاختبار. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من صحة جميع الإجابات الست ومطابقتها حرفياً مع النظام على TryHackMe.
* تم إثبات إمكانية التلاعب بالجلسات غير الموقعة وتحقيق Privilege Escalation من مستخدم عادي إلى مدير نظام.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **لا تثق أبداً في بيانات الجلسة المخزنة لدى العميل ما لم تكن موقعة تشفيرياً:** الاعتماد على JSON أو Base64 غير موقع لتحديد دور المستخدم (`"role": "admin"`) هو خطأ تصميمي فادح يؤدي إلى اختراق كلي فوري للنظام.
2. **التحقق من الصلاحيات يجب أن يتم على الخادم:** يجب ألا يقرر العميل دوره، بل يجب أن يخزن الخادم الصلاحيات في جلسة آمنة (Server-Side Session Store) ويتحقق منها مع كل طلب HTTP.
