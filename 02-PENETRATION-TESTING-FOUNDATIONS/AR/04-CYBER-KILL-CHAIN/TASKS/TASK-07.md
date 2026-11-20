# Task 07: Command and Control (C2)
## المهمة 07: مرحلة القيادة والتحكم وقنوات الاتصال المشفرة

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 07
* **Original Title:** Command and Control (C2)
* **Questions:**
  1. *Tunneling communication via DNS queries*
  2. *Encrypted protocol commonly used to blend with normal traffic*
* **Objective:** Understand Phase 6 of the Cyber Kill Chain (C2), examine covert channel communication, DNS tunneling mechanics, and encrypted HTTPS beaconing.
* **Core Concept:** Command & Control channels, Beaconing, DNS tunneling, HTTPS evasion.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول:**
  ما هي التقنية التي يستخدمها المهاجم لتمرير وتوجيه حركة مرور الأوامر والبيانات داخل استعلامات أسماء النطاقات (DNS Queries) لتجاوز الجدران النارية التي تسمح بحركة الـ DNS؟
  * تسمى هذه التقنية بنفق الـ DNS: **`DNS tunnelling`**.
* **تحليل السؤال الثاني:**
  ما هو البروتوكول المشفر المعياري لحركة الويب الذي تستخدمه برمجيات الـ C2 للاندماج والتخفي وسط حركة المرور الطبيعية للشركة وتفادي فحص محتوى الحزم؟
  * هذا هو بروتوكول: **`HTTPS`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** الكلمة تبدأ بـ DNS متبوعة بـ tunnelling: `DNS tunnelling`.
* **Hint 2 (للسؤال 2):** الحروف الإنجليزية الأربعة للويب المشفر: `HTTPS`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **قنوات القيادة والتحكم (C2 Infrastructure):**
   * تقوم برمجية الـ Agent بإرسال إشارات دورية (Beacons) لخادم المهاجم لتلقي التعليمات.
2. **تجاوز قيود الخروج (Egress Filtering):**
   * استخدام `DNS tunnelling` لتمرير البيانات عندما تكون كافة المنافذ الأخرى مغلقة.
   * استخدام `HTTPS` لتشفير البيانات وتفادي أجهزة الـ IDS/IPS التقليدية.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Tunneling communication via DNS queries*
  * **Answer:** `DNS tunnelling`
* **Question 2:** *Encrypted protocol commonly used to blend with normal traffic*
  * **Answer:** `HTTPS`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Phase 6 C2 Protocol Audit:
[+] Covert Channel: DNS tunnelling
[+] Encrypted Egress: HTTPS
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في قنوات الـ C2:**
> *"Adversaries do not invent exotic protocols for C2; they hide in plain sight using enterprise-trusted protocols like DNS and HTTPS."*
