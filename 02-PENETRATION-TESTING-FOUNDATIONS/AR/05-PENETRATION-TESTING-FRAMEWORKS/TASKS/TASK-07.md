# Task 07: MITRE ATT&CK
## المهمة 07: مصفوفة ميتري للتكتيكات والتقنيات والإجراءات (MITRE ATT&CK)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 07
* **Original Title:** MITRE ATT&CK
* **Questions:**
  1. *In the ATT&CK matrix, what do the columns represent?*
  2. *In the ATT&CK matrix, what do the rows within each column represent?*
  3. *You compromised a web server by exploiting an unpatched vulnerability in its public-facing application. What ATT&CK technique ID would you use to classify this finding?*
* **Objective:** Understand the MITRE ATT&CK framework structure, navigate the Enterprise Matrix, differentiate between Tactics and Techniques, and classify real findings using ATT&CK IDs.
* **Core Concepts:** MITRE ATT&CK Matrix, Tactics (Columns), Techniques (Rows/Cells), Sub-techniques, Technique ID T1190 (Exploit Public-Facing Application).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول (أعمدة المصفوفة):**
  في مصفوفة ATT&CK، ماذا تمثل الأعمدة الرأسية (Columns) في أعلى الجدول (مثل: Initial Access, Execution, Persistence, Privilege Escalation)؟
  * تمثل **التكتيكات (tactics)**؛ والتكتيك يجيب على سؤال: *"لماذا يفعل المهاجم ذلك؟ ما هو هدفه المباشر؟"* (The "Why").
* **تحليل السؤال الثاني (صفوف وعناصر الأعمدة):**
  ماذا تمثل الصفوف أو العناصر الموجودة تحت كل عمود (مثل: Phishing, Command-Line Interface, Web Shell)؟
  * تمثل **التقنيات (techniques)**؛ والتقنية تجيب على سؤال: *"كيف يحقق المهاجم هذا الهدف؟"* (The "How").
* **تحليل السؤال الثالث (تصنيف ثغرة استغلال خادم ويب خارجي):**
  عندما يخترق المختبر خادم ويب عبر استغلال ثغرة غير مرقعة في تطبيق موجه للإنترنت، ما هو المعرف الرقمي الرسمي للتقنية في مصفوفة ATT&CK Enterprise؟
  * التقنية هي: **Exploit Public-Facing Application**.
  * المعرف الرقمي الخاص بها هو: **T1190**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** كلمة واحدة بصيغة الجمع: `tactics`.
* **Hint 2 (للسؤال 2):** كلمة واحدة بصيغة الجمع: `techniques`.
* **Hint 3 (للسؤال 3):** حرف T متبوع بأربعة أرقام: `T1190`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **معمارية مصفوفة MITRE ATT&CK:**
   * **الأعمدة (Columns):** تُمثل **Tactics (إجابة السؤال 1)** — الأهداف التكتيكية الـ 14 للمهاجم (مثل: Reconnaissance, Initial Access, Lateral Movement, Exfiltration).
   * **الصفوف/البنود (Rows):** تُمثل **Techniques (إجابة السؤال 2)** — الطرق والوسائل التقنية لتحقيق التكتيك.
   * **التقنيات الفرعية (Sub-techniques):** تقسيمات إضافية دقيقة برمز نقطي (مثل `T1059.001` لـ PowerShell).
2. **تصنيف اختراق خادم الويب الخارجي:**
   * التكتيك: **Initial Access (TA0001)**
   * التقنية: **Exploit Public-Facing Application**
   * المعرّف الرسمي في MITRE: **T1190 (إجابة السؤال 3)**.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *In the ATT&CK matrix, what do the columns represent?*
  * **Answer:** `tactics`
* **Question 2:** *In the ATT&CK matrix, what do the rows within each column represent?*
  * **Answer:** `techniques`
* **Question 3:** *You compromised a web server by exploiting an unpatched vulnerability in its public-facing application. What ATT&CK technique ID would you use to classify this finding?*
  * **Answer:** `T1190`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
MITRE ATT&CK Mapping:
Matrix Columns   ⟶ Tactics (e.g., Initial Access)
Matrix Rows      ⟶ Techniques (e.g., T1190)
Lookup: https://attack.mitre.org/techniques/T1190/
Name: Exploit Public-Facing Application
Tactic: Initial Access
ID: T1190
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في مصفوفة ATT&CK:**
> *"Tactics define the adversary's intent; Techniques define their tradecraft; mapping findings to ATT&CK speaks a universal language understood by defenders worldwide."*
> التكتيكات تحدد نوايا الخصم، والتقنيات تحدد أسلوب عمله؛ وربط نتائج تقريرك بمعرفات ATT&CK يوفر لغة عالمية موحدة يفهمها المدافعون حول العالم.
