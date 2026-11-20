# Task 04: Attack the Admin Page
## المهمة 04: استغلال الصفحة الإدارية وتعديل الرصيد المصرفي

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 04
* **Original Title:** Attack the Admin Page
* **Questions:**
  1. *What could FakeBank do to make this bank-transfer page secure? Pick the most effective technique: Require login Make the URL harder to guess Move the page to another server*
  2. *When Mrs. G Benjamin's account balance turns positive, a pop-up with green text appears. Enter the green words as the answer (ALL CAPS)*
* **Objective:** Exploit missing access control on the discovered transfer portal to modify balance, capture the success flag, and determine proper architectural remediation.
* **Core Concept:** Broken Access Control, Authentication Enforcement, Privilege Misconfiguration.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول (الحل الأمني الفعال):**
  المشكلة الجذرية في صفحة `http://fakebank.thm/bank-transfer` هي أنها متاحة للعامة دون أي تحقق من هوية المستخدم أو صلاحياته.
  * جعل الرابط صعب التخمين (Security through Obscurity) ليس حلاً أمنياً فعالاً.
  * نقل الصفحة لخادم آخر لا يمنع الوصول إذا كانت غير محمية.
  * **الحل الأمني الصحيح والمباشر:** فرض المصادقة وتسجيل الدخول (**`Require login`**).
* **تحليل السؤال الثاني (الراية الخضراء):**
  بعد إرسال طلب التحويل لحساب المستخدم التجريبي ليصبح رصيده موجباً، تظهر نافذة منبثقة بنص أخضر بحروف كبيرة: **`BANK-HACKED`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** ما هو الإجراء الأساسي الذي تفرضه أي مؤسسة مالية لمنع الغرباء من تحويل الأموال؟ إجبار المستخدم على تسجيل الدخول: `Require login`.
* **Hint 2 (للسؤال 2):** الكلمتان المكتوبتان باللون الأخضر بالأحرف الكبيرة بينهما شرطة: `BANK-HACKED`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **الوصول لصفحة التحويل:**
   * فتح الرابط: `http://fakebank.thm/bank-transfer`.
   * ملاحظة عدم وجود أي طلب لتسجيل الدخول (Broken Access Control).
2. **تنفيذ التحويل المالي:**
   * **Transfer To:** `8881`
   * **Amount:** `200`
   * الضغط على زر التحويل `Send Funds`.
3. **التقاط الراية:**
   * تظهر نافذة تأكيد خضراء تحتوي على العبارة: `BANK-HACKED`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What could FakeBank do to make this bank-transfer page secure? Pick the most effective technique: Require login Make the URL harder to guess Move the page to another server*
  * **Answer:** `Require login`
* **Question 2:** *When Mrs. G Benjamin's account balance turns positive, a pop-up with green text appears. Enter the green words as the answer (ALL CAPS)*
  * **Answer:** `BANK-HACKED`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
FakeBank Transfer Execution:
[+] Endpoint: /bank-transfer
[+] Auth Check: None (Bypass Success)
[+] Action: Balance updated to positive
[+] Alert Box: BANK-HACKED
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في حماية التطبيقات المصرفية:**
> *"Obscurity is not security. Hiding a sensitive endpoint (/bank-transfer) never prevents discovery. Only strict, server-side authentication and role-based authorization can protect critical assets."*
