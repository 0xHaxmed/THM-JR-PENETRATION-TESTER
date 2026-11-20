# Task 07: Internet Message Access Protocol (IMAP)
## المهمة 07: بروتوكول الوصول لرسائل الإنترنت (IMAP) والمنفذ الافتراضي

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 07
* **Original Title:** Internet Message Access Protocol (IMAP)
* **Questions:**
  1. *What is the default port used by IMAP?*
* **Objective:** Understand the server-synchronized mail architecture of IMAP (RFC 3501), contrast it with POP3, and identify its standard default network port.
* **Core Concepts:** IMAP Protocol, Port 143, Mailbox Synchronization, Multiple Client Access, Cleartext vs IMAPS (Port 993).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **الفرق الهندسي الجوهري بين IMAP و POP3:**
  * **POP3:** يقوم بتنزيل الرسائل إلى جهاز المستخدم المحلي ثم يحذفها من الخادم، مما يجعله غير مناسب للاستخدام عبر أجهزة متعددة (هاتف، لابتوب، جهاز عمل).
  * **IMAP (Internet Message Access Protocol):** يحتفظ بالرسائل ومجلدات البريد على الخادم الرئيسي بصورة دائمة، ويقوم بمزامنة حالات الرسائل (مقروءة، محذوفة، مسودة) بين كافة الأجهزة المتصلة.
* **المنفذ المعياري لـ IMAP:**
  * خصصت هيئة IANA المنفذ رقم **`143`** (TCP) لبروتوكول IMAP غير المشفر (Cleartext IMAP).
  * في البيئات الحديثة، يُستبدل دائماً بالنسخة المشفرة **IMAPS (IMAP over SSL/TLS)** التي تعمل على المنفذ **`993`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** يمكنك التحقق من المنفذ في لينكس عبر فحص ملف `/etc/services` بالبحث عن imap.
* **Hint 2:** المنفذ مكون من 3 أرقام ويبدأ بـ 1 وينتهي بـ 3: `143`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **التحقق من المنفذ في ملف الخدمات:**
   ```bash
   grep -w "imap" /etc/services
   ```
   * مخرجات النظام:
     ```text
     imap2           143/tcp         imap            # Interim Mail Access Proto v2
     imap2           143/udp         imap
     ```
2. المنفذ الافتراضي المعياري لبروتوكول IMAP هو: **`143`**.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What is the default port used by IMAP?*
  * **Answer:** `143`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Protocol Port Binding:
Protocol: Internet Message Access Protocol (IMAP)
Transport: TCP
Default Port: 143
Encrypted Variant: IMAPS (Port 993)
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في حماية صناديق البريد:**
> *"IMAP on port 143 allows adversaries on the same LAN or routing path to capture mail credentials and read confidential company emails in real time; always mandate port 993 (IMAPS)."*
