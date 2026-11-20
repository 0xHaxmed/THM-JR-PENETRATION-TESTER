# Task 04: What is SQL Injection? (ما هو حقن SQL ومحدد إنهاء العبارات البرمجية؟)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
SQL Injection (SQLi) occurs when an application takes user-supplied input and directly concatenates it into a database query string without proper sanitization, filtering, or parameterization. Because the database engine cannot distinguish between the developer's intended SQL commands and the untrusted user input, an attacker can supply syntactically meaningful characters to break out of data contexts and enter execution contexts.

#### The Syntax-Breaking Mechanics:
Consider a PHP script querying user profiles:
```php
$id = $_GET['id'];
$query = "SELECT * FROM users WHERE id = " . $id . ";";
```
If a normal user requests `?id=1`, the query executes as:
`SELECT * FROM users WHERE id = 1;`

If an attacker supplies `1 OR 1=1`, the query transforms into:
`SELECT * FROM users WHERE id = 1 OR 1=1;`
The database evaluates `1=1` as always true, returning every user record in the table!

#### Critical Delimiters and Syntax Symbols:
- **Quotes (`'`, `"`):** Used to escape string literals.
- **Semicolon (`;`):** Signifies the **end of an SQL statement**. In engines that support stacked queries (such as MSSQL or PostgreSQL), an attacker can terminate the original query and append a completely new command:
  `1; DROP TABLE users;--`
- **Comment Delimiters (`-- `, `#`, `/* */`):** Instructs the database parser to ignore the remainder of the original query, neutralizing trailing syntax errors.

#### الأسئلة المطروحة (Questions):
1. What character signifies the end of an SQL statement? (ما هو المحرف الذي يشير إلى نهاية عبارة SQL البرمجية؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المسار المنطقي لكسر السياق (Context Escape):**
  * جوهر ثغرات الحقن هو الخلط بين "البيانات" (Data) و"الشفرة البرمجية" (Code).
  * محرف الفاصلة المنقوطة (`;`) يمثل فاصلاً تنفيذياً؛ فإذا سمح محرك قاعدة البيانات بتنفيذ استعلامات مكدسة (Stacked Queries)، يمكن للمهاجم تحويل استعلام قراءة بسيط (`SELECT`) إلى استعلام حذف شامل (`DELETE` أو `DROP`) أو حقن مستخدم إداري جديد عبر `INSERT`.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * الرمز هو علامة ترقيم برمجية شائعة لإنهاء الأسطر في لغات C و Java و SQL.
2. **التلميح الثاني (المستوى العملي):**
   * الفاصلة المنقوطة.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الرمز المطلوب هو: `;`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشريح كسر الاستعلام عبر الفاصلة المنقوطة والتعليق:
```text
Original Query Template:
SELECT * FROM products WHERE category = '[USER_INPUT]' AND active = 1;

Attacker Injected Input:
electronics'; DROP TABLE products;--

Final Executed SQL String:
SELECT * FROM products WHERE category = 'electronics'; DROP TABLE products;-- ' AND active = 1;
└──────────────────┬────────────────────────────────┘ └──────────┬────────┘ └──────────┬─────────┘
        Query 1 (Executed)                           Query 2 (Executed)    Commented Out (Ignored)
```

#### رموز التعليق حسب محركات قواعد البيانات:
* **MySQL / MariaDB:** `-- ` (يجب أن تليها مسافة أو محرف تحكم) أو `#` أو `/* ... */`.
* **MSSQL / PostgreSQL / SQLite:** `--` (لا تشترط مسافة) أو `/* ... */`.
* **Oracle:** `--` أو `/* ... */`.

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `;`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد الرمز `;` في منصة TryHackMe بنسبة نجاح 100% وظهور إشارة القبول الخضراء.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* محرف الفاصلة المنقوطة `;` ومحارف التعليق `--` هي الأدوات الهندسية التي تمكن المهاجم من إنهاء استعلام المطور والتخلص من بقية الشفرة غير المرغوب فيها، مما يتيح التلاعب التام بمنطق المعالجة.
