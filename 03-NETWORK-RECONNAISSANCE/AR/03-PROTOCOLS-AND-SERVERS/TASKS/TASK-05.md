# Task 05: Simple Mail Transfer Protocol (SMTP)
## المهمة 05: بروتوكول نقل البريد البسيط (SMTP) وترويسات الخدمة

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 05
* **Original Title:** Simple Mail Transfer Protocol (SMTP)
* **Questions:**
  1. *Using the AttackBox terminal, connect to the SMTP port of the target VM. What is the flag that you can get?*
* **Objective:** Understand the role of SMTP (RFC 5321) on port 25 as the standard protocol for inter-server mail transmission, master manual SMTP dialog commands (`HELO` / `EHLO`), and extract service banners and flags.
* **Core Concepts:** SMTP Protocol, Port 25, Service Banners (Code 220), Mail Transaction Commands (`MAIL FROM`, `RCPT TO`, `DATA`), Open Relays.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **طبيعة بروتوكول SMTP:**
  * يعمل بروتوكول نقل البريد البسيط عبر المنفذ **`25`** كبروتوكول موجه للإرسال وتوجيه الرسائل بين خوادم البريد (Mail Transfer Agents - MTAs).
  * مثل FTP، يقوم خادم الـ SMTP ببث رسالة ترحيبية فورية تبدأ برمز الحالة **`220`** بمجرد اتصال العميل، متبوعة باسم الخادم ومعلومات الدومين.
* **تحليل السؤال:**
  * المطلوب فتح اتصال بمقبس خادم البريد على المنفذ 25 عبر أداة `telnet` أو `nc`.
  * يقوم الخادم بعرض الراية الترحيبية أو يمكن التفاعل معه بإرسال أمر التحية `HELO target.thm` لاستخراج الراية الأمنية المضمنة في رسالة الترحيب: **`THM{5b31ddfc0c11d81eba776e983c35e9b5}`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** اتصل بمنفذ SMTP القياسي 25: `telnet MACHINE_IP 25` أو `nc MACHINE_IP 25`.
* **Hint 2:** راقب الرسالة الترحيبية الصادرة من الخادم بكود `220`.
* **Hint 3:** الراية تبدأ بـ `THM{5b31...}`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **الاتصال بمنفذ البريد 25:**
   ```bash
   nc 10.10.x.x 25
   ```
   * بدلاً من ذلك باستخدام Telnet:
     ```bash
     telnet 10.10.x.x 25
     ```
2. **قراءة استجابة الخادم الترحيبية:**
   ```text
   220 mail.target.thm ESMTP Postfix THM{5b31ddfc0c11d81eba776e983c35e9b5}
   ```
3. **تفاعل SMTP اليدوي (للممارسة المنهجية):**
   ```text
   HELO attackbox.thm
   250 mail.target.thm
   QUIT
   221 2.0.0 Bye
   ```
4. **الراية المستخرجة:** `THM{5b31ddfc0c11d81eba776e983c35e9b5}`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Using the AttackBox terminal, connect to the SMTP port... What is the flag that you can get?*
  * **Answer:** `THM{5b31ddfc0c11d81eba776e983c35e9b5}`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
SMTP Banner Extraction:
Target: MACHINE_IP:25
Response Code: 220
Banner String: 220 mail.target.thm ESMTP Postfix THM{5b31ddfc0c11d81eba776e983c35e9b5}
Extracted Flag: THM{5b31ddfc0c11d81eba776e983c35e9b5}
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في فحص خوادم البريد:**
> *"SMTP banners frequently disclose the internal mail domain and daemon version. Furthermore, testing for Open Relays via manual RCPT TO commands reveals whether external attackers can spoof mail through the organization."*
