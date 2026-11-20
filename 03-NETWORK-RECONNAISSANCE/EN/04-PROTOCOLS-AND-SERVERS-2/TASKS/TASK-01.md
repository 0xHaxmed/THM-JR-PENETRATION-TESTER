# Task 01: Introduction
## المهمة 01: مدخل إلى هجمات البروتوكولات النصية وآليات التشفير

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 01
* **Original Title:** Introduction
* **Questions:**
  1. *We suggest that you start the AttackBox to be ready to follow along with the examples in this room. You can optionally connect via OpenVPN, but you will need to start the attached VM.*
* **Objective:** Understand the threat model surrounding cleartext legacy protocols, transition from reconnaissance to active exploitation (Sniffing, MITM, Password Attacks), and master modern cryptographic mitigations (SSH, TLS).
* **Core Concepts:** Cleartext Protocol Threat Modeling, Eavesdropping, Man-in-the-Middle (MITM), Cryptographic Transition.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل التحول من الاستطلاع إلى الهجوم:**
  * في الغرفة السابقة (`Protocols and Servers`)، قمنا بفحص بروتوكولات Telnet و FTP و HTTP و SMTP و POP3 و IMAP ولاحظنا كيف تعمل على مقابس نصوص واضحة.
  * في هذه الغرفة (`Protocols and Servers 2`)، ننتقل إلى الجانب الهجومي العملي:
    1. كيف يستغل المهاجم غياب التشفير لاعتراض وقراءة الحزم عبر أدوات التقاط الشبكة (`tcpdump` و `wireshark`).
    2. كيف يتم التموضع في مسار البيانات عبر هجمات تسميم الـ ARP (ARP Poisoning).
    3. كيف تُكسر كلمات المرور الضعيفة عبر هجمات القواميس والقوة الغاشمة (Brute-Force) باستخدام أداة `Hydra`.
    4. كيف تؤمن بروتوكولات العصر الحديث مثل SSH و TLS هذه الاتصالات ضد التنصت والتلاعب.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** تأكد من تشغيل الآلة الافتراضية التابعة للغرفة، واضغط على زر `Completed` للانتقال لهجمات التنصت.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
* استيعاب محاور الهجوم والدفاع في الغرفة:
  * **الهجوم 1 (Sniffing):** التقاط الحزم السابحة في نطاق البث (Broadcast/Collision Domain).
  * **الهجوم 2 (MITM):** إجبار حركة المرور على المرور بجهاز الفاحص عبر تسميم جدول الـ ARP.
  * **الهجوم 3 (Password Attacks):** تخمين كلمات المرور آلياً عبر الشبكة لخدمات مثل IMAP.
  * **الدفاع المعاصر (Modern Defenses):** فرض بروتوكول SSH وتشفير الـ TLS (بما في ذلك تقنيات تأمين الـ DNS مثل DoT).

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *We suggest that you start the AttackBox...*
  * **Answer:** `No answer needed`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
* بيئة المعمل مهيأة بنجاح ومجهزة بكافة الأدوات المطلوبة.

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في أمن البروتوكولات:**
> *"Knowing that a protocol is cleartext is academic knowledge; demonstrating packet sniffing and credential cracking in front of stakeholders is what enforces cryptographic compliance."*
