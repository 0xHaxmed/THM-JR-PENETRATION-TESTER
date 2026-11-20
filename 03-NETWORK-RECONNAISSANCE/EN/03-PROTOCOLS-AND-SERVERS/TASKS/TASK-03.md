# Task 03: Hypertext Transfer Protocol (HTTP)
## المهمة 03: بروتوكول نقل النص الفائق (HTTP) وجلب الملفات يدوياً

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 03
* **Original Title:** Hypertext Transfer Protocol (HTTP)
* **Questions:**
  1. *Launch the attached VM. From the AttackBox terminal, connect using Telnet to MACHINE_IP 80 and retrieve the file flag.thm . What does it contain?*
* **Objective:** Understand the stateless, request-response architecture of HTTP (RFC 2616 / RFC 9110), construct manual HTTP/1.1 requests over a raw TCP socket, retrieve web resources directly, and capture the verification flag.
* **Core Concepts:** HTTP GET Verb, Host Header, CRLF Line Delimiters (`\r\n`), Status Codes (200 OK vs 404), Manual Socket Interaction.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **كيف يعمل طلب HTTP/1.1 هندسياً؟**
  * عندما يتصل العميل بخادم ويب على المنفذ 80، يتوقع الخادم طلباً نصياً منسقاً وفق معايير بروتوكول HTTP.
  * في الإصدار HTTP/1.1، تشترط المواصفات القياسية وجود ترويسة `Host:` لتحديد اسم النطاق المطلوب (وهو الأساس لعمل استضافة النطاقات المتعددة على نفس الـ IP أو ما يعرف بالـ Virtual Hosting).
  * يجب إنهاء كل سطر بترويسة سطر جديد (`\r\n` أو الضغط على زر Enter)، ويجب إرسال سطر فارغ إضافي في نهاية الترويسات لإعلام الخادم بانتهاء رأس الطلب وبدء المعالجة.
* **صياغة الطلب لجلب ملف `flag.thm`:**
  ```http
  GET /flag.thm HTTP/1.1
  Host: MACHINE_IP

  ```
* يرد الخادم بكود الحالة `HTTP/1.1 200 OK` متبوعاً بترويسات الاستجابة، ثم فراغ، ثم محتوى الملف المطلوب الذي يحتوي على الراية: **`THM{e3eb0a1df437f3f97a64aca5952c8ea0}`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** اتصل عبر الطرفية بالأمر: `telnet MACHINE_IP 80`.
* **Hint 2:** اكتب: `GET /flag.thm HTTP/1.1` واضغط Enter.
* **Hint 3:** اكتب: `Host: MACHINE_IP` واضغط Enter مرتين متتاليتين لإرسال السطر الفارغ المنفذ للطلب.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **فتح مقبس الاتصال عبر Telnet:**
   ```bash
   telnet 10.10.x.x 80
   ```
2. **كتابة وإرسال طلب HTTP الخام:**
   ```text
   GET /flag.thm HTTP/1.1
   Host: 10.10.x.x

   ```
3. **تحليل استجابة الخادم ومحتوى الملف:**
   ```text
   HTTP/1.1 200 OK
   Date: Wed, 18 Nov 2026 23:12:05 GMT
   Server: Apache/2.4.61 (Ubuntu)
   Last-Modified: Wed, 18 Nov 2026 22:00:00 GMT
   Content-Length: 38
   Content-Type: text/plain

   THM{e3eb0a1df437f3f97a64aca5952c8ea0}
   Connection closed by foreign host.
   ```
4. **استخراج الراية:**
   * محتوى الملف هو: `THM{e3eb0a1df437f3f97a64aca5952c8ea0}`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *...connect using Telnet to MACHINE_IP 80 and retrieve the file flag.thm . What does it contain?*
  * **Answer:** `THM{e3eb0a1df437f3f97a64aca5952c8ea0}`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
HTTP Raw Socket Execution:
Method: GET
Resource: /flag.thm
Response Code: 200 OK
Payload Body: THM{e3eb0a1df437f3f97a64aca5952c8ea0}
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في اختبار تطبيقات الويب:**
> *"Browsers restrict what headers you can send; mastering raw HTTP request construction over sockets or Burp Suite allows you to manipulate host headers, user-agents, and content delimiters at will."*
