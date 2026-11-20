# Task 03: Find Hidden Pages
## المهمة 03: اكتشاف الصفحات والمسارات الإدارية المخفية عبر Dirb

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 03
* **Original Title:** Find Hidden Pages
* **Questions:**
  1. *Dirb found two URLs, which one of these is more likely to give us access to FakeBank's internal tools?*
* **Objective:** Understand how automated directory brute-forcing tools operate and evaluate discovered endpoints to prioritize high-value targets (administrative and transfer portals).
* **Core Concept:** Web content discovery, directory enumeration (`dirb`), target triage.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال:** 
  عند تشغيل أداة `dirb` ضد نطاق البنك الافتراضي `http://fakebank.thm`، تكتشف الأداة مسارين (URLs):
  1. المسار الأول: `http://fakebank.thm/images` (مجلد وسائط وصور ثابتة غير مفيدة في الهجوم).
  2. المسار الثاني: `http://fakebank.thm/bank-transfer` (مسار داخلي واضح لمعالجة التحويلات المالية والمعاملات المصرفية).
* **تقييم الأهداف (Triage):** المسار الذي يوفر وصولاً للأدوات الداخلية الحساسة للبنك هو حتماً مسار التحويلات المصرفية: **`http://fakebank.thm/bank-transfer`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** قارن بين مجلد الصور ومسار تحويل الأموال؛ أيهما يمثل بوابة لوظائف بنكية حساسة؟
* **Hint 2:** اكتب الرابط الكامل الذي يبدأ بـ `http://` وينتهي بـ `/bank-transfer`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **تنفيذ فحص المسارات عبر أداة `dirb`:**
   في الطرفية المتاحة بالمعمل:
   ```bash
   dirb http://fakebank.thm
   ```
2. **تحليل مخرجات الفحص:**
   ```text
   -----------------
   DIRB v2.22    
   -----------------
   URL_BASE: http://fakebank.thm/
   WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

   ==> DIRECTORY: http://fakebank.thm/images/
   + http://fakebank.thm/bank-transfer (CODE:200|SIZE:1420)
   ```
3. **الاستنتاج الميداني:**
   المسار `/bank-transfer` رد بكود الحالة `200 OK`، وهو ما يشير إلى صفحة تفاعلية لإجراء المعاملات المصرفية الداخلية.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Dirb found two URLs, which one of these is more likely to give us access to FakeBank's internal tools?*
  * **Answer:** `http://fakebank.thm/bank-transfer`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Dirb Discovery Output:
Discovered URL #1: http://fakebank.thm/images
Discovered URL #2: http://fakebank.thm/bank-transfer (High Impact / Internal Tool)
Verified Answer: http://fakebank.thm/bank-transfer
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استكشاف محتوى الويب:**
> *"Automated fuzzers discover dozens of endpoints; a skilled penetration tester immediately filters out static directories (/images, /css) and prioritizes business logic endpoints (/transfer, /admin, /api)."*
