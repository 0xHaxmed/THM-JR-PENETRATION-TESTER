# Task 05: Secure Shell (SSH)
## المهمة 05: بروتوكول الشل الآمن (SSH) ونقل الملفات المشفر

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 05
* **Original Title:** Secure Shell (SSH)
* **Questions:**
  1. *Use SSH to connect to MACHINE_IP as mark with the password XBtc49AB . Using uname -r , find the Kernel release?*
  2. *Use SSH to download the file book.txt from the remote system. How many KBs did scp display as download size?*
* **Provided Credentials in Prompt:**
  * **Target Host:** `MACHINE_IP`
  * **Username:** `mark`
  * **Password:** `XBtc49AB`
* **Objective:** Master remote system administration via SSH (port 22), verify host key authenticity (`~/.ssh/known_hosts`), execute remote reconnaissance commands (`uname -r`), and utilize the `scp` utility for encrypted file exfiltration/transfer.
* **Core Concepts:** SSH (RFC 4253), Asymmetric Host Keys, Symmetric Session Ciphers, Kernel Version Discovery (`uname -r`), Secure Copy Protocol (`scp`).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **معمارية بروتوكول SSH (Secure Shell):**
  * صُمم SSH كبديل مشفر وآمن بالكامل لبروتوكولات Telnet و rlogin القديمة على المنفذ **`22`**.
  * يستخدم التشفير غير المتماثل (Asymmetric Cryptography) لتبادل المفاتيح والتحقق من هوية الخادم، ثم ينتقل إلى التشفير المتماثل فائق السرعة (مثل AES أو ChaCha20) لتشفير جلسة العمل.
* **تحليل السؤال الأول (إصدار النواة Kernel Release):**
  * تسجيل الدخول عبر أمر `ssh mark@MACHINE_IP` وإدخال كلمة المرور `XBtc49AB`.
  * بمجرد الحصول على الشل التفاعلي، ننفذ الأمر:
    ```bash
    uname -r
    ```
  * يعرض الأمر رقم إصدار نواة لينكس الدقيق، وهو: **`5.15.0-119-generic`**.
  * معرفة إصدار النواة خطوة محورية في اختبار الاختراق لأنها تكشف ما إذا كان النظام مصاباً بثغرات تصعيد صلاحيات معروفة في الكيرنل (Kernel Exploits مثل Dirty COW أو Dirty Pipe).
* **تحليل السؤال الثاني (حجم الملف المنقول عبر scp):**
  * أداة `scp` (Secure Copy) تستخدم نفق الـ SSH المشفر لنقل الملفات بأمان بين الأجهزة.
  * تنفيذ أمر تنزيل الملف `book.txt` إلى المجلد الحالي:
    ```bash
    scp mark@MACHINE_IP:book.txt .
    ```
  * أثناء التنزيل، يعرض شريط التقدم في الطرفية الحجم المنقول بالكيلوبايت: **`415`** KB.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** اكتب `ssh mark@MACHINE_IP` وأدخل كلمة المرور `XBtc49AB`، ثم اكتب `uname -r`. الإصدار يبدأ بـ `5.15.0-`.
* **Hint 2 (للسؤال 2):** استخدم الأمر `scp mark@MACHINE_IP:book.txt .` ولاحظ الرقم المعروض بجانب KB (وهو 415).

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **الاتصال بالخادم عبر SSH:**
   ```bash
   ssh mark@10.10.x.x
   ```
   * عند السؤال عن التحقق من البصمة (Host Key Fingerprint)، كتابة `yes`.
   * إدخال كلمة المرور: `XBtc49AB`.
2. **استخراج إصدار النواة (Kernel Release):**
   ```bash
   mark@target:~$ uname -r
   5.15.0-119-generic
   ```
   * الخروج من الجلسة: `exit`.
3. **تنزيل الملف عبر Secure Copy (scp):**
   ```bash
   scp mark@10.10.x.x:book.txt .
   ```
   * مخرجات شاشة الطرفية:
     ```text
     book.txt                   100%  415KB   4.2MB/s   00:00
     ```
   * الحجم المعروض بالكيلوبايت هو: **`415`**.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *...find the Kernel release?*
  * **Answer:** `5.15.0-119-generic`
* **Question 2:** *...How many KBs did scp display as download size?*
  * **Answer:** `415`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
SSH Session & SCP Transfer Logs:
Target: 10.10.x.x:22 (SSH-2.0-OpenSSH)
Authenticated User: mark
Kernel Release: 5.15.0-119-generic (Verified via uname -r)
File Transfer: book.txt
Reported Download Size: 415 KB
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استطلاع أنظمة Linux:**
> *"The command 'uname -r' is the definitive first command every penetration tester executes upon gaining an SSH shell; matching the kernel build to known public exploits is the fastest privilege escalation path."*
