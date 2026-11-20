# Task 03: What is SQL? (صياغة وتراكيب لغة الاستعلام الهيكلية)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
To interact with a database, developers execute SQL statements. Understanding core SQL syntax, clauses, and operators is mandatory for dissecting how SQL injection attacks manipulate database logic.

#### Core SQL Statements:
1. **SELECT:**
   - Retrieves rows and attributes from one or more tables.
   - Example: `SELECT username, email FROM users WHERE id = 1;`
2. **INSERT:**
   - Adds new records into a specified table.
   - Example: `INSERT INTO users (username, password) VALUES ('attacker', 'hacked123');`
3. **UPDATE:**
   - Modifies existing records based on a filtering condition.
   - Example: `UPDATE users SET role = 'admin' WHERE id = 5;`
4. **DELETE:**
   - Removes records permanently from a table.
   - Example: `DELETE FROM logs WHERE level = 'DEBUG';`

#### Essential Clauses and Operators:
- **UNION:**
  - Combines the result sets of two or more `SELECT` statements into a single, unified result set.
  - Crucial rule: Both queries must return the exact same number of columns with compatible data types.
- **WHERE:**
  - Filters records based on boolean conditions (e.g., `WHERE price > 100 AND active = 1`).
- **ORDER BY:**
  - Sorts results by a specific column name or column index number (e.g., `ORDER BY 3`).

#### الأسئلة المطروحة (Questions):
1. What SQL statement is used to retrieve data? (ما هو أمر SQL المستخدم لاسترجاع واستخلاص البيانات؟)
2. What SQL clause is used to combine the results of two or more SELECT statements? (ما هي عبارة SQL المستخدمة لدمج نتائج استعلامين أو أكثر من استعلامات SELECT؟)
3. What SQL statement is used to add data? (ما هو أمر SQL المستخدم لإضافة بيانات جديدة؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الأهمية التكتيكية لمشغل UNION في اختبار الاختراق:**
  * مشغل **`UNION`** هو السلاح الأكثر فتكاً في هجمات الحقن المباشر (In-Band SQLi).
  * في التطبيقات العادية، ينفذ الخادم استعلاماً مخصصاً مثل عرض مقال أو تفاصيل منتج:
    `SELECT title, description, date FROM articles WHERE id = 1;`
  * بواسطة عبارة `UNION`، يستطيع المهاجم إجبار الخادم على ضم نتائج استعلام جديد بالكامل إلى الاستعلام الأصلي:
    `UNION SELECT username, password, 'dummy' FROM users;`
  * هذا التحايل يمكن المهاجم من قراءة أي جدول في قاعدة البيانات وعرضه في واجهة المستخدم العادية المخصصة لقراءة المقالات!

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * الأمر المستخدم للاسترجاع يبدأ بحرف S ويتكون من 6 أحرف.
2. **التلميح الثاني (المستوى العملي):**
   * عبارة الدمج هي UNION، وأمر إضافة البيانات هو INSERT.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * إجابة السؤال 1: `SELECT`.
   * إجابة السؤال 2: `UNION`.
   * إجابة السؤال 3: `INSERT`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### المخطط المنطقي لعمل مشغل UNION:
```text
Query 1: SELECT col1, col2 FROM table1
Result Set 1:
┌──────┬──────┐
│  A1  │  A2  │
└──────┴──────┘

Query 2: SELECT col3, col4 FROM table2
Result Set 2:
┌──────┬──────┐
│  B1  │  B2  │
└──────┴──────┘

Combined with UNION:
┌──────┬──────┐
│  A1  │  A2  │  <-- From Query 1
├──────┼──────┤
│  B1  │  B2  │  <-- Appended From Query 2
└──────┴──────┘
```

#### شروط نجاح دمج الاستعلامات:
1. **تطابق عدد الأعمدة:** إذا كان الاستعلام الأول يرجع 3 أعمدة، يجب أن يرجع الاستعلام الثاني 3 أعمدة بالضبط (`UNION SELECT 1,2,3`).
2. **توافق الأنواع:** إذا كان العمود الثاني نصياً، يجب أن يقبل العمود المقابل قيماً نصية.

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `SELECT`
* **إجابة السؤال الثاني:**
  `UNION`
* **إجابة السؤال الثالث:**
  `INSERT`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم مطابقة الأوامر الثلاثة `SELECT` و `UNION` و `INSERT` واعتمادها بنجاح 100% في منصة TryHackMe.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* لغة SQL مبنية على أوامر قياسية واضحة؛ واستيعاب كيفية توظيف مشغل `UNION` وأوامر الاسترجاع يمنح المختبر القدرة على إعادة كتابة استعلامات الخادم واستخراج أي معلومة مخفية في النظام.
