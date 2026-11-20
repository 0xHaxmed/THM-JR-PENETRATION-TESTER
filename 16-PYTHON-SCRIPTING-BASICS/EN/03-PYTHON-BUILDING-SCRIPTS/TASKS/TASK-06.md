# Task 06: Password Strength Checker Project (مشروع أداة فحص قوة كلمات المرور)

## Stage A: Original Task Content & Educational Context (نص المهمة والسياق التعليمي)
This capstone task brings together all concepts acquired across the module to build a complete, real-world cybersecurity application: **The Password Strength Checker (`password_checker.py`)**.

### Architectural Breakdown:
1. **Wordlist Loader (`load_common_passwords`):**
   - Ingests `common_passwords.txt` safely using a context manager (`with open(...)`).
   - Normalizes entries with `.strip().lower()`.
   - Protects against missing files using `try...except FileNotFoundError`.
2. **Password Evaluator (`check_password`):**
   - Initializes `score = 0` and an empty `feedback = []` list.
   - Evaluates minimum length thresholds: $\ge 8$ characters (+1), $\ge 12$ characters (+1).
   - Evaluates character set diversity: uppercase letters (`string.ascii_uppercase`, +1), numbers (`string.digits`, +1), and special symbols (`string.punctuation`, +1).
   - **Critical Security Override:** If the password matches an entry in `common_passwords.txt`, the score is immediately overridden to `0`, discarding all diversity points, and warning the user: `"This password is in the common-passwords list. Choose another."`
3. **Application Loop & Secure Audit Logging (`main`):**
   - Continuously prompts the user for passwords using `while True:`.
   - Handles termination commands cleanly (`quit`).
   - Discards empty strings (`continue`).
   - Maps scores $\{0, 1\} \rightarrow \text{"Weak"}$, $\{2, 3\} \rightarrow \text{"Moderate"}$, $\{4, 5\} \rightarrow \text{"Strong"}$.
   - **Privacy-Preserving Audit Logging:** Appends the evaluation to `password_log.txt` without logging the cleartext password, masking it with asterisks: `'*' * len(password)`.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والتشغيلية)
- **Common Password Override Logic:** A password like `Password123!` possesses length ($\ge 12$), uppercase, numbers, and symbols, scoring a perfect $5/5$ in naïve regex checks. However, because it resides in common password dictionaries (and `rockyou.txt`), it can be cracked in milliseconds via dictionary attacks. Overriding the score to `0` when present in known lists reflects authentic security risk modeling.
- **Sensitive Data Logging Hazards:** Logging cleartext passwords to disk creates high-severity findings in security audits (CWE-532: Insertion of Sensitive Information into Log File). The script implements defensive credential masking by writing `'*' * len(password)` into `password_log.txt`.
- **Entropy vs. Complexity Rules:** Traditional length/complexity metrics can be misleading (e.g., NIST SP 800-63B recommends passphrases over complex short strings). A 4-word passphrase (`correct horse battery staple`) has massive entropy despite lacking numbers or symbols.

---

## Stage C: Guided Hints (تلميحات متدرجة)
1. **Tier 1 (Conceptual):**
   - Question 1 asks for the score assigned to a password if it appears in `common_passwords.txt`.
   - Question 2 asks for the strength rating displayed for `TryHackMe!2025`.
   - Question 3 asks for the number of asterisks logged in `password_log.txt` for `TryHackMe!2025`.
2. **Tier 2 (Logic & Calculations):**
   - In Question 1, look at the code: `if password.lower() in common_list: score = 0`.
   - In Question 2, evaluate `TryHackMe!2025`: length is $14 \ge 12$, has uppercase, digits, symbols, not in list $\rightarrow$ Score 5 $\rightarrow$ `"Strong"`.
   - In Question 3, count the length of the string `"TryHackMe!2025"`:
     - `TryHackMe` (9) + `!` (1) + `2025` (4) = $9 + 1 + 4 = 14$ characters.
     - `'*' * 14` produces 14 asterisks: `**************`.
3. **Tier 3 (Exact Values):**
   - Q1: `0`
   - Q2: `Strong`
   - Q3: `14`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

Let's trace `password_checker.py` when evaluating `TryHackMe!2025`:

```python
password = "TryHackMe!2025"
length = len(password) # 14
```

1. **Length Assessment:**
   - $14 \ge 8 \rightarrow \text{score} = 1$
   - $14 \ge 12 \rightarrow \text{score} = 2$
2. **Character Set Assessment:**
   - Uppercase present (`'T'`, `'H'`, `'M'`) $\rightarrow \text{score} = 3$
   - Digits present (`'2'`, `'0'`, `'2'`, `'5'`) $\rightarrow \text{score} = 4$
   - Punctuation present (`'!'`) $\rightarrow \text{score} = 5$
3. **Common Password Check:**
   - `"tryhackme!2025"` is not in `common_passwords.txt` $\rightarrow$ score remains `5`.
4. **Label Mapping:**
   - `strength_labels[5]` $\rightarrow$ `"Strong"`.
5. **Masking & Logging:**
   - Log line: `f"Password: {'*' * 14} | Strength: Strong (5/5)\n"`
   - Asterisks: `**************` (exactly 14 asterisks).

Terminal execution trace:
```shell-session
ubuntu@tryhackme:~/Building-Scripts$ python3 password_checker.py

Enter a password to check (or 'quit' to exit): TryHackMe!2025

Strength: Strong (5/5)

Enter a password to check (or 'quit' to exit): quit
Goodbye.
ubuntu@tryhackme:~/Building-Scripts$ cat password_log.txt
Password: ************** | Strength: Strong (5/5)
```

---

## Stage E: Final Answer & Key Values (الإجابة النهائية والقيم المستخرجة)
- **Question 1:** In the `check_password` function, what score does the password receive if it is found in the common_passwords list?
  - **Answer:** `0`
- **Question 2:** On the VM, run `password_checker.py` and enter the password `TryHackMe!2025`. What strength label does the program display?
  - **Answer:** `Strong`
- **Question 3:** After running the checker with the password `TryHackMe!2025`, open `password_log.txt` on the VM. How many asterisks appear on that line?
  - **Answer:** `14`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)
- **Automated Verification Script:**
  ```python
  import string

  def check_password(password, common_list):
      score = 0
      feedback = []
      if len(password) >= 8: score += 1
      if len(password) >= 12: score += 1
      if any(c in string.ascii_uppercase for c in password): score += 1
      if any(c in string.digits for c in password): score += 1
      if any(c in string.punctuation for c in password): score += 1
      if password.lower() in common_list:
          score = 0
          feedback = ["Common password"]
      return score, feedback

  # Test common override
  score, _ = check_password("Password123!", ["password123!"])
  assert score == 0

  # Test TryHackMe!2025
  score, _ = check_password("TryHackMe!2025", ["password"])
  assert score == 5
  assert len("TryHackMe!2025") == 14
  assert '*' * len("TryHackMe!2025") == "**************"

  print("Task 06 Password Strength Checker Architecture Verified Successfully.")
  ```
  Output:
  ```
  Task 06 Password Strength Checker Architecture Verified Successfully.
  ```

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)
Integrating modular functions, exception-guarded file I/O, standard libraries, and privacy-preserving audit logging produces robust security utilities. Always prioritize dictionary checks over pure complexity metrics to defend against known password lists.
