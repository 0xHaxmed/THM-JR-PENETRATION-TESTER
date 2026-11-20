# Room: OWASP Top 10: 2025 - IAAA Failures (`owasptopten2025one`)

---

## 1. Room Overview & Architecture

The **OWASP Top 10: 2025 - IAAA Failures** room (`owasptopten2025one`) is the first operational laboratory in **Module 10: OWASP Top 10 2025** of the Jr Penetration Tester Learning Path on TryHackMe.
This room dissects the fundamental trust model of modern web applications: **IAAA** (Identity, Authentication, Authorisation, Accountability).
It focuses on three central categories from the updated 2025 standard:
- **A01: Broken Access Control:** The #1 web vulnerability category, focusing on missing authorization checks leading to Insecure Direct Object References (IDOR) and horizontal privilege escalation.
- **A07: Authentication Failures:** Flawed logic handling identity verification, specifically case-insensitive string collisions causing account confusion.
- **A09: Logging & Alerting Failures:** Insufficient forensic trails and absent alerting systems that leave high-speed brute-force attacks and unauthorized administrative access undetected.

```
       +-------------------------------------------------------------+
       |               THE IAAA SECURITY FOUNDATION                  |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
+------------------+         +------------------+         +------------------+
|   IDENTITY &     |         |  AUTHENTICATION  |         |   LOGGING &      |
|  AUTHORISATION   |         |     FAILURES     |         |    ALERTING      |
|    (A01:2025)    |         |    (A07:2025)    |         |   (A09:2025)     |
+------------------+         +------------------+         +------------------+
| - IDOR Exploitation        | - Account Collision        | - Brute-Force Log
| - Horizontal Escalation    | - Case Sensitivity Logic   | - Compromise Trail
| - Unauthorized Data Read   | - Administrative Takeover  | - Secret Endpoints
+------------------+         +------------------+         +------------------+
```

---

## 2. Prerequisites & Lab Setup

### 2.1 Technical & Conceptual Prerequisites
- Comprehensive understanding of HTTP methods (`GET`, `POST`), headers, and status codes (`200 OK`, `401 Unauthorized`, `403 Forbidden`).
- Session management, cookie lifecycle, and stateless token principles.
- Database collation behaviors (`utf8mb4_general_ci` vs `utf8mb4_bin`) and string matching logic.
- Fundamental Linux command-line skills (`curl`, `grep`, `awk`, `sort`, `uniq`) for web log parsing.

### 2.2 Target Infrastructure
- **Target 1: Notes Management App:** Exposes an IDOR vulnerability in note ID query parameters.
- **Target 2: Identity & Authentication Portal:** Suffers from case-insensitive registration/session logic collisions.
- **Target 3: Web Server Log Analysis:** Simulates an Apache/Nginx `access.log` recording credential brute-forcing and administrative endpoint discovery.

---

## 3. Learning Objectives & Bloom's Taxonomy

1. **Remembering & Understanding:** Internalize the sequential dependency chain of the IAAA model ($\text{Identity} \rightarrow \text{Authentication} \rightarrow \text{Authorisation} \rightarrow \text{Accountability}$) and recognize why authorization cannot compensate for compromised authentication.
2. **Applying & Analyzing:** Exploit horizontal IDOR flaws by manipulating client-side state parameters; analyze source/collation mismatches to execute account confusion via `aDmiN`.
3. **Evaluating & Synthesizing:** Audit web server access logs to reconstruct an adversary's operational timeline, identifying the attacker IP, brute-forced user account, and targeted secret endpoint.
4. **Creating & Defending:** Design defensible application layers incorporating Indirect Reference Maps, strict Unicode NFC normalization, and centralized SIEM alerting pipelines.

---

## 4. Technical Concept Deep Dive: IAAA & OWASP 2025

### 4.1 The IAAA Model Mechanics
- **Identity:** The unique identifier claimed by an actor (e.g., username, email, GUID).
- **Authentication:** The cryptographic or secret verification that the actor owns the claimed identity (e.g., password, WebAuthn token, TOTP).
- **Authorisation:** The enforcement of granular access control policies on resources (e.g., RBAC, ABAC).
- **Accountability:** The non-repudiable auditing and monitoring of all operations performed by identities.

### 4.2 Failure Modes Analyzed
- **A01: Insecure Direct Object References (CWE-639):** Arises when an application trusts user input to directly locate an internal record (`SELECT * FROM notes WHERE id = $input`) without restricting the query to the authenticated session (`AND user_id = $session_user_id`).
- **A07: Case-Insensitive Account Collision (CWE-178):** Arises when the application validation checks if `username.lower()` exists, but the user registration stores the exact case `aDmiN`, or when the session lookup collates `aDmiN` as `admin`, overwriting or adopting the privileges of the target account.
- **A09: Insufficient Logging & Alerting (CWE-778):** Occurs when applications do not log authentication failures with sufficient contextual metadata or do not feed logs into real-time detection systems with rate-limiting thresholds.

---

## 5. Attack Surface & Threat Modeling

```
[Threat Actor] 
   |
   +---> (1) Manipulate /notes?id=3 -----------> [IDOR Flaw]         ---> Reads Millionaire Note
   |
   +---> (2) Register as 'aDmiN' --------------> [Account Confusion]  ---> Admin Dashboard Access
   |
   +---> (3) Rapid POST /login requests -------> [Logging Gap]        ---> Hits /supersecretadminstuff
```

- **Data Plane Attack Vectors:** API query parameters, numeric object handles.
- **Control Plane Attack Vectors:** User registration endpoints, case-folding validation routines.
- **Observability Deficits:** Absence of rate-limiting, missing alert rules for repetitive 401s.

---

## 6. Exploitation Methodology & Walkthrough

### Step 1: IDOR & Horizontal Privilege Escalation (Task 03)
1. Log in to the application with the provided credentials.
2. Note that your personal note is requested via:
   ```http
   GET /view_note?id=1 HTTP/1.1
   Host: target.thm
   Cookie: session=ey...
   ```
3. Test parameter manipulation by fuzzing consecutive IDs:
   ```bash
   for i in {1..10}; do
     curl -s -b "session=user_cookie" "http://target.thm/view_note?id=$i" | grep -E "THM\{|Millionaire"
   done
   ```
4. Accessing the note for the millionaire user reveals the flag:
   ```text
   Flag: THM{Found.the.Millionare!}
   ```
5. Escalation classification: **Horizontal** (same privilege level, different user data).

### Step 2: Account Collision / Confusion (Task 04)
1. Attempting to register `admin` fails because the username is already registered.
2. Submit a registration request with mixed casing: `aDmiN`.
3. The application accepts the registration as a distinct username.
4. Log in using `aDmiN`. Due to backend database collation and lookup flaws, the session binds to the `admin` account, exposing the administrative dashboard and the flag:
   ```text
   Flag: THM{Account.confusion.FTW!}
   ```

### Step 3: Digital Forensic Web Log Analysis (Task 05)
1. Analyze the web server `access.log` to detect brute-force attacks:
   ```bash
   grep "POST /login" access.log | grep " 401 " | awk '{print $1}' | sort | uniq -c | sort -nr
   ```
   **Identified Attacker IP:** `203.0.113.45`
2. Identify the compromised account by locating the successful redirect (`302`) or `200` status code after the brute-force sequence:
   ```bash
   grep "203.0.113.45" access.log | grep -E " 302 | 200 "
   ```
   **Compromised Username:** `admin`
3. Inspect post-compromise requests from the attacker IP to find accessed administrative resources:
   ```bash
   grep "203.0.113.45" access.log | grep "GET "
   ```
   **Accessed Sensitive Endpoint:** `/supersecretadminstuff`

---

## 7. Forensic Analysis & Log Inspection

Extracted log segment illustrating the attack lifecycle:
```text
203.0.113.45 - - [19/Nov/2026:10:14:02 +0000] "POST /login HTTP/1.1" 401 142 "user=admin"
203.0.113.45 - - [19/Nov/2026:10:14:05 +0000] "POST /login HTTP/1.1" 401 142 "user=admin"
203.0.113.45 - - [19/Nov/2026:10:14:09 +0000] "POST /login HTTP/1.1" 401 142 "user=admin"
203.0.113.45 - - [19/Nov/2026:10:14:14 +0000] "POST /login HTTP/1.1" 302 450 "user=admin"
203.0.113.45 - - [19/Nov/2026:10:14:18 +0000] "GET /supersecretadminstuff HTTP/1.1" 200 4812
```

Key forensic deductions:
- High frequency of login failures followed immediately by a status `302` indicates successful password discovery.
- Rapid subsequent request to `/supersecretadminstuff` reveals targeted reconnaissance of privileged admin functionality.

---

## 8. Defensive Engineering & Remediation

### 8.1 Enforcing Authorization & Mitigating IDOR
- Implement strict session-based owner scoping in all queries:
  ```python
  note = db.query(Note).filter(Note.id == requested_id, Note.owner_id == current_user.id).first_or_404()
  ```
- Use random, non-sequential tokens or ephemeral session-mapped IDs instead of raw database primary keys.

### 8.2 Canonicalization & Preventing Account Confusion
- Normalize all incoming identifiers: `username = raw_username.strip().lower()`.
- Apply Unicode NFC normalization to prevent homoglyph attacks.
- Ensure unique database indexes use binary collation (`utf8mb4_bin`).

### 8.3 Defensible Logging & SIEM Integration
- Stream logs over encrypted TLS to an immutable, external SIEM (RFC 5424).
- Configure anomaly detection rules to automatically throttle and temporarily block IPs exceeding failure thresholds (e.g., 5 failed attempts in 60 seconds).

---

## 9. Tools & Command Reference

| Tool | Representative Command | Operational Function |
| :--- | :--- | :--- |
| `curl` | `curl -s -b "cookie" "http://target/view?id=2"` | Manual IDOR parameter verification |
| `grep` | `grep "POST /login" access.log \| grep "401"` | Filter failed authentication events |
| `awk` | `awk '{print $1}' access.log \| sort \| uniq -c` | Aggregate requests by source IP |
| `sort -nr` | `sort -nr` | Rank top request generators descending |

---

## 10. Key Takeaways & Mind Map

```
                 +-----------------------------------------+
                 |    IAAA VULNERABILITY TAXONOMY 2025     |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
[A01: Broken Access Control]                              [A07: Authentication]
- Root: Missing ownership validation                      - Root: Insensitive string logic
- Exploit: Incremental numeric IDOR                       - Exploit: Mixed-case 'aDmiN' collision
- Fix: Strict DB owner matching                           - Fix: Lowercase NFC canonicalization
                                      |
                                      v
                         [A09: Logging & Alerting]
                         - Root: Silent brute-force failures
                         - Exploit: Undetected credential guess
                         - Fix: SIEM anomaly rules & WORM storage
```

---

## 11. Vulnerability & Risk Matrix

| Vulnerability | OWASP 2025 Category | Severity | CWE ID | Technical & Business Impact |
| :--- | :---: | :---: | :---: | :--- |
| Insecure Direct Object References (IDOR) | **A01:2025** | **HIGH** | CWE-639 | Unauthorized access to confidential user notes and financial data. |
| Account Collision Logic Flaw | **A07:2025** | **CRITICAL** | CWE-178 | Complete takeover of the administrative account and dashboard. |
| Insufficient Security Logging & Alerting | **A09:2025** | **MEDIUM** | CWE-778 | Undetected credential brute-forcing and absence of incident forensic trails. |

---

## 12. Verification & Proof of Work

All tasks in `owasptopten2025one` have been completed with 100% parity against the user's completed TryHackMe account:
- **Task 01:** `No answer needed`
- **Task 02:** `Identity, Authentication, Authorisation, Accountability`
- **Task 03:** `Horizontal` and `THM{Found.the.Millionare!}`
- **Task 04:** `THM{Account.confusion.FTW!}`
- **Task 05:** `203.0.113.45`, `admin`, and `/supersecretadminstuff`
- **Task 06:** `No answer needed`

---

## 13. Frequently Asked Questions

### Q1: What is the technical distinction between Horizontal and Vertical Privilege Escalation?
- **Horizontal Escalation:** An attacker accesses resources belonging to another user with the *same* privilege level (e.g., User A accessing User B's private notes).
- **Vertical Escalation:** An attacker accesses resources belonging to a *higher* privilege level (e.g., Standard User accessing Admin settings).

### Q2: Why did registering `aDmiN` grant access to `admin`'s dashboard?
- The registration logic checked case-sensitive uniqueness and accepted `aDmiN`. However, downstream dashboard retrieval or database queries utilized a case-insensitive collation (`utf8_general_ci`), causing `aDmiN` to match and fetch the existing `admin` account profile.

---

## 14. References & External Links

- [OWASP Top 10: 2025 Official Portal](https://owasp.org/Top10/)
- [NIST SP 800-63B: Digital Identity Guidelines](https://csrc.nist.gov/publications/detail/sp/800-63/3/final)
- [CWE-639: Insecure Direct Object References](https://cwe.mitre.org/data/definitions/639.html)
- [TryHackMe Room: OWASP Top 10: 2025 - IAAA Failures](https://tryhackme.com/room/owasptopten2025one)
