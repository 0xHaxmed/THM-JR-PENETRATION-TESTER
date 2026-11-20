# المراجع والمصادر التوثيقية: مكرر بيرب سويت (Burp Suite: Repeater References)

---

## 1. التوثيق الرسمي لأداة بيرب سويت (Official PortSwigger Documentation)
* [PortSwigger Burp Suite Repeater User Guide](https://portswigger.net/burp/documentation/desktop/tools/repeater):
  دليل الاستخدام الرسمي لوحدة Repeater، إدارة علامات التبويب، ضبط الخيارات، واستخدام المفتش (Inspector).
* [PortSwigger Message Analysis Tools](https://portswigger.net/burp/documentation/desktop/tools/repeater/message-analysis):
  توثيق شريط أدوات تحليل الرسائل (Pretty, Raw, Hex, Render) والتعامل مع المحتوى الثنائي والمشفر.
* [PortSwigger Web Security Academy: SQL Injection UNION Attacks](https://portswigger.net/web-security/sql-injection/union-attacks):
  معمل تعليمي شامل حول استغلال ثغرات حقن SQL القائمة على مشغل UNION لتحديد عدد الأعمدة واستخراج البيانات.

---

## 2. معايير بروتوكول نقل النص الفائق (IETF RFC Standards)
* [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110):
  المعيار الدولي لهيكلية رسائل HTTP، قواعد صياغة الترويسات (Headers Syntax)، وقواعد التفاوض على المحتوى.
* [RFC 9112: HTTP/1.1](https://www.rfc-editor.org/rfc/rfc9112):
  توثيق حدود رسائل HTTP/1.1، تأطير الرسائل، وترويسات التحكم في الطول مثل `Content-Length` و `Transfer-Encoding`.

---

## 3. تصنيفات الثغرات ونقاط الضعف البرمجية (CWE & OWASP Classifications)
* [OWASP Top 10: A03:2021 - Injection](https://owasp.org/Top10/A03_2021-Injection/):
  التصنيف الدولي لمخاطر حقن الأوامر و SQLi في تطبيقات الويب.
* [OWASP Top 10: A01:2021 - Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/):
  إخفاقات التحكم في الوصول والتفويض الناتج عن الثقة في ترويسات العميل العشوائية.
* [CWE-209: Generation of Error Message Containing Sensitive Information](https://cwe.mitre.org/data/definitions/209.html):
  تسريب المعلومات والرايات من خلال رسائل الأخطاء الداخلية (HTTP 500 Stack Traces).
* [CWE-89: Improper Neutralization of Special Elements used in an SQL Command](https://cwe.mitre.org/data/definitions/89.html):
  التعريف الأمني الشامل لثغرات حقن قواعد البيانات SQL Injection.

---

## 4. أدوات سطر الأوامر البديلة للمقارنة العملية (Alternative CLI Tools)
* [cURL Manual & HTTP Request Crafting](https://curl.se/docs/manpage.html):
  الدليل المرجعي لاستخدام cURL لإرسال الترويسات المخصصة وحمولات POST عبر سطر الأوامر.
* [HTTPie: Modern Command-Line HTTP Client](https://httpie.io/docs/cli):
  أداة سطر أوامر تفاعلية بديلة لـ cURL توفر تلوين المخرجات وصياغة طلبات JSON بسهولة.
