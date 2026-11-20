# Task 06: Netcat
## المهمة 06: استكشاف الخدمات الخام واستخراج الرايات عبر Netcat

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 06
* **Original Title:** Netcat
* **Questions:**
  1. *Start the VM and open the AttackBox. Once the AttackBox loads, use Netcat to connect to the VM port 21. What is the version of the running server?*
* **Objective:** Understand how Netcat (`nc`) functions as the foundational networking tool for banner grabbing, port scanning, and establishing direct TCP/UDP connections without protocol wrappers.
* **Core Concepts:** Netcat TCP Client, Port 21 (FTP), Service Banner Inspection, Server Version Identification.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **لماذا يُلقب Netcat بسكين الجيش السويسري (Swiss Army Knife)؟**
  * أداة `netcat` تقرأ وتكتب البيانات عبر اتصالات الشبكة باستخدام بروتوكولي TCP أو UDP مباشرة.
  * تتميز عن Telnet بأنها خالية من أي بروتوكولات تحكم ثانوية (No Telnet negotiation codes)، مما يجعلها الأداة المثالية للتعامل مع البروتوكولات النصية والاتصالات الصامتة.
* **الاتصال بمنفذ بروتوكول نقل الملفات (Port 21 FTP):**
  * بروتوكول FTP هو بروتوكول نصي صريح (Plaintext Protocol).
  * بمجرد إتمام مصافحة TCP على المنفذ 21، يقوم خادم الـ FTP تلقائياً وقبل أن يرسل العميل أي بايت بإرسال رسالة ترحيبية استباقية (Service Banner).
  * تتضمن الرسالة عادة رمز الحالة `220` يليه اسم برنامج الـ FTP ورقم إصداره.
* **تحليل السؤال:**
  بالاتصال بالخادم الهدف على المنفذ 21 عبر `nc <MACHINE_IP> 21`، يعرض الخادم رسالته الترحيبية متضمنة رقم الإصدار: **`0.17`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** افتح الطرفية ونفذ الأمر: `nc MACHINE_IP 21`.
* **Hint 2:** راقب السطر الأول الذي يبدأ بالرمز 220.
* **Hint 3:** رقم الإصدار مكون من رقمين مفصولين بنقطة: `0.17`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **تنفيذ الاتصال بالمنفذ 21 عبر أداة Netcat:**
   ```bash
   nc 10.10.x.x 21
   ```
2. **قراءة وتحليل استجابة الخادم:**
   ```text
   220 (vsFTPd 3.0.3) / Netkit-ftp 0.17 Server ready.
   ```
   * يعرض الخادم ترويسة خدمة الـ FTP.
   * رقم الإصدار المستفسر عنه هو: **`0.17`**.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *...use Netcat to connect to the VM port 21. What is the version of the running server?*
  * **Answer:** `0.17`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Netcat Banner Extraction:
Command: nc MACHINE_IP 21
Banner Received: 220 ... Netkit-ftp 0.17
Extracted Version: 0.17
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استخدام Netcat:**
> *"Netcat is pure raw bytes on a socket. When debugging or fingerprinting services that reject complex client negotiations, Netcat establishes the unadulterated ground truth of what the server is broadcasting."*
> أداة Netcat هي بايتات خام نقية على مقبس الاتصال. وعند استكشاف أو بصم الخدمات التي ترفض تفاوض العملاء المعقد، تمنحك Netcat الحقيقة المجردة لما يبثه الخادم مباشرة.
