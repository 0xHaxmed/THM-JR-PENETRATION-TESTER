# Task 04: File Transfer Protocol (FTP)
## المهمة 04: بروتوكول نقل الملفات (FTP) والمصادقة واسترجاع الراية

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 04
* **Original Title:** File Transfer Protocol (FTP)
* **Questions:**
  1. *Using an FTP client, connect to the VM and try to recover the flag file. What is the flag?*
* **Provided Credentials in Prompt:**
  * **Username:** `frank`
  * **Password:** `D2xc9CgD`
* **Objective:** Understand the control/data dual-channel architecture of FTP (RFC 959), authenticate using valid credentials over port 21, navigate server directories, download sensitive files, and analyze cleartext protocol vulnerabilities.
* **Core Concepts:** FTP Dual-Channel Architecture (Command Port 21 vs Data Port 20/Passive), Cleartext Credential Transmission (`USER` / `PASS`), File Retrieval (`RETR`).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **معمارية بروتوكول FTP القائمة على قناتين (Dual-Channel):**
  1. **قناة التحكم والأوامر (Command Channel):** مقبس اتصال TCP دائم على المنفذ `21`، تُرسل عبره أوامر المصادقة والتصفح (`USER`, `PASS`, `LIST`, `RETR`, `QUIT`).
  2. **قناة البيانات (Data Channel):** اتصال مستقل يُفتح لنقل محتوى الملفات أو قوائم المجلدات (في الوضع النشط Active يُفتح من منفذ 20، وفي الوضع الخامل Passive يُفتح على منفذ عشوائي عالي).
* **سيناريو المهمة:**
  * الهدف يشغل خادم FTP على المنفذ 21.
  * المعطيات توفر حساب مستخدم شرعي (`frank` : `D2xc9CgD`).
  * المطلوب الاتصال بالعميل، تسجيل الدخول، استعراض الملفات عبر أمر `ls`، وتنزيل ملف الراية وقراءة محتواه: **`THM{364db6ad0e3ddfe7bf0b1870fb06fbdf}`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** استخدم عميل الـ FTP القياسي في لينكس: `ftp MACHINE_IP`.
* **Hint 2:** أدخل اسم المستخدم `frank` ثم كلمة المرور `D2xc9CgD`.
* **Hint 3:** اكتب `ls` لمشاهدة الملفات، ثم `get flag.txt` لتنزيله، ثم اخرج بـ `quit` واقرأ الملف عبر `cat flag.txt`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **بدء جلسة الاتصال بالـ FTP:**
   ```bash
   ftp 10.10.x.x
   ```
2. **المصادقة والتنقل داخل الخادم:**
   ```text
   Connected to 10.10.x.x.
   220 ProFTPD Server ready.
   Name (10.10.x.x:user): frank
   331 Password required for frank
   Password: [D2xc9CgD]
   230 User frank logged in
   Remote system type is UNIX.
   Using binary mode to transfer files.
   ftp> ls
   200 PORT command successful
   150 Opening ASCII mode data connection for file list
   -rw-r--r--   1 frank    frank          38 Nov 18 22:15 flag.txt
   226 Transfer complete
   ftp> get flag.txt
   local: flag.txt remote: flag.txt
   200 PORT command successful
   150 Opening BINARY mode data connection for flag.txt (38 bytes)
   226 Transfer complete
   38 bytes received in 0.00 secs (12.4 kB/s)
   ftp> quit
   221 Goodbye.
   ```
3. **قراءة الراية من سطر الأوامر:**
   ```bash
   cat flag.txt
   ```
   * مخرجات الملف:
     `THM{364db6ad0e3ddfe7bf0b1870fb06fbdf}`

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Using an FTP client, connect to the VM and try to recover the flag file. What is the flag?*
  * **Answer:** `THM{364db6ad0e3ddfe7bf0b1870fb06fbdf}`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
FTP Session Extraction:
Auth: frank:D2xc9CgD -> Success (Code 230)
Recovered File: flag.txt
Extracted Flag: THM{364db6ad0e3ddfe7bf0b1870fb06fbdf}
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في أمن نقل الملفات:**
> *"FTP transmits both the USER and PASS commands in unencrypted cleartext across port 21. In an enterprise assessment, sniffing FTP traffic yields valid user credentials; modern environments must replace FTP with SFTP (SSH-based) or FTPS (TLS-wrapped)."*
