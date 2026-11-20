# Technical References & Documentation: Server-Side Request Forgery (SSRF)

---

## 1. Industry Standards & Threat Classifications

- **MITRE ATT&CK Framework:**
  - [Technique T1190: Exploit Public-Facing Application](https://attack.mitre.org/techniques/T1190/)
  - [Technique T1071.001: Application Layer Protocol - Web Protocols](https://attack.mitre.org/techniques/T1071/001/)
  - [Technique T1552.005: Unsecured Credentials - Cloud Instance Metadata API](https://attack.mitre.org/techniques/T1552/005/)
- **Common Weakness Enumeration (CWE):**
  - [CWE-918: Server-Side Request Forgery (SSRF)](https://cwe.mitre.org/data/definitions/918.html)
  - [CWE-22: Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal')](https://cwe.mitre.org/data/definitions/22.html)
  - [CWE-367: Time-of-Check to Time-of-Use (TOCTOU) Race Condition](https://cwe.mitre.org/data/definitions/367.html)
- **OWASP Top 10 & Testing Guides:**
  - [OWASP Top 10:2021 Category A10: Server-Side Request Forgery (SSRF)](https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/)
  - [OWASP SSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html)
  - [OWASP Web Security Testing Guide: Testing for SSRF (WSTG-INPV-19)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/19-Testing_for_Server_Side_Request_Forgery)

---

## 2. Technical RFCs & Protocol Specifications

- **IETF RFC 3927:**
  - *Dynamic Configuration of IPv4 Link-Local Addresses*
  - Defines the `169.254.0.0/16` network prefix reserved for communication between devices on a single physical link.
  - Link: [datatracker.ietf.org/doc/html/rfc3927](https://datatracker.ietf.org/doc/html/rfc3927)
- **IETF RFC 3986:**
  - *Uniform Resource Identifier (URI): Generic Syntax*
  - Outlines parsing rules, authority separators (`@`), query strings (`?`, `&`), and path normalizations.
  - Link: [datatracker.ietf.org/doc/html/rfc3986](https://datatracker.ietf.org/doc/html/rfc3986)
- **IETF RFC 1918:**
  - *Address Allocation for Private Internets* (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`).

---

## 3. High-Quality Educational & Research Resources

- **PortSwigger Web Security Academy:**
  - [Server-Side Request Forgery (SSRF) Labs & Tutorials](https://portswigger.net/web-security/ssrf)
  - [Blind SSRF Vulnerabilities & Exploitation](https://portswigger.net/web-security/ssrf/blind)
  - [Bypassing SSRF Filters & Defenses](https://portswigger.net/web-security/ssrf#bypassing-ssrf-defenses)
- **Cloud Security Documentation:**
  - [AWS Documentation: Transitioning to IMDSv2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html)
  - [Google Cloud Documentation: Storing and Retrieving Instance Metadata](https://cloud.google.com/compute/docs/metadata/overview)
  - [Azure Virtual Machines: Instance Metadata Service (IMDS)](https://learn.microsoft.com/en-us/azure/virtual-machines/instance-metadata-service)
- **Exploitation Guides:**
  - [PayloadsAllTheThings: Server Side Request Forgery (SSRF)](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery)
  - [Assetnote: SSRF Bible - Cheatsheet & Vulnerability Guide](https://assetnote.io/)

---

## 4. Notable Real-World Case Studies

1. **The 2019 Capital One Cloud Breach:**
   - A misconfigured ModSecurity WAF EC2 instance had permissions to assume an overly permissive IAM role. The attacker leveraged SSRF to query IMDSv1, extracted the temporary credentials, and downloaded over 30 million customer records from Amazon S3 buckets.
2. **Microsoft Exchange Server SSRF (CVE-2021-26855 - "ProxyLogon"):**
   - An SSRF vulnerability in the Exchange Frontend transport architecture allowed unauthenticated attackers to forge backend HTTP requests with arbitrary Kerberos authentication tickets, escalating to full Remote Code Execution.
3. **GitLab SSRF to RCE via Webhooks (Historical):**
   - Vulnerability where internal project webhooks could target local Redis services via `gopher://` or internal Elasticsearch clusters, leading to arbitrary code execution.
