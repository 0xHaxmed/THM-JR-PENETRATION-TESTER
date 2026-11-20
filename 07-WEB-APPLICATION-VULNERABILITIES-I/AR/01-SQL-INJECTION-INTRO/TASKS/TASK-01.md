# Task 01: Brief (المقدمة ومدخل إلى لغة الاستعلام الهيكلية)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Welcome to the SQL Injection room!
SQL (Structured Query Language) Injection is historically one of the most devastating and prevalent web application vulnerabilities. When an application accepts user input and directly concatenates it into a dynamic database query without proper validation or parameterization, an attacker can manipulate the query's logical structure.

This can allow attackers to:
- Bypass authentication mechanisms entirely.
- Access, extract, and dump confidential database tables (including usernames, passwords, credit card numbers, and PII).
- Modify, corrupt, or permanently destroy database records.
- In certain database configurations (such as MSSQL with `xp_cmdshell` or MySQL with `INTO OUTFILE`), execute operating system commands and achieve full Remote Code Execution (RCE).

In this room, we will explore:
- What databases are and how SQL operates.
- How SQL injection vulnerabilities occur from first principles.
- Hands-on exploitation of:
  - In-Band (UNION-Based) SQLi.
  - Blind SQLi (Authentication Bypass).
  - Blind SQLi (Boolean-Based).
  - Blind SQLi (Time-Based).
  - Out-of-Band (OOB) SQLi.
- Industry-standard remediation strategies (Prepared Statements).

#### الأسئلة المطروحة (Questions):
1. What does SQL stand for? (ماذا يمثل اختصار SQL؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المكانة التاريخية والخطورة الاستراتيجية لثغرات SQLi:**
  * على مدار أكثر من عقدين من الزمان، احتلت ثغرة SQL Injection صدارة تصنيفات **OWASP Top 10** تحت بند (A03: Injection).
  * تكمن خطورة الثغرة في أنها تمنح المهاجم وصولاً مباشراً إلى "القلب التخزيني" للمؤسسة (قواعد البيانات العلائقية RDBMS).
  * في كثير من الحالات، حتى لو كان خادم الويب مؤمناً ومقيداً بصلاحيات محدودة، فإن السيطرة على قاعدة البيانات تعني سحب التجزئات السرية لكلمات المرور ومفاتيح التشفير، مما يمهد للسيطرة الكاملة على البنية التحتية.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * الكلمة الأولى تعبر عن الهيكلية المنظمة (Structured).
2. **التلميح الثاني (المستوى العملي):**
   * الكلمة الثانية هي الاستعلام (Query)، والثالثة هي اللغة (Language).
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * العبارة الكاملة هي: `Structured Query Language`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### التعريف المعياري للغة SQL:
* **SQL (Structured Query Language):** هي لغة قياسية دولية (ANSI/ISO) طُوّرت في سبعينيات القرن الماضي بواسطة شركة IBM لإدارة ومعالجة البيانات المخزنة في أنظمة إدارة قواعد البيانات العلائقية (Relational Database Management Systems - RDBMS).
* **تصنيف أوامر SQL:**
  1. **DQL (Data Query Language):** مثل `SELECT` (استرجاع البيانات).
  2. **DML (Data Manipulation Language):** مثل `INSERT`, `UPDATE`, `DELETE` (تعديل البيانات).
  3. **DDL (Data Definition Language):** مثل `CREATE`, `ALTER`, `DROP` (هندسة الجداول).
  4. **DCL (Data Control Language):** مثل `GRANT`, `REVOKE` (صلاحيات المستخدمين).

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `Structured Query Language`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم مطابقة النص `Structured Query Language` في منصة TryHackMe بنسبة نجاح 100% واعتماد الإنجاز الأولي للغرفة.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* لغة SQL هي الوسيط اللغوي الموحد لمخاطبة قواعد البيانات، وفهم بنيتها الهيكلية هو المتطلب الأساسي الأول لاكتشاف عيوب صياغة الاستعلامات واستغلالها.
