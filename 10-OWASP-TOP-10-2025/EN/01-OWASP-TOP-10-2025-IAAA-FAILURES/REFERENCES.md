# References & Authoritative Documentation: OWASP Top 10 2025 - IAAA Failures (المراجع والتوثيق الرسمي)

---

## 1. Official Standards & Industry Frameworks

### 1.1 OWASP (Open Web Application Security Project)
- **OWASP Top 10:2025 - A01 Broken Access Control:**
  - URL: [https://owasp.org/Top10/A01_2021-Broken_Access_Control/](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
  - Focus: Authorization bypass, horizontal & vertical privilege escalation, IDOR, CORS misconfiguration.
- **OWASP Top 10:2025 - A07 Identification and Authentication Failures:**
  - URL: [https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/)
  - Focus: Credential stuffing, brute-force attacks, session fixation, account collision flaws.
- **OWASP Top 10:2025 - A09 Security Logging and Monitoring Failures:**
  - URL: [https://owasp.org/Top10/A09_2021-Security_Logging_and_Monitoring_Failures/](https://owasp.org/Top10/A09_2021-Security_Logging_and_Monitoring_Failures/)
  - Focus: Missing audit trails, inadequate alerting thresholds, log tampering, unmonitored administrative actions.
- **OWASP Web Security Testing Guide (WSTG):**
  - WSTG-ATHN-01 to WSTG-ATHN-10: Authentication Testing.
  - WSTG-ATHZ-01 to WSTG-ATHZ-04: Authorization Testing.

### 1.2 NIST (National Institute of Standards and Technology)
- **NIST Special Publication 800-63-3 / 800-63B:**
  - Title: *Digital Identity Guidelines - Authentication and Lifecycle Management*
  - Document: [https://csrc.nist.gov/publications/detail/sp/800-63/3/final](https://csrc.nist.gov/publications/detail/sp/800-63/3/final)
  - Key sections: Password complexity standards, multi-factor authentication (MFA), account lockout vs rate-limiting guidelines.
- **NIST SP 800-92:**
  - Title: *Guide to Computer Security Log Management*
  - Document: [https://csrc.nist.gov/publications/detail/sp/800-92/final](https://csrc.nist.gov/publications/detail/sp/800-92/final)

---

## 2. Common Weakness Enumeration (CWE) & RFCs

- **CWE-284: Improper Access Control:**
  - URL: [https://cwe.mitre.org/data/definitions/284.html](https://cwe.mitre.org/data/definitions/284.html)
- **CWE-639: Authorization Bypass Through User-Controlled Key (IDOR):**
  - URL: [https://cwe.mitre.org/data/definitions/639.html](https://cwe.mitre.org/data/definitions/639.html)
- **CWE-178: Improper Handling of Case Sensitivity:**
  - URL: [https://cwe.mitre.org/data/definitions/178.html](https://cwe.mitre.org/data/definitions/178.html)
- **CWE-778: Insufficient Logging:**
  - URL: [https://cwe.mitre.org/data/definitions/778.html](https://cwe.mitre.org/data/definitions/778.html)
- **RFC 5424:**
  - Title: *The Syslog Protocol*
  - URL: [https://datatracker.ietf.org/doc/html/rfc5424](https://datatracker.ietf.org/doc/html/rfc5424)

---

## 3. Platform & Target Information

- **TryHackMe Target Room:**
  - Title: OWASP Top 10: 2025 - IAAA Failures
  - Slug: `owasptopten2025one`
  - URL: [https://tryhackme.com/room/owasptopten2025one](https://tryhackme.com/room/owasptopten2025one)
  - Module: Module 10 (OWASP Top 10: 2025)
  - Learning Path: Jr Penetration Tester (PT1 Aligned Curriculum)
