# Task 04: Stop The Hack
## المهمة 04: تكوين الجدار الناري واحتواء التهديد واقتناص الراية

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 04
* **Original Title:** Stop The Hack
* **Questions:**
  1. *Copy the flag that appears when the hacker has been stopped*
* **Objective:** Apply an inbound firewall rule blocking the attacker's IP (`32.122.195.63`), verify traffic termination, and retrieve the verification flag.
* **Core Concept:** Incident containment, firewall rule enforcement, attack mitigation.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **إجراءات الاستجابة للحوادث (Incident Response):**
  بمجرد تأكيد أن العنوان `32.122.195.63` ينفذ هجوماً تخمينياً على لوحة الإدارة، فإن الإجراء الفوري الأول هو **الاحتواء (Containment)**:
  * إدخال عنوان IP المهاجم في واجهة إدارة الجدار الناري (Firewall Configuration Panel).
  * اختيار الإجراء: **`BLOCK`** أو **`DENY`**.
  * حفظ القاعدة وتطبيقها فوراً.
  * بمجرد سريان القاعدة، تنقطع اتصالات المهاجم وتظهر رسالة نجاح الاحتواء مصحوبة براية الغرفة.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** انتقل إلى تبويب Firewall في المعمل التفاعلي.
* **Hint 2:** الصق عنوان IP الخاص بالمهاجم `32.122.195.63` واضغط على `Block IP`.
* **Hint 3:** انسخ الراية التي تبدأ بـ `THM{` والمحاطة بقوسين.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. الانتقال إلى قسم الجدار الناري (Firewall Panel) في تطبيق المعمل.
2. إضافة قاعدة الحظر:
   * **Rule Type:** Inbound Drop
   * **Source IP:** `32.122.195.63`
   * **Action:** Block Connection
3. الضغط على زر تطبيق القاعدة.
4. يظهر تنبيه فوري يفيد بقطع كافة الحزم القادمة من المهاجم بنجاح:
   ```text
   SUCCESS: Threat Neutralized!
   Firewall rule applied to 32.122.195.63.
   Flag: THM{THREAT-BLOCKED}
   ```

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Copy the flag that appears when the hacker has been stopped*
  * **Answer:** `THM{THREAT-BLOCKED}`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Mitigation Verification Log:
[+] Threat Source: 32.122.195.63
[+] Firewall Action: REJECT / DROP
[+] Connection State: Terminated
[+] Capture Flag: THM{THREAT-BLOCKED}
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في الاستجابة للحوادث:**
> *"Containment buys time. Immediately severing the adversary's communication channel stops data exfiltration while digital forensics investigates the root cause."*
