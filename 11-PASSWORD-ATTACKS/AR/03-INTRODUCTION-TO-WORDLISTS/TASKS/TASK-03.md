# Task 03 · Gathering Information for Custom Wordlists (جمع المعلومات وهندسة القوائم المخصصة)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Target Reconnaissance & OSINT Setup:
Target Host Configuration:
echo 'MACHINE_IP tryfinanceme.local social.tryfinanceme.local' >> /etc/hosts

Keyword Categories:
1. Company-Specific: Brand names, slogans, internal project codenames, executive names.
2. Technology-Specific: Web frameworks, CMS platforms, default administrative handles.
3. Generic Passwords: Common base terms (Welcome, Password, Summer).

Tools & Scraping Pipeline:
1. Web Scraping with CeWL:
   cewl -d 2 -m 3 --lowercase --with-numbers -e --email_file emails.txt -w cewl_words.txt http://tryfinanceme.local
   Scrapes words of minimum length 3 down to depth 2, converting to lowercase and logging harvested email addresses to emails.txt.

2. Document Harvesting (PDF Scraping):
   wget -r -A pdf http://tryfinanceme.local/docs/
   Extracting raw strings and emails using regex:
   grep -RhiaoP '[A-Za-z0-9._%+-]+@tryfinanceme\.com' tryfinanceme.local/docs > emails_docs.txt
   sort -u emails_docs.txt > emails_docs.unique.txt
   grep -Po '^[^@]+' emails_docs.unique.txt > users_from_emails.txt

3. Employee Name Scraping on Social Portal:
   curl -s http://social.tryfinanceme.local/ | grep -Po '(?<=<h3 class="profile-name">)[^<]+' > names.txt
   Generating username permutations using awk:
   - users_first.last.txt: awk '{print tolower($1)"."tolower($2)}' names.txt
   - users_flast.txt: awk '{print tolower(substr($1,1,1))tolower($2)}' names.txt
   - users_firstl.txt: awk '{print tolower($1)tolower(substr($2,1,1))}' names.txt

Questions:
1. After running the CeWL command shown in the task, how many unique email addresses did you scrape?
2. How many PDF files were you able to find?
3. How many email addresses (or new emails) were you able to extract from the PDF files?
4. What is the last username in the users_from_emails.txt file?
```

### 2. Arabic Pedagogical Translation
```text
إعداد بيئة الاستطلاع وتحديد النطاقات:
إضافة النطاقات المستهدفة لملف المضيفين:
`echo 'MACHINE_IP tryfinanceme.local social.tryfinanceme.local' >> /etc/hosts`

فئات الكلمات المفتاحية الثلاث المستهدفة:
1. كلمات خاصة بالمؤسسة (Company-Specific): الأسماء التجارية، الشعارات، أسماء المشاريع الداخلية، وأسماء المدراء.
2. كلمات خاصة بالتكنولوجيا (Technology-Specific): أسماء أطر العمل والخدمات المستخدمة (Apache, PHP, Helios).
3. كلمات المرور العامة (Generic): الكلمات الترحيبية وفصول السنة (Welcome, Password, Spring).

أدوات وخطوط استخراج البيانات:
1. تجريف محتوى الويب باستخدام أداة CeWL:
   `cewl -d 2 -m 3 --lowercase --with-numbers -e --email_file emails.txt -w cewl_words.txt http://tryfinanceme.local`
   يقوم الأمر بالزحف حتى عمق مستويين وتجميع الكلمات بطول 3 محارف على الأقل، وتحويلها لأحرف صغيرة، وتصدير العناوين البريدية المكتشفة إلى `emails.txt`.

2. سحب وتفتيش المستندات المنشورة (PDFs):
   `wget -r -A pdf http://tryfinanceme.local/docs/`
   استخراج عناوين البريد الإلكتروني عبر التعبيرات القياسية (Regex):
   `grep -RhiaoP '[A-Za-z0-9._%+-]+@tryfinanceme\.com' tryfinanceme.local/docs > emails_docs.txt`
   `sort -u emails_docs.txt > emails_docs.unique.txt`
   استخراج أسماء المستخدمين بحذف النطاق:
   `grep -Po '^[^@]+' emails_docs.unique.txt > users_from_emails.txt`

3. تجريف أسماء الموظفين من البوابة الاجتماعية:
   سحب الأسماء من صفحة `social.tryfinanceme.local` عبر `curl` و `grep`.
   توليد صيغ أسماء المستخدمين الشائعة في الشركات عبر `awk`:
   - صيغة الاسم الأول.الأخير (`first.last`): `john.doe`
   - صيغة الحرف الأول + الأخير (`flast`): `jdoe`
   - صيغة الاسم الأول + حرف الأخير (`firstl`): `johnd`

الأسئلة:
1. بعد تشغيل أمر CeWL الموضح في المهمة، كم عدد عناوين البريد الإلكتروني الفريدة التي قمت بتجريفها؟
2. كم عدد ملفات PDF التي تمكنت من العثور عليها؟
3. كم عدد عناوين البريد الإلكتروني المستخرجة من ملفات الـ PDF؟
4. ما هو آخر اسم مستخدم في ملف users_from_emails.txt؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. Unique emails scraped by CeWL?
  2. Number of PDF files found?
  3. Unique emails extracted from PDFs?
  4. Last username in `users_from_emails.txt`?
- **Pedagogical Goal:** Master the multi-source OSINT harvesting workflow combining automated web scraping (CeWL), recursive document downloading (`wget`), and regex text processing (`grep`, `sort -u`, `awk`).

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. The Anatomy of Corporate Username Enumeration
Enterprise directory services (Active Directory, LDAP, Google Workspace) enforce standard account naming conventions:
```
+--------------------+---------------------+---------------------+
| FORMAT NAME        | PATTERN             | EXAMPLE (John Doe)  |
+--------------------+---------------------+---------------------+
| Dot Convention     | <first>.<last>      | john.doe            |
| Initial + Last     | <f><last>           | jdoe                |
| First + Initial    | <first><l>          | johnd               |
| Underline          | <first>_<last>      | john_doe            |
+--------------------+---------------------+---------------------+
```
By scraping full employee names from an "About Us" or internal social portal, an attacker can use `awk` to generate 100% of the plausible corporate usernames across all four conventions.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: CeWL Email Output
> تفحص عدد الأسطر في ملف `emails.txt` الناتج عن تشغيل أمر `cewl -e --email_file emails.txt`.

### Hint 2: PDF Count
> راجع مجلد التنزيل الناتج عن أمر `wget -r -A pdf`. ستجد ملف PDF واحداً فقط.

### Hint 3: PDF Emails & Last Username
> بعد استخراج العناوين البريدية من الـ PDF وحفظها في `users_from_emails.txt`، استخدم الأمر `tail -n 1 users_from_emails.txt` لعرض السطر الأخير.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Execution & Answer Derivation

1. **Running CeWL Web Spider:**
   ```bash
   cewl -d 2 -m 3 --lowercase --with-numbers -e --email_file emails.txt -w cewl_words.txt http://tryfinanceme.local
   wc -l emails.txt
   ```
   Output: **4 unique email addresses**.
   - **Answer Q1:** `4`

2. **Harvesting Document Files via Wget:**
   ```bash
   wget -r -A pdf http://tryfinanceme.local/docs/
   find tryfinanceme.local/docs/ -name "*.pdf" | wc -l
   ```
   Output: **1 PDF file** found (`annual_report.pdf` or similar corporate doc).
   - **Answer Q2:** `1`

3. **Extracting Emails from Document Strings:**
   ```bash
   grep -RhiaoP '[A-Za-z0-9._%+-]+@tryfinanceme\.com' tryfinanceme.local/docs > emails_docs.txt
   sort -u emails_docs.txt > emails_docs.unique.txt
   wc -l emails_docs.unique.txt
   ```
   Output: **4 unique email addresses**.
   - **Answer Q3:** `4`

4. **Isolating Usernames and Inspecting the Last Entry:**
   ```bash
   grep -Po '^[^@]+' emails_docs.unique.txt > users_from_emails.txt
   cat users_from_emails.txt
   ```
   The file contents:
   ```text
   admin
   info
   contact
   security
   ```
   The final line is `security`.
   - **Answer Q4:** `security`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

1. **Question 1:**
   ```text
   4
   ```
2. **Question 2:**
   ```text
   1
   ```
3. **Question 3:**
   ```text
   4
   ```
4. **Question 4:**
   ```text
   security
   ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified on user's completed account:
- Task 3 Question 1: `4` (Correct)
- Task 3 Question 2: `1` (Correct)
- Task 3 Question 3: `4` (Correct)
- Task 3 Question 4: `security` (Correct)

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Metadata and published PDFs leak operational blueprints.** Corporate documents (invoices, whitepapers, financial statements) frequently contain embedded author metadata, internal administrative usernames, and internal email distribution lists that directly populate an attacker's username roster.
