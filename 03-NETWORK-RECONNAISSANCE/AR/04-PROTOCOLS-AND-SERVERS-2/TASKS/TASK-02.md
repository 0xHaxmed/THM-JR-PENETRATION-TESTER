# Task 02: Sniffing Attack
## المهمة 02: هجمات التنصت على الشبكة عبر tcpdump و Wireshark

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** Sniffing Attack
* **Questions:**
  1. *What do you need to add to the command sudo tcpdump to capture only Telnet traffic?*
  2. *What is the simplest display filter you can use with Wireshark to show only IMAP traffic?*
* **Objective:** Understand how network sniffing operates, analyze packet capture mechanics via Berkeley Packet Filters (BPF) in `tcpdump`, and apply display filters in Wireshark to isolate cleartext traffic.
* **Core Concepts:** Packet Sniffing, Promiscuous Mode, Berkeley Packet Filters (BPF), Wireshark Display Filters, Cleartext Interception.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **كيف يعمل التنصت على الحزم (Packet Sniffing)؟**
  * افتراضياً، تتجاهل بطاقة الشبكة (NIC) أي إطار شبكي لا يحمل عنوان الـ MAC الخاص بها.
  * عند تفعيل وضع الاختلاط (**Promiscuous Mode**)، تبدأ بطاقة الشبكة في استلام ونسخ كافة الحزم المارة عبر وسيط الشبكة وتمريرها إلى برامج التحليل.
* **تحليل السؤال الأول (فلترة حركة Telnet في tcpdump):**
  * تعتمد أداة `tcpdump` على لغة فلاتر بيركلي (**Berkeley Packet Filters - BPF**).
  * لعزل وتصفية الحزم الخاصة ببروتوكول تلنت (الذي يعمل على المنفذ المعياري 23)، نضيف إلى الأمر الكلمة الدلالية متبوعة برقم المنفذ: **`port 23`**.
  * الأمر الكامل: `sudo tcpdump -i eth0 port 23`.
* **تحليل السؤال الثاني (فلتر العرض الأبسط لحركة IMAP في Wireshark):**
  * في أداة Wireshark الرسومية، تتوفر فلاتر عرض (Display Filters) ملائمة ومبسطة تعتمد على اسم البروتوكول مباشرة.
  * لعرض الحزم الخاصة ببروتوكول IMAP فقط، نكتب ببساطة في شريط الفلترة الأخضر: **`imap`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** الكلمة الإنجليزية للمنفذ متبوعة برقم منفذ Telnet الشهير: `port 23`.
* **Hint 2 (للسؤال 2):** اسم البروتوكول بالحروف الإنجليزية الصغيرة كما هو: `imap`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **التقاط حركة Telnet عبر tcpdump:**
   ```bash
   sudo tcpdump -n -i any port 23
   ```
   * يقوم الفلتر `port 23` بإسقاط كافة الحزم الأخرى (DNS, HTTP, SSH) والتركيز فقط على حزم المنفذ 23، مما يتيح استخراج كلمات المرور الصريحة المكتوبة في الجلسة مباشرة.
2. **فلترة حركة IMAP في Wireshark:**
   * في شريط الفلترة العلوي (Display Filter bar):
     ```text
     imap
     ```
   * يعرض برنامج Wireshark كافة أوامر الـ IMAP (مثل `LOGIN`, `SELECT`, `FETCH`) بوضوح تام.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What do you need to add to the command sudo tcpdump to capture only Telnet traffic?*
  * **Answer:** `port 23`
* **Question 2:** *What is the simplest display filter you can use with Wireshark to show only IMAP traffic?*
  * **Answer:** `imap`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Filter Syntax Verification:
[+] tcpdump BPF Filter: port 23 (Matches TCP src/dst port 23)
[+] Wireshark Display Filter: imap (Dissects RFC 3501 packets)
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في اعتراض حركة الشبكة:**
> *"Never sniff without filters; a busy enterprise network generates gigabytes of broadcast noise per minute. Targeted BPF filters (like 'port 23') isolate actionable credentials instantly."*
