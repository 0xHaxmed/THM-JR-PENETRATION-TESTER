# المراجع والمصادر التوثيقية: حقن قواعد البيانات (SQL Injection References)

---

## 1. التوثيق والمصادر المتقدمة من PortSwigger (PortSwigger Authoritative Guides)
* [PortSwigger SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet):
  الدليل المرجعي العالمي الأشهر لحمولات وتراكيب SQL Injection عبر محركات MySQL و MSSQL و Oracle و PostgreSQL.
* [PortSwigger Web Security Academy: SQL Injection](https://portswigger.net/web-security/sql-injection):
  أكاديمية تعليمية تفاعلية تغطي هجمات UNION والتسريب الأعمى وهجمات Out-of-Band بالتفصيل.
* [PortSwigger Blind SQL Injection Deep Dive](https://portswigger.net/web-security/sql-injection/blind):
  دليل منهجي لاستغلال الثغرات العمياء عبر الشروط المنطقية ودوال التأخير الزمني واستخراج البيانات عبر DNS.

---

## 2. معايير وإرشادات الحماية من OWASP و MITRE (Security Standards)
* [OWASP SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html):
  الدليل الدفاعي المعياري لتحصين التطبيقات عبر الاستعلامات المجهزة (Parameterized Queries) والتحقق من المدخلات.
* [OWASP Top 10: A03:2021 - Injection](https://owasp.org/Top10/A03_2021-Injection/):
  التصنيف الدولي لمخاطر حقن الأوامر و SQLi والآثار التدميرية على المؤسسات.
* [CWE-89: Improper Neutralization of Special Elements used in an SQL Command](https://cwe.mitre.org/data/definitions/89.html):
  التعريف الأمني الشامل لنقاط الضعف الناتجة عن دمج مدخلات المستخدم غير المنقاة في أوامر SQL.
* [NIST SP 800-95: Guide to Secure Web Services](https://csrc.nist.gov/publications/detail/sp/800-95/final):
  إرشادات المعهد القومي للمعايير لحماية قواعد البيانات وخدمات الويب المترابطة.

---

## 3. أدوات الأتمتة ومستودعات الفحص المفتوحة (Offensive Tooling)
* [SQLMap: Automatic SQL Injection and Database Takeover Tool](https://sqlmap.org/):
  الموقع الرسمي ومستودع الشفرة المصدرية لأداة SQLMap المفتوحة المصدر.
* [PayloadsAllTheThings - SQL Injection Directory](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection):
  مستودع شامل للحمولات وتجاوزات WAF الجاهزة لمختلف قواعد البيانات.
* [SecLists - Fuzzing Payloads for SQLi](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/Databases):
  قواميس متخصصة لفحص واكتشاف ثغرات قواعد البيانات تلقائياً.
