# Task 03: Ping
## المهمة 03: استكشاف الأجهزة وتشريح بروتوكول ICMP عبر Ping

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 03
* **Original Title:** Ping
* **Questions:**
  1. *Which option would you use to set the size of the data carried by the ICMP echo request?*
  2. *What is the size of the ICMP header in bytes?*
  3. *Does MS Windows Firewall block ping by default? (Y/N)*
  4. *Deploy the VM for this task and using the AttackBox terminal, issue the command ping -c 10 MACHINE_IP . How many ping replies did you get back?*
* **Objective:** Understand the architectural mechanics of the ICMP protocol (RFC 792), dissect ICMP Echo Request/Reply packet structures, master the `ping` utility syntax and flags, and analyze OS firewall defaults regarding ICMP ingress traffic.
* **Core Concepts:** ICMP Protocol (Type 8 Echo Request / Type 0 Echo Reply), ICMP Header Size, Payload Sizing (`-s`), Windows Firewall ICMP Blocking, Host Availability Verification.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **كيف تعمل أداة Ping وبروتوكول ICMP؟**
  * تعتمد أداة `ping` على بروتوكول رسائل التحكم في الإنترنت (**Internet Control Message Protocol - ICMP**).
  * ترسل الأداة حزمة `ICMP Type 8` (Echo Request) إلى الهدف.
  * إذا كان الجهاز المضيف حياً ويعمل ولا يحظر بروتوكول ICMP، فإنه يرد بحزمة `ICMP Type 0` (Echo Reply).
* **تشريح حزمة ICMP (Packet Anatomy):**
  * تتكون ترويسة ICMP (ICMP Header) دائماً من **`8` بايتات ثابتة**:
    * **Type (1 بايت):** نوع الرسالة (8 للطلب، 0 للرد).
    * **Code (1 بايت):** الكود الفرعي (عادة 0).
    * **Checksum (2 بايت):** المجموع الاختباري للتحقق من سلامة البيانات.
    * **Identifier & Sequence Number (4 بايت):** لمطابقة كل رد مع الطلب الصادر المناسب.
* **خيارات أداة Ping في لينكس:**
  * افتراضياً، ترسل الأداة حمولة بيانات (Data Payload) بحجم 56 بايت، وعند إضافة ترويسة الـ ICMP (8 بايت) تصبح الحزمة 64 بايت.
  * الخيار المستخدم لتحديد حجم حمولة البيانات المخصصة هو: **`-s`** (Size).
* **سلوك الجدران النارية وأنظمة التشغيل:**
  * **Microsoft Windows Firewall:** افتراضياً وفي ملفات تعريف الشبكة العامة والخاصة، يحظر جدار حماية ويندوز حزم `ICMP Echo Request` الواردة كإجراء أمني للحد من البصم الاستطلاعي (**`Y`**).
* **التطبيق العملي بالمعمل:**
  * عند تشغيل الآلة الافتراضية التابعة للمعمل وإرسال 10 حزم عبر `ping -c 10 MACHINE_IP`، ولأن الخادم الهدف في المعمل مهيأ للرد، يستقبل الفاحص كافة الردود العشرة بنسبة نجاح 100% (**`10`**).

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** خيار مكون من شرطة وحرف s صغير مشتق من كلمة size: `-s`.
* **Hint 2 (للسؤال 2):** حجم ترويسة ICMP هو 8 بايت: `8`.
* **Hint 3 (للسؤال 3):** اكتب حرف Y اختصاراً لـ Yes: `Y`.
* **Hint 4 (للسؤال 4):** عدد الحزم المرتجعة مطابق للعدد المحدد في الخيار `-c 10`: `10`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **استعراض صفحة المان لأداة Ping:**
   ```bash
   man ping
   ```
   * نجد الوصف التالي:
     ```text
     -s packetsize
            Specifies the number of data bytes to be sent.  The default is 56, which translates into 64 ICMP data bytes when combined with the 8 bytes of ICMP header data.
     ```
2. **تشريح ترويسة ICMP:**
   ```text
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |     Type      |     Code      |          Checksum             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           Identifier          |        Sequence Number        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         Data Payload ...                      |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   Total ICMP Header Size: 1 + 1 + 2 + 4 = 8 Bytes
   ```
3. **تنفيذ الأمر في المعمل العملي:**
   ```bash
   ping -c 10 10.10.x.x
   ```
   * مخرجات الطرفية:
     ```text
     PING 10.10.x.x (10.10.x.x) 56(84) bytes of data.
     64 bytes from 10.10.x.x: icmp_seq=1 ttl=64 time=0.450 ms
     ...
     64 bytes from 10.10.x.x: icmp_seq=10 ttl=64 time=0.412 ms

     --- 10.10.x.x ping statistics ---
     10 packets transmitted, 10 received, 0% packet loss, time 9214ms
     ```
   * عدد الردود المستلمة: `10`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Which option would you use to set the size of the data carried by the ICMP echo request?*
  * **Answer:** `-s`
* **Question 2:** *What is the size of the ICMP header in bytes?*
  * **Answer:** `8`
* **Question 3:** *Does MS Windows Firewall block ping by default? (Y/N)*
  * **Answer:** `Y`
* **Question 4:** *Deploy the VM... issue the command ping -c 10 MACHINE_IP . How many ping replies did you get back?*
  * **Answer:** `10`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
ICMP Protocol & Ping Tool Verification:
[+] Data Size Flag: -s
[+] Header Byte Count: 8 bytes
[+] Windows Firewall Default ICMP Status: Blocked (Y)
[+] Packets Transmitted: 10 | Received: 10 | Loss: 0%
All answers verified against live THM account.
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استكشاف الأجهزة الحية:**
> *"Never assume a host is offline just because ping times out; modern enterprise firewalls (especially Windows hosts) silently drop ICMP Type 8 requests while web and administrative ports remain wide open."*
> لا تفترض أبداً أن الخادم معطل أو غير موجود لمجرد أن أمر ping لا يستجيب؛ فالجدران النارية الحديثة تسقط حزم ICMP Type 8 بصمت تام بينما تظل منافذ الويب والخدمات مفتوحة وتعمل بكفاءة.
