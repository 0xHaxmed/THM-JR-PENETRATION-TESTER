# Technical References & Documentation: Cross-Site Request Forgery (CSRF)

---

## 1. Industry Standards & Threat Classifications

- **MITRE ATT&CK Framework:**
  - [Sub-technique T1189: Drive-by Compromise](https://attack.mitre.org/techniques/T1189/)
  - [Technique T1539: Steal Web Session Cookie](https://attack.mitre.org/techniques/T1539/)
- **Common Weakness Enumeration (CWE):**
  - [CWE-352: Cross-Site Request Forgery (CSRF)](https://cwe.mitre.org/data/definitions/352.html)
  - [CWE-613: Insufficient Session Expiration](https://cwe.mitre.org/data/definitions/613.html)
  - [CWE-330: Use of Insufficiently Random Values](https://cwe.mitre.org/data/definitions/330.html)
- **OWASP Top 10 & Verification Standards:**
  - [OWASP Top 10:2021 Category A01: Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
  - [OWASP Web Security Testing Guide (WSTG-CLNT-05: Testing for CSRF)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/05-Testing_for_Cross_Site_Request_Forgery)
  - [OWASP CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)

---

## 2. Technical RFCs & Protocol Specifications

- **IETF RFC 6265bis:**
  - *Cookies: HTTP State Management Mechanism (Draft-ietf-httpbis-rfc6265bis)*
  - Defines cookie scopes, paths, lifecycles, and the formal behavior of the `SameSite` attribute (`Strict`, `Lax`, `None`).
  - Link: [tools.ietf.org/html/draft-ietf-httpbis-rfc6265bis](https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-rfc6265bis)
- **IETF RFC 7231 (HTTP/1.1 Semantics and Content):**
  - Section 4.2.1: Safe Methods (Definitions of Idempotency for GET, HEAD, OPTIONS).
  - Link: [datatracker.ietf.org/doc/html/rfc7231#section-4.2.1](https://datatracker.ietf.org/doc/html/rfc7231#section-4.2.1)
- **W3C Fetch Specification:**
  - Details cross-origin request handling, credential flags (`omit`, `same-origin`, `include`), and preflight check mechanisms.
  - Link: [fetch.spec.whatwg.org](https://fetch.spec.whatwg.org/)

---

## 3. High-Quality Educational & Research Resources

- **PortSwigger Web Security Academy:**
  - [Cross-site Request Forgery (CSRF) Tutorials & Vulnerability Labs](https://portswigger.net/web-security/csrf)
  - [CSRF Tokens & Validation Vulnerabilities](https://portswigger.net/web-security/csrf/tokens)
  - [SameSite Cookie Bypass Techniques](https://portswigger.net/web-security/csrf/bypassing-samesite-cookie-restrictions)
- **Mozilla Developer Network (MDN) Web Docs:**
  - [MDN: Using HTTP Cookies & SameSite Attribute](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#samesite_attribute)
  - [MDN: Same-Origin Policy (SOP)](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)

---

## 4. Notable Historical CVE Case Studies

1. **ING Direct / Netgear Router CSRF (CVE-2008-2398 / Historical):**
   - Demonstrated how an attacker could force a victim to execute authenticated fund transfers or alter DNS router settings simply by visiting an infected webpage carrying an embedded `<img>` tag with administrative URLs.
2. **WordPress Core CSRF in Comment Moderation (Historical):**
   - Exploited administrative users browsing external blogs, automatically approving spam comments or executing unvalidated moderation actions via ambient cookies.
3. **GlassFish Application Server CSRF (CVE-2011-0807):**
   - Critical vulnerability enabling remote administrative takeover of application servers due to missing CSRF tokens in the administration console.
4. **Apache Superset CSRF Bypass (CVE-2023-37941):**
   - Misconfiguration in session token validation allowing attackers to execute unauthorized analytical queries.
