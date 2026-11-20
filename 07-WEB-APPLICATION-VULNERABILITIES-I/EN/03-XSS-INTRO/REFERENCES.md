# Technical References & Documentation: Cross-Site Scripting (XSS)

---

## 1. Industry Standards & Threat Classifications

- **MITRE ATT&CK Framework:**
  - [Technique T1189: Drive-by Compromise](https://attack.mitre.org/techniques/T1189/)
  - [Technique T1059.007: Command and Scripting Interpreter - JavaScript](https://attack.mitre.org/techniques/T1059/007/)
  - [Technique T1539: Steal Web Session Cookie](https://attack.mitre.org/techniques/T1539/)
- **Common Weakness Enumeration (CWE):**
  - [CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')](https://cwe.mitre.org/data/definitions/79.html)
  - [CWE-80: Improper Neutralization of Script-Related HTML Tags in a Web Page (Basic XSS)](https://cwe.mitre.org/data/definitions/80.html)
  - [CWE-83: Improper Neutralization of Script in Attributes in a Web Page](https://cwe.mitre.org/data/definitions/83.html)
- **OWASP Standards & Testing Methodologies:**
  - [OWASP Top 10:2021 Category A03: Injection](https://owasp.org/Top10/A03_2021-Injection/)
  - [OWASP Web Security Testing Guide: Testing for Reflected XSS (WSTG-INPV-01)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting)
  - [OWASP Web Security Testing Guide: Testing for Stored XSS (WSTG-INPV-02)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/02-Testing_for_Stored_Cross_Site_Scripting)
  - [OWASP Web Security Testing Guide: Testing for DOM-Based XSS (WSTG-CLNT-01)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/01-Testing_for_DOM-Based_Cross_Site_Scripting)

---

## 2. Defensive Cheat Sheets & Engineering Guidelines

- **OWASP Cheat Sheet Series:**
  - [Cross-Site Scripting Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
  - [DOM-Based XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)
  - [Content Security Policy (CSP) Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html)
- **W3C Specifications:**
  - [W3C Content Security Policy Level 3](https://www.w3.org/TR/CSP3/)
  - [W3C Document Object Model (DOM) Technical Architecture](https://dom.spec.whatwg.org/)

---

## 3. High-Quality Research & Exploitation Repositories

- **PortSwigger Web Security Academy:**
  - [Cross-Site Scripting (XSS) Learning Path & Laboratories](https://portswigger.net/web-security/cross-site-scripting)
  - [DOM XSS Sources and Sinks Index](https://portswigger.net/web-security/cross-site-scripting/dom-based)
  - [PortSwigger XSS Cheat Sheet (Complete Filter Evasion Matrix)](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
- **Community Repositories:**
  - [PayloadsAllTheThings: XSS Injection Repository](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection)
  - [XSS Hunter Express (Automated Blind XSS Callback Framework)](https://github.com/mandatoryprogrammer/xsshunter-express)
  - [DOMPurify: Fast, tolerant DOM-only (XSS sanitizer) for HTML](https://github.com/cure53/DOMPurify)

---

## 4. Landmark Historical Case Studies & Bug Bounties

1. **The Samy MySpace Worm (2005):**
   - The first major global demonstration of Stored XSS worm propagation, infecting over one million user profiles within 20 hours through client-side DOM injection.
2. **Steam Chat Zero-Click Stored XSS ($7,500 Reward):**
   - A critical vulnerability where sending a crafted message to a user over Steam chat executed arbitrary JavaScript upon rendering the chat bubble, allowing full profile takeover.
3. **Shopify Currency Symbol Reflected XSS:**
   - Demonstrating how reflection inside template engine variables enabled administrative session hijacking.
4. **Yahoo Mail Stored XSS (2015 / $10,000 Reward):**
   - Vulnerability in HTML email body parser that allowed persistent JavaScript execution whenever a victim viewed an email.
