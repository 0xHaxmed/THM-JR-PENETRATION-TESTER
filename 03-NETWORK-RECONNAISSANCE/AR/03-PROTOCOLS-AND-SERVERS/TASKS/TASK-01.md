# Task 01: Introduction
## المهمة 01: مدخل إلى بروتوكولات وخوادم الشبكة الأساسية

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 01
* **Original Title:** Introduction
* **Questions:**
  1. *We suggest that you start the AttackBox and the lab machine as you proceed to tackle the following tasks. You can connect to the different services over Telnet for better practice and learning experience.*
* **Objective:** Understand the architectural role of core application-layer protocols (HTTP, FTP, SMTP, POP3, IMAP) in enterprise infrastructure, and prepare to interact with raw network sockets over Telnet.
* **Core Concepts:** Application-Layer Protocols, Client-Server Architecture, Cleartext Sockets, Protocol Interaction via Telnet.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **لماذا نحتاج لدراسة البروتوكولات الخام في اختبار الاختراق؟**
  * معظم الأدوات الآلية (مثل المتصفحات وعملاء البريد) تُخفي عن المستخدم حقيقة ما يدور عبر مقبس الشبكة (Network Socket).
  * في اختبار الاختراق المتقدم، يجب أن يفهم الباحث الأمني كيف يرسل العميل الطلب (Request Verb)، وكيف يرد الخادم برموز الحالة (Status Codes)، وما إذا كانت البيانات وكلمات المرور تُنقل كنص واضح (Cleartext) يمكن اعتراضه والتلاعب به.
* **بيئة المعمل التفاعلي:**
  * تشغيل الهدف الافتراضي واستخدام أداة `telnet` للاتصال بكل منفذ على حدة ومخاطبة الخادم بلغته الأصلية (RFC Dialect).

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** تأكد من تشغيل الآلة الافتراضية والضغط على زر `Completed` للانتقال لمنفذ بروتوكول تلنت.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
* استعراض المنافذ المعيارية للبروتوكولات التي سيتم فحصها في الغرفة:
  * **Port 23:** بروتوكول Telnet (الإدارة عن بعد القديمة).
  * **Port 80:** بروتوكول HTTP (نقل صفحات الويب).
  * **Port 21:** بروتوكول FTP (نقل الملفات والبيانات).
  * **Port 25:** بروتوكول SMTP (إرسال وتوجيه البريد الإلكتروني).
  * **Port 110:** بروتوكول POP3 (استرجاع وتنزيل البريد للعميل).
  * **Port 143:** بروتوكول IMAP (مزامنة وتصفح صناديق البريد على الخادم).

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *We suggest that you start the AttackBox and the lab machine...*
  * **Answer:** `No answer needed`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
* الآلة الافتراضية تعمل وجاهزة لاستقبال اتصالات المقابس عبر الشبكة المحلية لـ TryHackMe.

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في فهم بروتوكولات الشبكة:**
> *"High-level tools obscure protocol semantics; speaking raw RFC commands over Telnet strips away abstraction and exposes server behavior in its purest state."*
