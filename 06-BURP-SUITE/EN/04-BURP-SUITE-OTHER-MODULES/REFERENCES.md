# المراجع والمصادر التوثيقية: وحدات بيرب سويت الإضافية (Burp Suite: Other Modules References)

---

## 1. التوثيق الرسمي لأداة بيرب سويت (Official PortSwigger Documentation)
* [PortSwigger Burp Suite Decoder Documentation](https://portswigger.net/burp/documentation/desktop/tools/decoder):
  الدليل الإرشادي لأداة فك التشفير، دعم خوارزميات التجزئة، ميزة Smart decode، وتحرير البيانات الثنائية في نمط Hex.
* [PortSwigger Burp Suite Comparer Documentation](https://portswigger.net/burp/documentation/desktop/tools/comparer):
  دليل استخدام أداة المقارنة البصرية، الفروق النصية (Words)، وفروق البايتات الثنائية (Bytes).
* [PortSwigger Burp Suite Sequencer Documentation](https://portswigger.net/burp/documentation/desktop/tools/sequencer):
  توثيق أداة تحليل عشوائية التوكنات، الالتقاط الحي للعينات، وتفسير تقارير الإنتروبيا واختبارات FIPS.
* [PortSwigger Burp Suite Organizer Documentation](https://portswigger.net/burp/documentation/desktop/tools/organizer):
  دليل إدارة وحفظ وتصنيف الطلبات والاستجابات بنمط القراءة فقط لدعم كتابة التقارير الأمنية.

---

## 2. المعايير التشفيرية والإحصائية الدولية (NIST & IETF Standards)
* [NIST Special Publication 800-22 (Rev 1a)](https://csrc.nist.gov/publications/detail/sp/800-22/rev-1a/final):
  المعيار الإحصائي الشامل لاختبار جودة مولدات الأرقام العشوائية وشبه العشوائية (RNG / PRNG).
* [FIPS PUB 140-2: Security Requirements for Cryptographic Modules](https://csrc.nist.gov/publications/detail/fips/140-2/final):
  المعيار الفيدرالي لاختبارات العشوائية الإحصائية (Monobit, Poker, Runs Tests).
* [IETF RFC 4648: The Base16, Base32, and Base64 Data Encodings](https://www.rfc-editor.org/rfc/rfc4648):
  المعيار الدولي لترميزات البيانات الثنائية وقواعد الحشو بالبايتات (`=`).
* [IETF RFC 3986: Uniform Resource Identifier (URI) - Percent-Encoding](https://www.rfc-editor.org/rfc/rfc3986):
  المعيار العالمي لترميز العناوين وتنسيق النسبة المئوية للمحارف المحجوزة.

---

## 3. تصنيفات الثغرات ونقاط الضعف البرمجية (OWASP & CWE Classifications)
* [OWASP Top 10: A02:2021 - Cryptographic Failures](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/):
  تصنيف مخاطر الإخفاقات التشفيرية، استخدام خوارزميات تجزئة ضعيفة (MD5/SHA1)، أو ضعف مصادر الإنتروبيا.
* [CWE-330: Use of Insufficiently Random Values](https://cwe.mitre.org/data/definitions/330.html):
  التعريف الدولي لنقاط الضعف الناتجة عن الاعتماد على قيم متوقعة أو ضعيفة العشوائية في توليد التوكنات.
* [CWE-338: Use of Cryptographically Weak Pseudo-Random Number Generator (PRNG)](https://cwe.mitre.org/data/definitions/338.html):
  مخاطر استخدام دوال عشوائية قياسية غير آمنة تشفيرياً (مثل `Math.random()` أو `rand()`) في التوكنات الحساسة.
