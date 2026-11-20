# Task 06: Post Office Protocol 3 (POP3)
## المهمة 06: بروتوكول مكتب البريد (POP3) والاستعلام عن الرسائل

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 06
* **Original Title:** Post Office Protocol 3 (POP3)
* **Questions:**
  1. *Connect to the VM ( MACHINE_IP ) at the POP3 port. Authenticate using the username frank and password D2xc9CgD . What is the response you get to STAT ?*
  2. *How many email messages are available to download via POP3 on MACHINE_IP ?*
* **Provided Credentials in Prompt:**
  * **Username:** `frank`
  * **Password:** `D2xc9CgD`
* **Objective:** Understand the mail retrieval workflow of POP3 (RFC 1939) on port 110, execute manual POP3 authentication (`USER` / `PASS`), issue mailbox interrogation commands (`STAT`, `LIST`), and interpret status responses.
* **Core Concepts:** POP3 Protocol, Port 110, Server Status (`+OK` / `-ERR`), Mailbox Statistics (`STAT`), Download-and-Delete Architecture.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **معمارية بروتوكول POP3:**
  * بينما يُستخدم SMTP لإرسال البريد بين الخوادم، يُستخدم بروتوكول POP3 (المنفذ **`110`**) ليقوم المستخدم بسحب وتنزيل رسائله من الخادم إلى جهازه المحلي.
  * بروتوكول POP3 يعتمد على مبدأ "التنزيل ثم الحذف من الخادم" (Download and Delete).
  * كل استجابة ناجحة من خادم الـ POP3 تبدأ بعبارة **`+OK`**، وأي خطأ يبدأ بـ **`-ERR`**.
* **تحليل الحوار البرمجي في POP3:**
  1. الاتصال بالمنفذ 110.
  2. إرسال أمر المستخدم: `USER frank`.
  3. إرسال أمر كلمة المرور: `PASS D2xc9CgD`.
  4. بمجرد قبول الدخول، يرسل العميل أمر الإحصائيات: **`STAT`** (Status).
  5. يرد الخادم بصيغة: `+OK <NUMBER_OF_MESSAGES> <TOTAL_MAILBOX_SIZE_IN_OCTETS>`.
  * الرد المستلم على أمر `STAT` هو: **`+OK 0 0`**.
  * يوضح الرد أن الرقم الأول يمثل عدد الرسائل المتاحة وهو **`0`** رسالة، والرقم الثاني يمثل الحجم بالبايت وهو `0`.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** اتصل بالمنفذ 110 عبر `telnet MACHINE_IP 110` أو `nc MACHINE_IP 110`.
* **Hint 2 (للسؤال 1):** أرسل `USER frank` ثم `PASS D2xc9CgD` ثم اكتب `STAT` واكتب الرد الكامل كما هو: `+OK 0 0`.
* **Hint 3 (للسؤال 2):** الرقم الأول بعد `+OK` يمثل عدد الرسائل: `0`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **فتح اتصال بمقبس POP3 على المنفذ 110:**
   ```bash
   telnet 10.10.x.x 110
   ```
2. **تنفيذ المصادقة وإرسال أمر STAT:**
   ```text
   +OK Dovecot ready.
   USER frank
   +OK
   PASS D2xc9CgD
   +OK Logged in.
   STAT
   +OK 0 0
   QUIT
   +OK Logging out.
   ```
3. **تحليل النتائج:**
   * الاستجابة لأمر `STAT`: **`+OK 0 0`**.
   * عدد الرسائل المتاحة للتنزيل: **`0`**.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *...What is the response you get to STAT ?*
  * **Answer:** `+OK 0 0`
* **Question 2:** *How many email messages are available to download via POP3 on MACHINE_IP ?*
  * **Answer:** `0`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
POP3 Protocol Dialog Verification:
Server: Dovecot POP3
Auth: USER frank / PASS D2xc9CgD -> +OK Logged in.
Command: STAT
Raw Server Response: +OK 0 0
Mail Message Count: 0
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في فحص بروتوكولات البريد:**
> *"Legacy POP3 on port 110 passes authentication credentials across the wire in cleartext just like FTP and Telnet. Always ensure mail servers enforce POP3S (POP3 over TLS) on port 995."*
