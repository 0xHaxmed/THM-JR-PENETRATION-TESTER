# Task 01: Introduction
## المهمة 01: مدخل إلى الاستطلاع النشط (Active Reconnaissance)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 01
* **Original Title:** Introduction
* **Questions:**
  1. *Ensure that you understand why these tools fall under active reconnaissance. Launch your AttackBox and ensure that it is ready. You will need it to answer the questions, especially in later tasks.*
* **Objective:** Understand the fundamental operational definition of Active Reconnaissance, contrast it with passive techniques, and understand the technical and legal implications of direct packet transmission.
* **Core Concepts:** Active Reconnaissance, Direct Interaction, Network Packets, Firewall Logs, Legal Boundaries, Engagement Scoping.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **الفرق الجوهري بين الاستطلاع السلبي والاستطلاع النشط:**
  * في **الاستطلاع السلبي (Passive Recon)**: قمنا بجمع المعلومات عن الهدف دون إرسال حزمة بيانات (Packet) واحدة مباشرة إلى خوادمه (اعتمدنا على سجلات WHOIS، استعلامات DNS العامة، ومحركات بحث خارجية كـ Shodan). الهدف لم يكن يعلم بوجودنا على الإطلاق.
  * في **الاستطلاع النشط (Active Recon)**: نرسل حزم بيانات حقيقية ومباشرة إلى خوادم وأجهزة الهدف (عبر بروتوكولات ICMP, TCP, UDP).
* **التداعيات الميدانية والمهنية للاستطلاع النشط:**
  1. **تسجيل الحزم (Logging):** كل طلب أو اتصال يرسله جهاز الفاحص يُسجل فوراً في سجلات الجدار الناري (Firewall Logs)، وسجلات خادم الويب (Web Server Logs)، وأنظمة كشف التسلل (IDS/IPS).
  2. **الإنذار الأمني (Security Alerts):** الاستطلاع النشط قد يُطلق إنذارات فورية لدى فريق الدفاع الأزرق (SOC Team) إذا لم يكن مصرحاً به مسبقاً.
  3. **الالتزام الصارم بنطاق العمل (Rules of Engagement):** لا يجوز إطلاق أي أداة استطلاع نشط إلا بعد توقيع العقد والحصول على إذن كتابي رسمي (Written Permission) يحدد بدقة عناوين الـ IP والمناطق المسموح بملامستها.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** تهدف هذه المهمة إلى إرساء القواعد المنهجية والأخلاقية، وتشغيل بيئة المعمل المخصصة.
* **Hint 2:** اضغط على زر `Completed` للانتقال فوراً للمهمة العملية التالية.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
* **أدوات الاستطلاع النشط الأساسية التي تغطيها هذه الغرفة:**
  1. **متصفح الويب (Web Browser & DevTools):** الفحص المباشر لأكواد المصدر واستجابات الـ HTTP وعناصر التحكم.
  2. **أداة Ping:** التحقق من وجود الهدف واستجابته عبر حزم `ICMP Echo Request`.
  3. **أداة Traceroute:** تتبع مسار الحزم عبر الموجهات (Routers/Hops) في شبكة الإنترنت وقياس زمن الانتقال.
  4. **أداة Telnet:** الاتصال المباشر بالمنافذ النصية لاستخراج ترويسات الخوادم (Banner Grabbing).
  5. **أداة Netcat:** الأداة السويسرية المتعددة الاستخدامات لفحص المنافذ وتبادل البيانات الخام.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Ensure that you understand why these tools fall under active reconnaissance...*
  * **Answer:** `No answer needed`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Reconnaissance Classification Verification:
Passive Recon: Third-party caches, no direct packets -> Stealthy, unlogged.
Active Recon: Direct IP socket connection -> Logged, monitored, triggers IDS.
Status: Environment Verified & Ready.
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في الاستطلاع النشط:**
> *"The moment a single SYN or ICMP packet leaves your network interface toward the target's IP, passive intelligence ends and active footprinting begins; ensure authorization is signed before transmitting."*
> اللحظة التي تغادر فيها حزمة SYN أو ICMP واحدة بطاقة شبكتك نحو عنوان الهدف، ينتهي الاستطلاع السلبي ويبدأ البصم النشط؛ تأكد دائماً من وجود تفويض رسمي موقع قبل إرسال أول بايت.
