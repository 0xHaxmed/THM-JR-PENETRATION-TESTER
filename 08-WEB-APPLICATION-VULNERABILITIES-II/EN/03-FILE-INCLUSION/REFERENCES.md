# References & Authoritative Documentation: File Inclusion (المراجع والتوثيق المعتمد)

---

## 1. المعايير والمواصفات القياسية (Standards & Specifications)

* **CWE - Common Weakness Enumeration:**
  - **CWE-22:** Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal').
    - التصنيف المعياري لثغرات اجتياز المسارات واستخدام الرموز الخاصة (`../`).
    - الرابط: https://cwe.mitre.org/data/definitions/22.html
  - **CWE-98:** Improper Control of Generation of Code ('PHP Remote File Inclusion').
    - التصنيف المعياري لثغرات التضمين البعيد للأكواد البرمجية.
    - الرابط: https://cwe.mitre.org/data/definitions/98.html
  - **CWE-73:** External Control of File Name or Path.
    - الرابط: https://cwe.mitre.org/data/definitions/73.html

* **PHP Official Documentation - Supported Protocols and Wrappers:**
  - التوثيق الرسمي لأغلفة التدفق في لغة PHP (`php://`, `data://`, `file://`, `phar://`):
  - الرابط: https://www.php.net/manual/en/wrappers.php

---

## 2. أدلة مجتمع أمان تطبيقات الويب (OWASP Guidelines)

* **OWASP Testing Guide (WSTG) - WSTG-INPV-11: Testing for Path Traversal:**
  - الدليل المعياري لاختبار ثغرات اجتياز المسارات واكتشاف المعلمات المعيبة وتقنيات كشف الملفات:
  - الرابط: https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion
  - الرابط: https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.2-Testing_for_Remote_File_Inclusion

* **OWASP File Inclusion Vulnerabilities & Path Traversal Cheat Sheet:**
  - الإرشادات الهندسية للمطورين لمنع ثغرات تضمين الملفات وفرض القوائم البيضاء:
  - الرابط: https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html

* **OWASP Top 10 (2021):**
  - A01:2021 - Broken Access Control.
  - A03:2021 - Injection.
  - الرابط: https://owasp.org/Top10/A01_2021-Broken_Access_Control/

---

## 3. مختبرات ومنصات البحث المتقدم (Research & Labs)

* **PortSwigger Web Security Academy - Directory Traversal:**
  - شروحات ومختبرات تفاعلية متقدمة في اجتياز المسارات، والتجاوز التشفيري، وقراءة الملفات الحساسة:
  - الرابط: https://portswigger.net/web-security/file-path-traversal

* **PayloadsAllTheThings - File Inclusion / Path Traversal:**
  - المستودع العالمي الأشمل لحمولات وتجاوزات LFI و RFI وأغلفة PHP:
  - الرابط: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion
