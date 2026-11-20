# Task 05: Technical Documentation (MAN)
## المهمة 05: البحث في التوثيق التقني وصفحات الأدلة (MAN Pages)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 05
* **Original Title:** Technical Documentation (MAN)
* **Questions:**
  1. *What is the example command?*
* **Objective:** Read and interpret Unix manual pages (`man`) to extract exact syntax, parameter configurations, and usage examples.
* **Core Concept:** Linux command manual navigation, Netcat syntax, CLI documentation discovery.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل صفحات الأدلة:**
  توفر أنظمة Linux توثيقاً داخلياً متكاملاً عبر الأمر `man <command>`. تحتوي الصفحات في نهايتها عادة على قسم أمثلة الاستخدام (**EXAMPLES**).
  * السؤال يطلب تحديد أمر المثال المذكور في دليل أداة `nc` (Netcat) لإجراء اتصال بهدف محدد على منفذ معين.
  * يوضح نمط الإجابة المطلوب: `__ ____._______.___ __` (أمر مكون من حرفين، متبوعاً باسم نطاق افتراضي شهير من 16 حرفاً، ثم رقم منفذ من خانتين).
  * الأمر النموذجي القياسي في صفحة المان لأداة netcat هو: **`nc host.example.com 42`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** انظر إلى قسم EXAMPLES في صفحة الدليل المعروضة.
* **Hint 2:** الأمر يبدأ بـ `nc`، ثم النطاق `host.example.com` ثم المنفذ `42`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. في صفحة المان لأداة `nc`:
   ```text
   EXAMPLES
        Open a TCP connection to port 42 of host.example.com, using port 31337 as the source port:
              $ nc host.example.com 42
   ```
2. الأمر هو: `nc host.example.com 42`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What is the example command?*
  * **Answer:** `nc host.example.com 42`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Manual Extraction:
Utility: nc (Netcat)
Section: EXAMPLES
Extracted Command: nc host.example.com 42
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استخدام الطرفية:**
> *"When flags and options escape your memory, the quickest path to ground truth is typing 'man <tool>' and jumping directly to the EXAMPLES section using '/EXAMPLES'."*
