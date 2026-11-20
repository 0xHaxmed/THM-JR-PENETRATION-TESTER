# Technical References & Documentation: Recruit Challenge & Advanced Web Attacks

---

## 1. Industry Standards & Threat Classifications

- **MITRE ATT&CK Framework:**
  - [Technique T1190: Exploit Public-Facing Application](https://attack.mitre.org/techniques/T1190/)
  - [Technique T1059.006: Command and Scripting Interpreter - Python/PHP](https://attack.mitre.org/techniques/T1059/006/)
  - [Technique T1078.003: Valid Accounts - Local Accounts](https://attack.mitre.org/techniques/T1078/003/)
- **Common Weakness Enumeration (CWE):**
  - [CWE-434: Unrestricted Upload of File with Dangerous Type](https://cwe.mitre.org/data/definitions/434.html)
  - [CWE-639: Authorization Bypass Through User-Controlled Key (IDOR)](https://cwe.mitre.org/data/definitions/639.html)
  - [CWE-640: Weak Password Recovery Mechanism for Forgotten Password](https://cwe.mitre.org/data/definitions/640.html)
  - [CWE-284: Improper Access Control](https://cwe.mitre.org/data/definitions/284.html)
- **OWASP Application Security Verification Standard (ASVS):**
  - Section V4: Access Control Verification Requirements
  - Section V12: File and Resources Verification Requirements (V12.1 - File Uploads)
  - Section V2: Authentication Verification Requirements (V2.5 - Credential Recovery)

---

## 2. Defensive Cheat Sheets & Engineering Guidelines

- **OWASP Cheat Sheet Series:**
  - [OWASP File Upload Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html)
  - [OWASP Forgot Password Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html)
  - [OWASP Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
- **Hardening References:**
  - [Nginx Official Documentation: Securing Upload Directories](https://docs.nginx.com/)
  - [PHP: Core Security Configuration (`disable_functions`, `engine off`)](https://www.php.net/manual/en/security.php)

---

## 3. High-Quality Exploitation & Methodology References

- **PortSwigger Web Security Academy:**
  - [File Upload Vulnerabilities Labs & Path Traversal](https://portswigger.net/web-security/file-upload)
  - [Flawed Two-Factor Authentication & Password Reset Logic](https://portswigger.net/web-security/authentication)
  - [Bypassing Access Controls and IDOR in Multi-Step Workflows](https://portswigger.net/web-security/access-control)
- **Community Repositories:**
  - [PayloadsAllTheThings: File Upload Filter Bypasses](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files)
  - [SecLists: Common Web Discovery & Extension Wordlists](https://github.com/danielmiessler/SecLists)

---

## 4. Notable Real-World Applicant Tracking System (ATS) Disclosures

1. **Lever / Greenhouse ATS Attachment Disclosures (Historical):**
   - Multiple bug bounty reports detailing how unauthenticated candidate application endpoints permitted uploading active HTML/SVG files or executable scripts that rendered within internal recruiter sessions.
2. **WordPress Plugin "WP-Job-Manager" Unrestricted Upload (CVE-2015-6967):**
   - Vulnerability where unauthenticated resume submissions allowed uploading arbitrary `.php` files to the public `wp-content/uploads/` directory, resulting in full remote code execution.
3. **Workday Partner Portal IDOR (Historical):**
   - Access control bypass where tampering with applicant interview IDs exposed confidential evaluation matrices and offer letters across organizational boundaries.
