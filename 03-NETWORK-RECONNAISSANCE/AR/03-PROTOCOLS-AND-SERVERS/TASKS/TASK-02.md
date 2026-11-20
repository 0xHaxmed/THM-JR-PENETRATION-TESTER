# Task 02: Telnet
## المهمة 02: بروتوكول Telnet والمنفذ الافتراضي المعياري

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** Telnet
* **Questions:**
  1. *To which port will the telnet command with the default parameters try to connect?*
* **Objective:** Understand the historical background of the Telnet protocol (RFC 854), its default port assignment, and its vulnerability to cleartext packet sniffing.
* **Core Concepts:** Telnet Protocol, Port 23, Cleartext Administration, Default Port Binding.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل بروتوكول Telnet:**
  * طُوّر بروتوكول `Telnet` (Telecommunication Network) في أواخر الستينيات ليتيح للمستخدمين تسجيل الدخول وإدارة الأنظمة الطرفية عن بعد عبر الشبكة.
  * خصصت له هيئة أرقام الإنترنت المخصصة (IANA) المنفذ المعياري رقم **`23`** على بروتوكول TCP.
* **المشكلة الأمنية القاتلة في Telnet:**
  * لا يحتوي بروتوكول Telnet على أي آلية تشفير؛ فكل حرف يكتبه المستخدم (بما في ذلك اسم المستخدم وكلمات المرور السرية والأوامر المنفذة) يُبث عبر كابل الشبكة كنص واضح (Cleartext).
  * إذا قام أي شخص بتشغيل برنامج التقاط حزم بسيط مثل Wireshark على نفس الشبكة المحلية، فإنه يستطيع قراءة كلمة المرور فوراً بمجرد الضغط على خيار `Follow TCP Stream`.
  * لهذا السبب، تم استبدال Telnet عالمياً ببروتوكول **SSH (Secure Shell)** الذي يعمل على المنفذ 22 ويشفر كافة البيانات المنقولة.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** إذا كتبت الأمر `telnet target.thm` دون تحديد رقم منفذ في النهاية، ما هو المنفذ الافتراضي الذي سيتصل به؟
* **Hint 2:** رقم المنفذ هو عدد يقع بين 20 و 25: `23`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **استعراض المنفذ في ملف الخدمات بنظام لينكس:**
   في أي نظام لينكس، يمكن التحقق من المنافذ المعيارية في ملف `/etc/services`:
   ```bash
   grep -w "telnet" /etc/services
   ```
   * مخرجات الأمر:
     ```text
     telnet          23/tcp
     ```
2. **سلوك عميل Telnet:**
   * عند كتابة `telnet <IP>` بدون وسيط المنفذ:
     ```bash
     telnet 10.10.x.x
     ```
   * تحاول الأداة تلقائياً فتح مقبس TCP على المنفذ `23`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *To which port will the telnet command with the default parameters try to connect?*
  * **Answer:** `23`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Default Port Verification:
Command: telnet target.thm
Attempting connection: target.thm:23 (TCP)
Assigned Default Port: 23
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في إدارة الأنظمة وأمن الشبكات:**
> *"Telnet on port 23 is a critical security vulnerability in modern networks. Any credentials transmitted over port 23 can be intercepted with zero cryptographic effort; always enforce SSH (port 22)."*
