# Technical References & Documentation: Insecure Direct Object References (IDOR) & BOLA

---

## 1. Industry Standards & Threat Classifications

- **MITRE ATT&CK Framework:**
  - [Technique T1078: Valid Accounts](https://attack.mitre.org/techniques/T1078/)
  - [Technique T1552: Unsecured Credentials](https://attack.mitre.org/techniques/T1552/)
- **Common Weakness Enumeration (CWE):**
  - [CWE-639: Authorization Bypass Through User-Controlled Key](https://cwe.mitre.org/data/definitions/639.html)
  - [CWE-284: Improper Access Control](https://cwe.mitre.org/data/definitions/284.html)
  - [CWE-285: Improper Authorization](https://cwe.mitre.org/data/definitions/285.html)
- **OWASP Standards & Testing Methodologies:**
  - [OWASP Top 10:2021 Category A01: Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
  - [OWASP API Security Top 10: API1:2023 - Broken Object Level Authorization (BOLA)](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)
  - [OWASP Web Security Testing Guide: Testing for Insecure Direct Object References (WSTG-ATHZ-04)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References)
  - [OWASP Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)

---

## 2. Academic Papers & Architectural Models

- **Google Zanzibar: A Consistent, Global Authorization System:**
  - Authored by Pang et al. (Google Inc., USENIX ATC 2019).
  - Outlines the modern relationship-based access control (ReBAC) model used to govern billions of object-level permissions across Google Docs, YouTube, and Cloud.
  - Link: [research.google/pubs/pub48190/](https://research.google/pubs/pub48190/)
- **NIST Special Publication 800-162:**
  - *Guide to Attribute Based Access Control (ABAC) Definition and Considerations*
  - Detailed governmental framework for enforcing dynamic, contextual access evaluation.

---

## 3. High-Quality Educational & Research Resources

- **PortSwigger Web Security Academy:**
  - [Insecure Direct Object References (IDOR) Tutorials & Interactive Labs](https://portswigger.net/web-security/access-control/idor)
  - [Access Control Vulnerabilities & Privilege Escalation](https://portswigger.net/web-security/access-control)
- **Specialized Auditing Tooling:**
  - [Autorize (Burp Suite BApp Store)](https://github.com/Quitten/Autorize) - Automated authorization auditing extension.
  - [Arjun (HTTP Parameter Discovery Suite)](https://github.com/s0md3v/Arjun) - Automated parameter mining.

---

## 4. Notable Real-World IDOR Breaches & Bug Bounties

1. **First American Financial Corporation (2019):**
   - A catastrophic IDOR flaw in a web portal exposed over **885 million** sensitive mortgage and title documents dating back to 2003. Anyone with a valid link could change a sequential digit in the URL to view other customers' social security numbers, bank statements, and tax records.
2. **Uber Account Takeover via IDOR (Anand Prakash - $10,000 Reward):**
   - The security researcher discovered an IDOR on a phone number verification endpoint where modifying the user ID parameter returned the active session token of any Uber driver or rider, enabling complete account takeover.
3. **Facebook Photo Deletion IDOR (Laxman Muthiyah - $12,500 Reward):**
   - An IDOR vulnerability in the Facebook Graph API allowed any user to delete any public or private photo album belonging to any other user or page by supplying the album ID to a deletion endpoint.
