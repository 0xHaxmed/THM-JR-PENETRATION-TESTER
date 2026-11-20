# References & Authoritative Documentation: Broken Authentication (المراجع والتوثيق المعتمد)

---

## 1. المعايير والمواصفات القياسية (Standards & Specifications)

* **NIST Special Publication 800-63B - Digital Identity Guidelines:**
  - الدليل الفيدرالي الشامل لمتطلبات المصادقة الرقمية، واختيار كلمات المرور، وإدارة الجلسات، وقواعد قفل الحسابات.
  - الرابط: https://pages.nist.gov/800-63-3/sp800-63b.html

* **CWE - Common Weakness Enumeration:**
  - **CWE-287:** Improper Authentication (المصادقة غير السليمة).
  - **CWE-307:** Improper Restriction of Excessive Authentication Attempts (عدم تقييد محاولات المصادقة المفرطة).
  - **CWE-235:** Improper Handling of Extra Parameters (سوء معالجة المعلمات الزائدة - HTTP Parameter Pollution).
  - **CWE-640:** Weak Password Recovery Mechanism for Forgotten Password (آليات استعادة كلمات المرور الضعيفة).
  - الرابط: https://cwe.mitre.org/data/definitions/287.html

---

## 2. أدلة مجتمع أمان تطبيقات الويب (OWASP Guidelines)

* **OWASP Authentication Cheat Sheet:**
  - إرشادات هندسية وتطبيقية تفصيلية لتأمين نماذج الدخول، وقفل الحسابات، وفرض المصادقة متعددة العوامل.
  - الرابط: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html

* **OWASP Forgot Password Cheat Sheet:**
  - المرجع الهندسي لتصميم آليات استعادة كلمات المرور الآمنة وحماية الرموز المؤقتة ومنع التلاعب بمصادر المعلمات.
  - الرابط: https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html

* **OWASP Web Security Testing Guide (WSTG) - Authentication Testing:**
  - WSTG-ATHN-01: Testing for Credentials Transported over an Encrypted Channel.
  - WSTG-ATHN-02: Testing for Default Credentials.
  - WSTG-ATHN-03: Testing for Weak Lock Out Mechanism.
  - WSTG-ATHN-04: Testing for Bypassing Authentication Schema.
  - WSTG-ATHN-05: Testing for Vulnerable Remember Password and Password Reset.
  - WSTG-ATHN-06: Testing for Browser Cache Weaknesses.
  - الرابط: https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/03-Identity_Management_Testing/

* **OWASP Top 10 (2021) - A07: Identification and Authentication Failures:**
  - تصنيف ثغرات فشل التحقق من الهوية والمصادقة ضمن أخطر التهديدات العالمية لتطبيقات الويب.
  - الرابط: https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/

---

## 3. أدوات ومختبرات البحث المتقدم (Tools & Labs)

* **ffuf (Fuzz Faster U Fool) Official Repository:**
  - مستودع أداة التخمين السريعة والتوثيق الرسمي للخيارات والمطابقات:
  - الرابط: https://github.com/ffuf/ffuf

* **SecLists Security Wordlists:**
  - الحزمة العالمية الرائدة لقوائم أسماء المستخدمين وكلمات المرور الشائعة:
  - الرابط: https://github.com/danielmiessler/SecLists

* **PortSwigger Web Security Academy:**
  - مختبرات تفاعلية متقدمة في استغلال عيوب المصادقة وهجمات استعادة الحسابات:
  - الرابط: https://portswigger.net/web-security/authentication
