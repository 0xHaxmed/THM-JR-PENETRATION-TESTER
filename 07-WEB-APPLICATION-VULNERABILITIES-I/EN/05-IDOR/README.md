# Comprehensive Technical Room Guide: Insecure Direct Object References (IDOR)

---

## Section 01: Room Overview & Metadata

| Metadata Field | Technical Specification |
| :--- | :--- |
| **Room Title** | IDOR (Insecure Direct Object Reference) |
| **Learning Path** | Jr Penetration Tester (PT1 Aligned Curriculum) |
| **Module** | Web Application Vulnerabilities I (Module 07) |
| **Room Slug** | `idor` (Direct URL: `https://tryhackme.com/room/idor`) |
| **Difficulty Level** | Easy |
| **Room Type** | Walkthrough & Practical Laboratory |
| **Estimated Completion Time** | 30 Minutes |
| **Total Tasks** | 7 Interactive Tasks |
| **Target Application** | Interactive IDOR Sandbox & Acme IT Support Customer Management API |
| **User Completion Status** | 100% Completed (Verified live on TryHackMe platform) |

---

## Section 02: Educational & Operational Objectives

1. **Understand Insecure Direct Object References:** Master how IDOR occurs when an application exposes a reference to an internal database or file object, and processes client-supplied identifiers without verifying authorization.
2. **Distinguish Authentication vs. Authorization:** Recognize that applications vulnerable to IDOR possess fully operational authentication layers, but fail completely at the object-level authorization tier.
3. **Analyze Diverse Object Reference Formats:**
   - **Plaintext Identifiers:** Direct sequential integers manipulated via simple URL increments.
   - **Encoded Identifiers:** Reversible transformations like Base64 decoded, tampered, and re-encoded.
   - **Hashed Identifiers:** Deterministic digests (MD5) reversed using precomputed rainbow/lookup tables.
   - **Unpredictable Identifiers (UUIDs):** High-entropy strings audited via the Two-Account Technique.
4. **Identify Hidden IDOR Vectors in Modern Architectures:** Intercept background AJAX/Fetch API calls, analyze RESTful path segments, examine JSON request bodies, and discover hidden parameters via parameter mining.
5. **Execute End-to-End API IDOR Exploitation:** Intercept the Acme IT Support background call `/api/v1/customer?id=X`, tamper with parameters, and exfiltrate user data for accounts `1` and `3`.
6. **Apply Enterprise-Grade Remediation:** Implement session-scoped identifier resolution, database query ownership scoping, and indirect reference maps.

---

## Section 03: Target Architecture & Network Topology

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    NETWORK TOPOLOGY DIAGRAM                             |
|                                                                                         |
|  [ Authenticated Penetration Tester ]                                                   |
|  Session Identity: User ID 13 (pentester)                                               |
|                 │                                                                       |
|                 │ 1. Asynchronous Background Request:                                   |
|                 │    GET /api/v1/customer?id=1                                          |
|                 ▼                                                                       |
|  +──────────────────────────────────────────────────+                                   |
|  |           Acme IT Support Web Application        |                                   |
|  |──────────────────────────────────────────────────|                                   |
|  | - Authentication Layer: Validates Session Cookie | ➔ Valid Session!                  |
|  | - Authorization Layer: Checks if User 13 owns ID | ➔ MISSING CHECK!                  |
|  +──────────────────────────────────────────────────+                                   |
|                 │                                                                       |
|                 │ 2. Unchecked Database Query:                                          |
|                 │    SELECT * FROM customers WHERE id = 1;                              |
|                 ▼                                                                       |
|  +──────────────────────────────────────────────────+                                   |
|  |                 Backend Database                 |                                   |
|  | Holds Records for: User 1 (adam84), User 3 (john)|                                   |
|  +──────────────────────────────────────────────────+                                   |
|                 │                                                                       |
|                 ▼ 3. Returns User 1's Private PII Data                                  |
|  [ Attacker receives adam84's data -> IDOR COMPLETED! ]                                 |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Section 04: Prerequisites, Tooling & Environment Setup

### 1. Conceptual Knowledge:
- Understanding of HTTP request/response flows and REST API JSON serialization.
- Basic familiarity with Base64 encoding/decoding and cryptographic hash functions (MD5, SHA-1).
- Understanding of web session management and relational database structures.

### 2. Testing Utilities & Setup:
- Intercepting HTTP Proxy: Burp Suite (Proxy, Repeater, and Autorize extension).
- Browser Developer Tools (Network and Console tabs).
- Linux terminal tools: `base64`, `md5sum`, `hashid`, `curl`.

---

## Section 05: Theoretical Deep Dive

### 1. The Core Nature of IDOR:
An Insecure Direct Object Reference (IDOR) is an access control vulnerability that occurs when a web application uses client-supplied input to directly access database objects or files without verifying that the requesting user possesses authorization to interact with that specific object.

### 2. Authentication vs. Authorization Breakdown:
- **Authentication:** Confirms the identity of the client ("Who are you?").
- **Authorization:** Confirms the permissions of the client ("Are you allowed to access this object?").
- In an IDOR vulnerability, authentication succeeds (the user possesses a valid session cookie), but authorization is completely absent.

### 3. Classification in Industry Frameworks:
- **OWASP Top 10:2021:** Ranked at #1 under Category A01: **Broken Access Control**.
- **OWASP API Security Top 10:** Ranked at #1 under API1:2023: **Broken Object Level Authorization (BOLA)**.
- **MITRE CWE:** Classified under **CWE-639: Authorization Bypass Through User-Controlled Key**.

---

## Section 06: Reconnaissance & Attack Surface Mapping

To uncover IDOR vulnerabilities across enterprise applications:

1. **Examine Query String Parameters:** Inspect parameters like `?id=`, `?user_id=`, `?account=`, `?order=`, `?doc=`.
2. **Inspect RESTful Path Segments:** Examine API paths containing numerical or resource identifiers (e.g. `/api/v2/users/42/invoices/105`).
3. **Audit JSON Request Payloads:** Review POST, PUT, and PATCH requests for object reference keys:
   ```json
   { "account_id": 13, "email": "test@thm.com" }
   ```
4. **Monitor Asynchronous Background Calls:** Open DevTools Network tab while navigating the UI to capture dynamic XHR/Fetch API calls that never appear in the address bar.

---

## Section 07: Vulnerability Analysis & Root Cause

### 1. Interactive Example Site (Task 02):
- **Root Cause:** Direct reliance on client-supplied URL parameters:
  ```php
  $user_id = $_GET['user_id'];
  $profile = db_query("SELECT * FROM profiles WHERE id = ?", [$user_id]);
  ```
  The application fails to verify whether `$_SESSION['user_id'] === $user_id`.

### 2. Acme IT Support Portal (Task 07):
- **Root Cause:** The backend REST endpoint `/api/v1/customer?id={id}` accepts the `id` parameter as an absolute lookup key, blindly returning any customer record requested without validating that the authenticated caller owns the record.

---

## Section 08: Step-by-Step Exploitation & Flag Capture

### Scenario 1: Plaintext URL Parameter Manipulation (Task 02)
1. Open the interactive lab page via **View Site**.
2. Note your profile URL:
   `http://online-service.thm/profile?user_id=1305`
3. Modify the parameter to target User 1000:
   `http://online-service.thm/profile?user_id=1000`
4. The server returns User 1000's profile card containing Flag 1:
   > **Flag 1:** `THM{IDOR-VULN-FOUND}`

---

### Scenario 2: REST API Background Request Exploitation (Task 07)
1. Register a customer account and log into the Acme IT Support portal.
2. Open Developer Tools (`F12`), select the **Network** tab, and navigate to **Your Account**.
3. Identify the background request:
   `GET /api/v1/customer?id=13`
4. Replay the request with `id=1` using DevTools or `curl`:
   ```bash
   curl -s "https://LAB_URL.p.thmlabs.com/api/v1/customer?id=1" -H "Cookie: session=..."
   ```
   - Server Response:
     ```json
     { "id": 1, "username": "adam84", "email": "adam@fakemail.thm" }
     ```
   > **Target 1 Username:** `adam84`

5. Replay the request with `id=3`:
   ```bash
   curl -s "https://LAB_URL.p.thmlabs.com/api/v1/customer?id=3" -H "Cookie: session=..."
   ```
   - Server Response:
     ```json
     { "id": 3, "username": "john", "email": "j@fakemail.thm" }
     ```
   > **Target 3 Email:** `j@fakemail.thm`

---

## Section 09: Defense-in-Depth & Hardening Strategies

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           DEFENSE-IN-DEPTH REMEDIATION MODEL                            |
|                                                                                         |
|  [ Layer 1: Implicit Session Derivation ]                                               |
|  - Derive the active user ID strictly from the server-side session:                     |
|    `$current_user = $_SESSION['user_id'];`                                              |
|                                                                                         |
|  [ Layer 2: Query-Level Ownership Scoping ]                                             |
|  - Restrict database queries to enforce ownership in the SQL WHERE clause:             |
|    `SELECT * FROM invoices WHERE id = :invoice_id AND customer_id = :session_user_id`   |
|                                                                                         |
|  [ Layer 3: Indirect Reference Maps ]                                                   |
|  - Map internal database IDs to random, temporary, session-bound tokens before rendering.|
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Section 10: Detection, Threat Hunting & SIEM Rules

### 1. Web Access Log Auditing:
Inspect web access logs for rapid sequential requests targeting diverse object IDs from a single IP or session:
```bash
awk '$7 ~ /\/api\/v1\/customer\?id=/ {print $1, $7}' /var/log/nginx/access.log | sort | uniq -c | sort -nr
```

### 2. Sigma Detection Rule:
```yaml
title: Rapid Sequential Object ID Enumeration (Potential IDOR / BOLA)
id: 8b1c4e7a-2d3f-4a6b-9e0c-37a5f8b91c2d
status: stable
description: Detects rapid sequential object identifier modification indicative of automated IDOR exploitation.
logsource:
  category: webserver
detection:
  selection:
    cs-method: 'GET'
    cs-uri-stem|contains:
      - '/api/v1/customer'
      - '/profile'
      - '/invoices'
  condition: selection | count() by c-ip > 50
timeframe: 1m
level: high
```

---

## Section 11: Common Pitfalls & Troubleshooting

1. **Equating Obfuscation with Security:**
   - *Pitfall:* Assuming Base64 encoding or MD5 hashing protects an identifier.
   - *Reality:* Encoding is fully reversible, and predictable hashes are reversed in milliseconds using lookup tables.
2. **Frontend-Only Controls:**
   - *Pitfall:* Hiding edit buttons or links in the UI for unauthorized users.
   - *Reality:* Attackers intercept traffic via Burp Suite and send the raw HTTP/JSON requests directly.
3. **Overlooking Mutation Methods (PUT / DELETE):**
   - *Pitfall:* Enforcing authorization on `GET` requests while leaving `DELETE` or `PUT` endpoints unprotected.
   - *Reality:* Always verify permissions across all HTTP verbs.

---

## Section 12: Real-World Case Studies & CVE Mapping

1. **First American Financial Corporation (2019):**
   - Over 885 million mortgage documents leaked due to an IDOR vulnerability where modifying a sequential digit in the URL accessed other customers' tax filings, bank account numbers, and social security records.
2. **Facebook Photo Album Deletion ($12,500 Reward):**
   - An IDOR vulnerability in the Graph API permitted any user to delete any photo album by providing the album ID to an unvalidated deletion endpoint.
3. **Standard Classifications:**
   - **CWE-639:** Authorization Bypass Through User-Controlled Key.
   - **OWASP Top 10:2021:** A01:2021 - Broken Access Control.
   - **OWASP API Top 10:** API1:2023 - Broken Object Level Authorization (BOLA).

---

## Section 13: Summary & Answers Matrix

| Task Number | Focus Concept | Platform Question | Final Accepted Answer |
| :--- | :--- | :--- | :--- |
| **Task 01** | Acronym Definition | What does IDOR stand for? | `Insecure Direct Object Reference` |
| **Task 02** | Plaintext IDOR | What is the Flag from the IDOR example website? | `THM{IDOR-VULN-FOUND}` |
| **Task 03** | Encoded IDs | What is a common type of encoding used by websites? | `base64` |
| **Task 04** | Hashed IDs | What is a common algorithm used for hashing IDs? | `md5` |
| **Task 05** | Unpredictable IDs | What is the minimum number of accounts needed to check for IDORs? | `2` |
| **Task 06** | Vector Locations | Read the above. | `No answer needed` |
| **Task 07** | Practical User 1 | What is the username for user id 1? | `adam84` |
| | Practical User 3 | What is the email address for user id 3? | `j@fakemail.thm` |

---

## Section 14: Practical Exercises & Challenge Extensions

1. **Exercise 1: Burp Suite Autorize Setup:**
   - Install the Autorize extension in Burp Suite, configure Low-Privilege user cookies, and perform a full crawl to detect IDOR flaws automatically.
2. **Exercise 2: Automated BOLA Scanner in Python:**
   - Develop a Python script using `requests` that creates two test accounts and attempts to access Account A's resources using Account B's session token.
3. **Exercise 3: Framework Authorization Policy Implementation:**
   - Implement a Laravel Policy or Django Object-Level Permission rule enforcing strict ownership checks and verify that all cross-account requests return HTTP 403 Forbidden.
