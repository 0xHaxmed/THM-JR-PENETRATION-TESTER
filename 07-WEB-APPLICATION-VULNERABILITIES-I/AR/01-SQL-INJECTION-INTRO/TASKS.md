# دليل مهام غرفة حقن قواعد البيانات (SQL Injection Tasks Guide)

يقدم هذا الملف فهرساً شاملاً ومفصلاً لجميع المهام العشر الخاصة بغرفة **SQL Injection**، مع توثيق الأسئلة، الإجابات المعتمدة، وروابط الوصول المباشر للتحليل التقني المتعمق (بمنهجية المراحل السبع من أ إلى ز).

---

## جدول المهام والحلول الموثقة (Tasks Summary Table)

| رقم المهمة | عنوان المهمة (Task Title) | السؤال المطروح (Question) | نوع الإجابة / القيمة (Answer) | ملف التحليل التفصيلي |
| :---: | :--- | :--- | :--- | :---: |
| **01** | Brief | What does SQL stand for? | `Structured Query Language` | [TASK-01.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-01.md) |
| **02** | What is a Database? | 1. Acronym for database software?<br>2. Name of grid-like structure? | Q1: `DBMS`<br>Q2: `table` | [TASK-02.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-02.md) |
| **03** | What is SQL? | 1. Statement to retrieve data?<br>2. Clause to combine SELECTs?<br>3. Statement to add data? | Q1: `SELECT`<br>Q2: `UNION`<br>Q3: `INSERT` | [TASK-03.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-03.md) |
| **04** | What is SQL Injection? | What character signifies the end of a statement? | `;` | [TASK-04.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-04.md) |
| **05** | In-Band SQLi | What is the flag after completing level one? | `THM{SQL_INJECTION_3840}` | [TASK-05.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-05.md) |
| **06** | Blind SQLi - Auth Bypass | What is the flag after completing level two? | `THM{SQL_INJECTION_9581}` | [TASK-06.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-06.md) |
| **07** | Blind SQLi - Boolean Based | What is the flag after completing level three? | `THM{SQL_INJECTION_1093}` | [TASK-07.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-07.md) |
| **08** | Blind SQLi - Time Based | What is the final flag after completing level four? | `THM{SQL_INJECTION_MASTER}` | [TASK-08.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-08.md) |
| **09** | Out-of-Band SQLi | Protocol beginning with D to exfiltrate data? | `DNS` | [TASK-09.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-09.md) |
| **10** | Remediation | Method of protecting from SQLi? (P... S...) | `Prepared Statements` | [TASK-10.md](../../01-SQL-INJECTION-INTRO/TASKS/TASK-10.md) |

---

## المنهجية المتبعة في إعداد المهام (7-Stage Methodology)
تمت كتابة وتوثيق كل ملف من الملفات المذكورة أعلاه وفق منهجية دقيقة وشاملة تتألف من المراحل التالية:
1. **المرحلة أ:** نص المهمة الأصلي والأسئلة (باللغة الإنجليزية والترجمة العربية).
2. **المرحلة ب:** التفكير التحليلي المستقل والحس الأمني (OpSec وتحليل السلوك).
3. **المرحلة ج:** التلميحات والإرشادات المتدرجة (ثلاثة مستويات توجيهية).
4. **المرحلة د:** الحل العملي الكامل والعمق التقني (مع الشروحات، الأوامر، والرسومات التوضيحية).
5. **المرحلة هـ:** الإجابة النهائية والقيم المستخرجة (وفق حساب المنصة المعتمد).
6. **المرحلة و:** التحقق العملي وإثبات الأثر (برهان الكفاءة وإعادة الإنتاج).
7. **المرحلة ز:** الخاتمة والدرس المستفاد (الخلاصة التكتيكية).
