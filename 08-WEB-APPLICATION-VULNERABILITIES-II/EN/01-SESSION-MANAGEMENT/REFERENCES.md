# References & Authoritative Documentation: Session Management (المراجع والتوثيق المعتمد)

---

## 1. المعايير والمواصفات القياسية (Standards & Specifications)

* **RFC 6265 - HTTP State Management Mechanism:**
  - الوثيقة المعيارية لبروتوكول IETF التي تحدد كيفية عمل ملفات تعريف الارتباط، والترويسات `Set-Cookie` و `Cookie`، والقواعد الصارمة لسمات `Secure`, `HttpOnly`, و `SameSite`.
  - الرابط: https://datatracker.ietf.org/doc/html/rfc6265

* **RFC 7519 - JSON Web Token (JWT):**
  - المواصفة القياسية لتنسيق رموز JWT المستقلة، وبنية ترويساتها وحمولتها وتوقيعها التشفيري.
  - الرابط: https://datatracker.ietf.org/doc/html/rfc7519

* **RFC 6750 - OAuth 2.0 Bearer Token Usage:**
  - توثيق كيفية إرسال الرموز الرقمية عبر ترويسة الطلب `Authorization: Bearer`.
  - الرابط: https://datatracker.ietf.org/doc/html/rfc6750

* **NIST Special Publication 800-63B - Digital Identity Guidelines:**
  - الدليل المعياري للمعهد الوطني الأمريكي للمعايير والتقنية (NIST) لإدارة دورة حياة المصادقة والجلسات، ومتطلبات الإنتروبيا والمهل الزمنية (Timeouts).
  - الرابط: https://pages.nist.gov/800-63-3/sp800-63b.html

---

## 2. أدلة مجتمع أمان تطبيقات الويب (OWASP Guidelines)

* **OWASP Session Management Cheat Sheet:**
  - الدليل الشامل والمحدث لتصميم وتطبيق وإدارة الجلسات الآمنة، وتغطية جميع مراحل دورة الحياة من الإنشاء حتى الإبطال.
  - الرابط: https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html

* **OWASP Application Security Verification Standard (ASVS) v4.0.3:**
  - **الفصل الثالث (Chapter 3: Session Management Verification Requirements):**
    - V3.1: توليد معرفات الجلسات وعشوائيتها (Entropy).
    - V3.2: سمات الكوكيز وحمايتها.
    - V3.3: مهل انتهاء الصلاحية التلقائية (Timeouts).
    - V3.4: إنهاء وإبطال الجلسات (Termination & Revocation).
  - الرابط: https://owasp.org/www-project-application-security-verification-standard/

* **OWASP Top 10 (2021) - A07: Identification and Authentication Failures:**
  - تصنيف الثغرات المتعلقة بإدارة الجلسات وتثبيتها وسرقتها ضمن أخطر 10 مخاطر أمنية للويب.
  - الرابط: https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/

---

## 3. منصات التدريب والمختبرات المتقدمة (Research & Labs)

* **PortSwigger Web Security Academy:**
  - موضوعات إدارة الجلسات، التلاعب بالرموز، وتثبيت الجلسات، وثغرات JWT:
  - الرابط: https://portswigger.net/web-security/jwt
  - الرابط: https://portswigger.net/web-security/essential-skills/cookies

* **TryHackMe Room Official Reference:**
  - غرفة Session Management على منصة TryHackMe:
  - الرابط: https://tryhackme.com/room/sessionmanagement
