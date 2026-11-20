# المراجع والمصادر التوثيقية: دخيل بيرب سويت (Burp Suite: Intruder References)

---

## 1. التوثيق الرسمي لأداة بيرب سويت (Official PortSwigger Documentation)
* [PortSwigger Burp Suite Intruder User Guide](https://portswigger.net/burp/documentation/desktop/tools/intruder):
  الدليل المرجعي الشامل لأداة Intruder، إعداد المواضع، إدارة مجموعات الحمولات، وضبط قواعد المعالجة.
* [PortSwigger Intruder Attack Types](https://portswigger.net/burp/documentation/desktop/tools/intruder/attack-types):
  شرح مفصل لكيفية عمل أنماط الهجوم الأربعة (Sniper, Battering Ram, Pitchfork, Cluster Bomb) وسلوكيات توزيع الحمولات.
* [PortSwigger Configuring Macros and Session Handling Rules](https://portswigger.net/burp/documentation/desktop/settings/sessions/rules):
  توثيق كيفية بناء الماكرو التلقائي، استخراج المعاملات الديناميكية، ودمجها مع أدوات الفحص لتجاوز رموز CSRF.
* [PortSwigger Web Security Academy: Authentication Vulnerabilities](https://portswigger.net/web-security/authentication):
  معامل تعليمية متقدمة حول كسر المصادقة، تجاوز قيود المعدل، وهجمات Password Spraying.

---

## 2. معايير ومراجع OWASP و MITRE CWE (Security Standards)
* [OWASP Top 10: A07:2021 - Identification and Authentication Failures](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/):
  تصنيف مخاطر إخفاقات المصادقة، هجمات التخمين الشامل وحشو بيانات الاعتماد (Credential Stuffing).
* [OWASP Top 10: A01:2021 - Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/):
  مخاطر غياب ضوابط التفويض وثغرات الإسناد المباشر غير الآمن للكائنات (IDOR).
* [OWASP WSTG: Testing for Brute Force (WSTG-ATHN-04)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/03-Identity_Management_Testing/04-Testing_for_Brute_Force):
  منهجية اختبار قوة المصادقة وتجاوز آليات كشف التخمين.
* [CWE-307: Improper Restriction of Excessive Authentication Attempts](https://cwe.mitre.org/data/definitions/307.html):
  التعريف الدولي لنقاط الضعف الناتجة عن غياب قيود المحاولات المتكررة على بوابات تسجيل الدخول.
* [CWE-639: Authorization Bypass Through User-Controlled Key](https://cwe.mitre.org/data/definitions/639.html):
  التعريف الرسمي لثغرات التلاعب بالمعرفات التسلسلية (IDOR).

---

## 3. مستودعات القواميس وأدوات الفحص المساندة (Wordlists & CLI Alternatives)
* [SecLists by Daniel Miessler](https://github.com/danielmiessler/SecLists):
  المستودع القياسي العالمي لقواميس أسماء المستخدمين، كلمات المرور، وحمولات الفحص الشامل.
* [ffuf (Fast Web Fuzzer)](https://github.com/ffuf/ffuf):
  أداة سطر الأوامر الفائقة السرعة المكتوبة بلغة Go للمسح والتخمين عالي الكثافة كبديل لـ Intruder في الفحص المبدئي.
* [Hydra: Network Logon Cracker](https://github.com/vanhauser-thc/thc-hydra):
  أداة كسر كلمات المرور الشبكية متعددة البروتوكولات (HTTP-POST-Form, SSH, FTP).
