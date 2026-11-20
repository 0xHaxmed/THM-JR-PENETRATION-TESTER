# References & Authoritative Documentation: Command Injection (المراجع والتوثيق المعتمد)

---

## 1. المعايير والمواصفات القياسية (Standards & Specifications)

* **CWE - Common Weakness Enumeration:**
  - **CWE-78:** Improper Neutralization of Special Elements used in an OS Command ('OS Command Injection').
    - التصنيف المعياري الأساسي لثغرات حقن أوامر نظام التشغيل.
    - الرابط: https://cwe.mitre.org/data/definitions/78.html
  - **CWE-88:** Improper Neutralization of Argument Delimiters in a Command ('Argument Injection').
    - التصنيف المعياري لثغرات حقن وسائط الأوامر.
    - الرابط: https://cwe.mitre.org/data/definitions/88.html

* **MITRE ATT&CK Framework:**
  - **Technique T1059:** Command and Scripting Interpreter (تكتيك تنفيذ الأوامر عبر مفسرات الشل):
    - T1059.004: Unix Shell
    - T1059.001: PowerShell
    - T1059.003: Windows Command Shell
    - الرابط: https://attack.mitre.org/techniques/T1059/

---

## 2. أدلة مجتمع أمان تطبيقات الويب (OWASP Guidelines)

* **OWASP Testing Guide (WSTG) - WSTG-INPV-12: Testing for Command Injection:**
  - الدليل الميداني الشامل لفحص واكتشاف ثغرات حقن الأوامر الصريحة والعمياء:
  - الرابط: https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/12-Testing_for_Command_Injection

* **OWASP OS Command Injection Defense Cheat Sheet:**
  - الإرشادات الهندسية للمطورين لتفادي استدعاءات الشل المباشرة وتطبيق المعاملات الآمنة:
  - الرابط: https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html

* **OWASP Top 10 (2021) - A03: Injection:**
  - تصنيف ثغرات الحقن (بما فيها حقن الأوامر و SQLi) ضمن أخطر 3 تهديدات لتطبيقات الويب عالمياً:
  - الرابط: https://owasp.org/Top10/A03_2021-Injection/

---

## 3. مختبرات ومنصات البحث المتقدم (Research & Labs)

* **PortSwigger Web Security Academy - OS Command Injection:**
  - شروحات ومختبرات تفاعلية متقدمة تغطي الحقن المرئي، والتأخير الزمني، وإعادة توجيه الملفات، وقنوات OOB:
  - الرابط: https://portswigger.net/web-security/os-command-injection

* **PayloadsAllTheThings - Command Injection:**
  - المستودع العالمي الأشمل لحمولات وتجاوزات حقن أوامر نظام التشغيل:
  - الرابط: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection
