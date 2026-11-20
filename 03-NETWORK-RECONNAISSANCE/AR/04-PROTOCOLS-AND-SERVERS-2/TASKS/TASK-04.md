# Task 04: Transport Layer Security (TLS)
## المهمة 04: أمان طبقة النقل (TLS) وتأمين بروتوكول DNS عبر DoT

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 04
* **Original Title:** Transport Layer Security (TLS)
* **Questions:**
  1. *DNS can also be secured using TLS. What is the three-letter acronym of the DNS protocol that uses TLS?*
* **Objective:** Understand the cryptographic foundation of Transport Layer Security (TLS 1.2 / TLS 1.3), master symmetric vs asymmetric encryption in session establishment, and study encrypted DNS protocols (DoT vs DoH).
* **Core Concepts:** TLS Cryptographic Handshake, X.509 Certificates, Asymmetric Key Exchange, Symmetric Data Encryption, DNS over TLS (DoT - RFC 7858).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **كيف يحمي بروتوكول TLS الاتصالات؟**
  * يعالج TLS العيب القاتل في البروتوكولات النصية عبر تحقيق ثلاث ركائز أمنية رئيسية:
    1. **السرية (Confidentiality):** تشفير كافة البيانات المنقولة حتى لا يستطيع المتنصت قراءتها.
    2. **سلامة البيانات (Integrity):** التأكد من عدم تعديل الحزم أثناء انتقالها عبر خوارزميات الهاش (HMAC / AEAD).
    3. **المصادقة (Authentication):** إثبات هوية الخادم باستخدام الشهادات الرقمية الموقعة من جهات معتمدة (Certificate Authorities - CAs).
* **تأمين بروتوكول DNS:**
  * بروتوكول DNS التقليدي (المنفذ 53 UDP) ينقل أسماء المواقع التي يزورها المستخدم صراحة كنص واضح، مما يتيح لمزودي الخدمة والمتنصتين مراقبة نشاط المستخدم.
  * لتأمين استعلامات الـ DNS، تم تطوير تقنيتين حديثتين:
    1. **DNS over HTTPS (DoH):** تشفير استعلامات الـ DNS داخل طلبات HTTP/2 أو HTTP/3 قياسية عبر المنفذ 443.
    2. **DNS over TLS (DoT):** تغليف استعلامات الـ DNS مباشرة داخل نفق TLS مشفر مستقل عبر المنفذ المعياري **`853`** (RFC 7858).
* **تحليل السؤال:**
  * السؤال يطلب الاختصار المكون من ثلاثة أحرف (Three-letter acronym) لبروتوكول الـ DNS الذي يستخدم الـ TLS مباشرة.
  * الاختصار المعياري هو: **`DoT`** (DNS over TLS).

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** الاختصار مكون من الحرف الأول من كلمة DNS والحرف الثاني من كلمة over والحرف الثالث من كلمة TLS.
* **Hint 2:** الاختصار المعياري هو: `DoT`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **معيار RFC 7858 (DNS over TLS):**
   * يعرف هذا المعيار كيف يقوم عميل الـ DNS بفتح اتصال TCP على المنفذ 853 مع خادم DNS يدعم التشفير (مثل خوادم Cloudflare `1.1.1.1` أو Google `8.8.8.8`)، وإجراء مصافحة TLS وتشفير كافة استعلامات أسماء النطاقات.
2. **المقارنة بين DoT و DoH:**
   * **DoT (DNS over TLS):** يعمل على المنفذ المخصص `853 TCP`. ميزته أنه بروتوكول مخصص ونقي للـ DNS، وعيبه أن الجدران النارية تستطيع حظره بسهولة بإغلاق المنفذ 853.
   * **DoH (DNS over HTTPS):** يختفي داخل حركة مرور الويب العامة على المنفذ `443 TCP`، مما يجعل حظره صعباً دون حظر كافة مواقع الويب.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *DNS can also be secured using TLS. What is the three-letter acronym of the DNS protocol that uses TLS?*
  * **Answer:** `DoT`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Encrypted DNS Protocol Standard:
RFC: RFC 7858
Standard Name: Specification for DNS over Transport Layer Security
Acronym: DoT
Default Port: 853/TCP
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في خصوصية وأمان DNS:**
> *"Even if your web traffic is encrypted with HTTPS, unencrypted DNS queries expose every domain you visit; deploying DoT or DoH closes the final plaintext leak in your reconnaissance footprint."*
