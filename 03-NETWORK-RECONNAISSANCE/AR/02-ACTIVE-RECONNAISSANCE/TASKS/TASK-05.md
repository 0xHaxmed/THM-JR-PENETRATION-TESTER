# Task 05: Telnet
## المهمة 05: استخراج الترويسات (Banner Grabbing) عبر عميل Telnet

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 05
* **Original Title:** Telnet
* **Questions:**
  1. *Start the attached VM from Task 3 if it is not already started. On the AttackBox, open the terminal and use the telnet client to connect to the VM on port 80. What is the name of the running server?*
  2. *What is the version of the running server (on port 80 of the VM)?*
* **Objective:** Understand how the Telnet protocol (RFC 854) operates as a raw cleartext TCP socket client, master manual HTTP request crafting (`GET / HTTP/1.1`), and execute application banner grabbing to extract server names and version signatures.
* **Core Concepts:** Banner Grabbing, Raw TCP Sockets, Telnet Client, HTTP Header Inspection (`Server:` header), Apache Version Fingerprinting.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **لماذا يُستخدم عميل Telnet في الاستطلاع النشط؟**
  * صُمم بروتوكول `telnet` قديماً لإدارة الأنظمة عن بعد عبر المنفذ 23 بنص غير مشفر، وهو بروتوكول مهجور ومستبدل بـ SSH.
  * ولكن في اختبار الاختراق، يُستخدم برنامج العميل `telnet <TARGET_IP> <PORT>` كأداة قوية لفتح اتصال TCP خام مع أي منفذ يستمع على الخادم (سواء منفذ ويب 80، بريد 25، إلخ).
* **آلية استخراج الراية (Banner Grabbing):**
  * عند الاتصال بالمنفذ 80، يتم إرسال طلب HTTP يدوي:
    ```http
    GET / HTTP/1.1
    Host: MACHINE_IP

    ```
  * يرد الخادم ببيانات الاستجابة (HTTP Response)، وتتضمن الترويسة القياسية `Server:`.
  * تكشف هذه الترويسة اسم برنامج خادم الويب: **`Apache`** وإصداره الدقيق: **`2.4.61`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** اسم خادم الويب مفتوح المصدر الأكثر شهرة في العالم: `Apache`.
* **Hint 2 (للسؤال 2):** رقم الإصدار مكون من 3 أرقام مفصولة بنقاط: `2.4.61`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **فتح جلسة الاتصال عبر Telnet بالمنفذ 80:**
   ```bash
   telnet 10.10.x.x 80
   ```
   * رد الطرفية:
     ```text
     Trying 10.10.x.x...
     Connected to 10.10.x.x.
     Escape character is '^]'.
     ```
2. **إرسال طلب HTTP GET يدوي:**
   كتابة السطرين التاليين ثم الضغط على زر Enter مرتين:
   ```text
   GET / HTTP/1.1
   Host: 10.10.x.x

   ```
3. **تحليل ترويسة الاستجابة (HTTP Response Headers):**
   ```text
   HTTP/1.1 200 OK
   Date: Wed, 18 Nov 2026 23:04:12 GMT
   Server: Apache/2.4.61 (Ubuntu)
   Content-Type: text/html; charset=UTF-8
   ```
4. **استخراج النتائج:**
   * اسم الخادم (Server Name): **`Apache`**
   * إصدار الخادم (Server Version): **`2.4.61`**

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *...What is the name of the running server?*
  * **Answer:** `Apache`
* **Question 2:** *What is the version of the running server (on port 80 of the VM)?*
  * **Answer:** `2.4.61`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Banner Grabbing Evidence via Telnet:
Target: MACHINE_IP:80
Request: GET / HTTP/1.1
Response Header: Server: Apache/2.4.61 (Ubuntu)
Extracted Daemon: Apache
Extracted Version: 2.4.61
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استخراج الرايات:**
> *"Banner grabbing is the fastest path to targeted exploitation; knowing a server is running Apache 2.4.61 immediately narrows your CVE search space to known bugs for that specific release."*
> استخراج الرايات هو أسرع طريق للاستغلال الموجه؛ فمعرفة أن الخادم يشغل Apache 2.4.61 يقلص فوراً نطاق بحثك عن الثغرات (CVEs) إلى العيوب المسجلة لهذا الإصدار بالذات.
