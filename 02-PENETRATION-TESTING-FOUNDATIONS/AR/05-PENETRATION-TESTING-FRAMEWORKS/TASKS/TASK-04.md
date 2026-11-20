# Task 04: NIST SP 800-115
## المهمة 04: دليل نيست الفني لاختبار وتقييم أمن المعلومات (NIST SP 800-115)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 04
* **Original Title:** NIST SP 800-115
* **Questions:**
  1. *During the Execution phase, your vulnerability scanner flags 15 potential issues on a web application. Before attempting exploitation, which NIST SP 800-115 technique category should you apply to these findings?*
* **Objective:** Understand the National Institute of Standards and Technology (NIST) Special Publication 800-115, its testing methodologies (Review, Inspect, Test), and execution phase workflows.
* **Core Concepts:** NIST SP 800-115, Four-Phase Methodology (Planning, Discovery, Execution, Post-Execution), Target Vulnerability Validation.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال:**
  أثناء مرحلة التنفيذ (Execution phase)، أظهر فاحص الثغرات الآلي (Vulnerability Scanner) وجود 15 ثغرة محتملة في التطبيق. وقبل محاولة استغلالها فعلياً، ما هي فئة التقنيات وفق معيار NIST SP 800-115 التي يجب تطبيقها أولاً للتحقق من أن هذه النتائج حقيقية وليست إنذارات كاذبة (False Positives)؟
  * تسمى هذه العملية وفق وثيقة نيست: **التحقق من صحة ثغرات الهدف (Target vulnerability validation)**.
  * يمنع هذا الإجراء الممارس من إطلاق استغلالات عشوائية قد تسقط الخادم استناداً إلى تقارير آلية غير دقيقة.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** العبارة مكونة من ثلاث كلمات بالإنجليزية: Target تليها vulnerability تليها validation: `Target vulnerability validation`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **المراحل الأربع لاختبار الاختراق في NIST SP 800-115:**
   * **1. Planning (التخطيط):** تحديد الموارد، النطاق، والقواعد.
   * **2. Discovery (الاستكشاف):** مسح الشبكة، تحديد المنافذ وتحديد الثغرات المحتملة.
   * **3. Execution (التنفيذ):**
     * الخطوة الأولى: **Target vulnerability validation (إجابة السؤال)** للتحقق اليدوي من وجود الثغرة الفعلي قبل الهجوم.
     * الخطوة الثانية: استغلال الثغرات المؤكدة فقط لاختبار عمق الاختراق.
   * **4. Post-Execution (ما بعد التنفيذ):** كتابة التقرير، تنظيف الآثار، وعقد جلسة إحاطة للعميل.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *During the Execution phase, your vulnerability scanner flags 15 potential issues on a web application. Before attempting exploitation, which NIST SP 800-115 technique category should you apply to these findings?*
  * **Answer:** `Target vulnerability validation`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
NIST SP 800-115 Execution Phase Hierarchy:
Discovery Phase Outputs ⟶ Target Vulnerability Validation (Filtering False Positives) ⟶ Exploit Execution
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في معايير NIST:**
> *"Never trust a raw vulnerability scan; validate the finding manually before attempting exploitation."*
> لا تثق أبداً في نتائج الماسح الآلي الخام؛ بل تحقق يدوياً من وجود الثغرة أولاً قبل الشروع في استغلالها.
