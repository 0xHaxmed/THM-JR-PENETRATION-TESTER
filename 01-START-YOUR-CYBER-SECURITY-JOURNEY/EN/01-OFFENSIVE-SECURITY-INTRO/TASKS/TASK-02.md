# Task 02: Starting the Lab
## المهمة 02: تشغيل المعمل واستكشاف تطبيق FakeBank

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** Starting the Lab
* **Question:** *What is the bank account number in the FakeBank application?*
* **Objective:** Deploy the interactive web laboratory, interact with the simulated target portal (`FakeBank`), and identify target identifiers.
* **Core Concept:** Target reconnaissance and asset identification.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **ما هو المطلوب؟** تشغيل بيئة المعمل عبر زر "View Site" الأخضر، وتفحص واجهة تطبيق البنك الافتراضي (`FakeBank`) للبحث عن رقم الحساب المصرفي الخاص بالمستخدم المستهدف.
* **أين تتواجد هذه المعلومة في التطبيق؟** في تطبيقات البنوك والحسابات المالية، تظهر أرقام الحسابات عادة في أعلى يسار لوحة التحكم أو بطاقة العميل الرئيسية.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** اضغط على زر `View Site` لفتح المتصفح المدمج في الغرفة.
* **Hint 2:** انظر إلى الجزء العلوي من شاشة تطبيق البنك بجوار اسم صاحب الحساب أو فوق الرصيد الحالي.
* **Hint 3:** رقم الحساب مكون من 4 أرقام عددية متطابقة في البداية والنهاية.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **الخطوة 1:** الضغط على زر `View Site` لتوليد واجهة تطبيق البنك على الرابط الداخلي: `http://fakebank.thm`.
2. **الخطوة 2:** بمجرد فتح الواجهة، تظهر بطاقة الحساب المصرفي للمستخدم التجريبي، وتظهر البيانات التالية:
   * **Account Holder:** User
   * **Account Number:** `8881`
   * **Current Balance:** `-$100.00` (رصيد سالب يتطلب تسوية).
3. **الخطوة 3:** تسجيل رقم الحساب `8881` لاستخدامه لاحقاً كهدف للتحويل أو إيداع الأموال في المراحل الهجومية التالية.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Answer:** `8881`

---

### Stage F — Evidence & Verification | الأدلة والتحقق
* لقطة واجهة التطبيق في المعمل:
  ```text
  FakeBank Online Portal
  ---------------------------------
  Account Number: 8881
  Current Balance: -$100.00
  Status: Overdrawn
  ---------------------------------
  ```

---

### Stage G — Learning Takeaway | الدرس المستفاد عملياً
في بداية أي اختبار اختراق لتطبيق ويب، يجب جمع كل المعرفات الفرعية (Identifiers / IDs) مثل أرقام الحسابات وأسماء المستخدمين، لأن هذه المعرفات هي التي تُستخدم كمدخلات (Parameters) لاختبار ثغرات منطق الأعمال وتجاوز الصلاحيات (IDOR).
