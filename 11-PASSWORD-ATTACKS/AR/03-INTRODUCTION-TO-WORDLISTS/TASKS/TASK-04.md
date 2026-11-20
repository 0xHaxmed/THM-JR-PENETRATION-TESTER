# Task 04 · Creating and Cleaning Wordlists (تطهير القوائم وتوليد كلمات المرور بأداة Crunch)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Why Wordlist Sanitization Matters:
Raw scraped text contains noise: HTML tags, punctuation fragments, duplicate words, carriage returns (\r from Windows systems), and uppercase/lowercase fragmentation. Cleaning and deduplicating wordlists minimizes redundant network requests during brute-force campaigns.

Sanitization Pipeline:
1. Merging & Deduplicating Raw Wordlists:
   cat cewl_words.txt raw_words.txt | sort -u > words_raw.txt

2. Case Normalization & Regex Filtering:
   cat words_raw.txt | tr '[:upper:]' '[:lower:]' | tr -d '\r' | grep -P '^[a-z0-9][a-z0-9._-]{4,}$' | sort -u > words_clean.txt
   Filters words with at least 5 alphanumeric/symbol characters, ensuring clean candidate entries.

3. Consolidating All Discovered Usernames:
   cat users_first.last.txt users_flast.txt users_firstl.txt users_from_emails.txt | sort -u > users.txt

4. Targeted Combinatorial Password Generation with Crunch:
   Reconnaissance revealed internal project codename "Helios" paired with the year 20NN and an exclamation mark: Helios20NN!
   Command:
   crunch 11 11 -t Helios20%%! -o pass_helios.txt
   Generates 100 candidate passwords from Helios2000! to Helios2099!.

Questions:
1. How many words are in words_clean.txt after cleaning up the file?
2. What is the 10th password in the pass_helios.txt file?
3. What flag does sort use to remove duplicate lines?
4. What crunch template character represents a digit?
```

### 2. Arabic Pedagogical Translation
```text
أهمية تطهير وتوحيد القوائم المعجمية:
تحتوي النصوص المستخرجة عشوائياً على تشويش تقني: وسوم HTML، علامات ترقيم، كلمات مكررة، محارف نهاية السطر في ويندوز (`\r`)، وتشتت بين الأحرف الكبيرة والصغيرة. يؤدي تطهير القوائم وإزالة التكرارات إلى خفض عدد الطلبات الشبكية غير المجدية وتفادي إهدار موارد الفحص.

خطوات خط الأنابيب لتطهير وتوليد القوائم:
1. دمج وإزالة التكرار من القوائم الأولية:
   `cat cewl_words.txt raw_words.txt | sort -u > words_raw.txt`

2. توحيد حالة الأحرف والفلترة بالتعبيرات القياسية:
   `cat words_raw.txt | tr '[:upper:]' '[:lower:]' | tr -d '\r' | grep -P '^[a-z0-9][a-z0-9._-]{4,}$' | sort -u > words_clean.txt`
   يعمل هذا الأمر على تحويل الحروف إلى صغيرة، وحذف محارف `\r`، واستبقاء الكلمات المكونة من 5 محارف على الأقل دون فراغات.

3. دمج كافة أسماء المستخدمين المكتشفة:
   `cat users_first.last.txt users_flast.txt users_firstl.txt users_from_emails.txt | sort -u > users.txt`

4. التوليد التركيبي لكلمات المرور الموجهة بأداة Crunch:
   أظهر الاستطلاع أن الشركة تعتمد نمط تسمية لمشروعها الداخلي Helios متبوعاً بسنوات الألفينات وعلامة تعجب: `Helios20NN!`.
   الأمر المنفذ:
   `crunch 11 11 -t Helios20%%! -o pass_helios.txt`
   يولد هذا الأمر بدقة 100 كلمة مرور تبدأ من `Helios2000!` وحتى `Helios2099!`.

الأسئلة:
1. كم عدد الكلمات الموجودة في ملف words_clean.txt بعد الانتهاء من تنقيته وتطهيره؟
2. ما هي كلمة المرور العاشرة (10th password) في ملف pass_helios.txt؟
3. ما هو المعامل (flag) الذي تستخدمه أداة sort لإزالة الأسطر المكررة؟
4. ما هو المحرف المستخدم في قوالب أداة crunch لتمثيل الأرقام (digit)؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. Word count in `words_clean.txt`?
  2. 10th password in `pass_helios.txt`?
  3. Sort deduplication flag?
  4. Crunch digit mask symbol?
- **Pedagogical Goal:** Master Linux pipeline sanitization commands (`sort -u`, `tr`, `grep`) and crunch template masking syntax.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Crunch Mask Symbol System
| Symbol | Character Set Represented | Example Match |
| :---: | :--- | :--- |
| **`@`** | Lowercase alphabetic (`a-z`) | `@` $\longrightarrow$ `a, b, c...` |
| **`,`** | Uppercase alphabetic (`A-Z`) | `,` $\longrightarrow$ `A, B, C...` |
| **`%`** | Numeric digits (`0-9`) | `%` $\longrightarrow$ `0, 1, 2...` |
| **`^`** | Special characters / punctuation | `^` $\longrightarrow$ `!, @, #...` |

When `crunch 11 11 -t Helios20%%! -o pass_helios.txt` is run:
- Total length: exactly 11 characters.
- Fixed prefix: `Helios20` (8 chars).
- Variable digits: `%%` ($10 \times 10 = 100$ combinations: `00` through `99`).
- Fixed suffix: `!` (1 char).
- Sequence: `Helios2000!`, `Helios2001!`, ... `Helios2009!` (10th entry).

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Word Count
> استخدم الأمر `wc -l words_clean.txt` لمعرفة عدد الكلمات بعد التطهير.

### Hint 2: 10th Password
> الأرقام تبدأ من 00 (الأولى: Helios2000!) وحتى 09 (العاشرة: Helios2009!). يمكنك فحصها عبر `sed -n '10p' pass_helios.txt`.

### Hint 3: Sort & Crunch Flags
> معامل الفرز الفريد في sort هو `-u` (unique)، ورمز الرقم في crunch هو `%`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Execution

1. **Inspecting Cleaned Wordlist:**
   ```bash
   wc -l words_clean.txt
   ```
   Output: **161** words.
   - **Answer Q1:** `161`

2. **Generating Crunch Mask & Extracting 10th Password:**
   ```bash
   crunch 11 11 -t Helios20%%! -o pass_helios.txt
   sed -n '10p' pass_helios.txt
   ```
   The sequence from line 1 to 10:
   - Line 1: `Helios2000!`
   - Line 2: `Helios2001!`
   - ...
   - Line 10: `Helios2009!`
   - **Answer Q2:** `Helios2009!`

3. **Sort Unique Flag:**
   The standard GNU `sort` flag to emit only unique lines is `-u` (`--unique`).
   - **Answer Q3:** `-u`

4. **Crunch Digit Placeholder:**
   The template placeholder for numeric digits is `%`.
   - **Answer Q4:** `%`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

1. **Question 1:**
   ```text
   161
   ```
2. **Question 2:**
   ```text
   Helios2009!`
   ```
3. **Question 3:**
   ```text
   -u
   ```
4. **Question 4:**
   ```text
   %
   ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified on user's completed account:
- Task 4 Question 1: `161` (Correct)
- Task 4 Question 2: `Helios2009!` (Correct)
- Task 4 Question 3: `-u` (Correct)
- Task 4 Question 4: `%` (Correct)

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Sanitization reduces attack surface latency.** Stripping non-compliant words and normalizing case prevents an automated fuzzer from sending thousands of malformed or invalid requests that clutter SIEM logs and trigger Web Application Firewall (WAF) rate blocks.
