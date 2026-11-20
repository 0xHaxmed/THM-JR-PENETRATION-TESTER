# Technical References & Documentation: Support Challenge & Chained Web Exploitation

---

## 1. Industry Standards & Threat Classifications

- **MITRE ATT&CK Framework:**
  - [Technique T1190: Exploit Public-Facing Application](https://attack.mitre.org/techniques/T1190/)
  - [Technique T1059.004: Command and Scripting Interpreter - Unix Shell](https://attack.mitre.org/techniques/T1059/004/)
  - [Technique T1548.003: Abuse Elevation Control Mechanism - Sudo and Sudoers](https://attack.mitre.org/techniques/T1548/003/)
  - [Technique T1078.003: Valid Accounts - Local Accounts](https://attack.mitre.org/techniques/T1078/003/)
- **Common Weakness Enumeration (CWE):**
  - [CWE-915: Improperly Controlled Modification of Dynamically-Determined Object Attributes (Mass Assignment)](https://cwe.mitre.org/data/definitions/915.html)
  - [CWE-78: Improper Neutralization of Special Elements used in an OS Command ('OS Command Injection')](https://cwe.mitre.org/data/definitions/78.html)
  - [CWE-250: Execution with Unnecessary Privileges](https://cwe.mitre.org/data/definitions/250.html)
  - [CWE-284: Improper Access Control](https://cwe.mitre.org/data/definitions/284.html)
- **OWASP Application Security Verification Standard (ASVS):**
  - Section V5: Validation, Sanitization and Encoding Verification Requirements (V5.2 - OS Command Injection)
  - Section V4: Access Control Verification Requirements (V4.1 - General Access Control)
  - Section V1: Architecture, Design and Threat Modeling

---

## 2. Defensive Cheat Sheets & Engineering Guidelines

- **OWASP Cheat Sheet Series:**
  - [OWASP Command Injection Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html)
  - [OWASP Mass Assignment Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html)
  - [OWASP REST Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html)
  - [OWASP Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
- **Linux Security & Hardening:**
  - [Sudoers Manual (`sudoers(5)`) - Man7.org](https://man7.org/linux/man-pages/man5/sudoers.5.html)
  - [Ubuntu Security Guide: Principle of Least Privilege](https://ubuntu.com/security)

---

## 3. High-Quality Exploitation & Methodology References

- **PortSwigger Web Security Academy:**
  - [OS Command Injection Labs & Bypass Techniques](https://portswigger.net/web-security/os-command-injection)
  - [Mass Assignment Vulnerabilities & API Security](https://portswigger.net/web-security/api-testing)
  - [Access Control Vulnerabilities and Privilege Escalation](https://portswigger.net/web-security/access-control)
- **GTFOBins & Shell Stabilization:**
  - [GTFOBins: Unix Binaries That Can Be Used to Bypass Local Security Restrictions](https://gtfobins.github.io/)
  - [PayloadsAllTheThings: Command Injection Payloads](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)
  - [PayloadsAllTheThings: Reverse Shell Cheatsheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
  - [Netsec: Upgrading Simple Shells to Fully Interactive TTYs](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/)

---

## 4. Notable Real-World Incident Case Studies

1. **GitHub Enterprise Mass Assignment Vulnerability (Egor Homakov, 2012):**
   - The classic demonstration of Mass Assignment where an attacker appended their public SSH key to the Rails organization administrator account by adding an unrestricted attribute (`user[public_key]`) in a standard update request.
2. **Atlassian Confluence OGNL & Command Injections (CVE-2022-26134, CVE-2021-26084):**
   - Exploitation of internal administrative utilities and web endpoints allowing unauthenticated attackers to evaluate arbitrary Java OGNL expressions and invoke operating system shells directly.
