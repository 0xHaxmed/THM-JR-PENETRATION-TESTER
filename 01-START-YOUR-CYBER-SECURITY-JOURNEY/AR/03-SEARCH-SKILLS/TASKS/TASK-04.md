# Task 04: Vulnerability Databases (CVE)
## المهمة 04: تقييم خطورة الثغرات عبر قواعد بيانات CVE و CVSS

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 04
* **Original Title:** Vulnerability Databases (CVE)
* **Questions:**
  1. *What CVSS (Common Vulnerability Scoring System) classification did the vulnerability get?*
* **Objective:** Query vulnerability databases (NVD / MITRE CVE), understand CVSS metrics, and identify vulnerability severity ratings.
* **Core Concept:** CVE identification, CVSS v3.1 qualitative severity rating, vulnerability triage.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل تصنيف CVSS:**
  نظام تسجيل وتقييم الثغرات المشتركة (**Common Vulnerability Scoring System**) يقسم خطورة الثغرات إلى درجات رقمية (0.0 إلى 10.0) ويقابلها تصنيف نوعي معتمد (Qualitative Rating):
  * **None:** 0.0
  * **Low:** 0.1 – 3.9
  * **Medium:** 4.0 – 6.9
  * **High:** 7.0 – 8.9
  * **Critical:** 9.0 – 10.0
* **تحليل السؤال:** عند فحص الثغرة المعروضة في المعمل، نجد أن درجتها تقع في أعلى مقياس الخطورة لتصنف كـ ثغرة حرجة (**`Critical`** أو **`CRITICAL`**).

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** انظر إلى كلمة تصنيف الخطورة المقابلة لدرجة الـ CVSS المرتفعة.
* **Hint 2:** الكلمة باللغة الإنجليزية وتعني "حرج": `Critical`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. في واجهة البحث عن الثغرات:
   * فحص تفاصيل معرف الثغرة المستعلم عنها.
   * قراءة بطاقة تقييم الـ CVSS:
     ```text
     CVSS v3 Base Score: 9.8
     Severity: CRITICAL
     Vector: CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
     ```
2. التصنيف المعتمد للثغرة هو: `Critical`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What CVSS (Common Vulnerability Scoring System) classification did the vulnerability get?*
  * **Answer:** `Critical`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Vulnerability Metric Assessment:
[+] Score: 9.8 / 10.0
[+] Metric Standard: CVSS v3.1
[+] Qualitative Classification: Critical
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في تقييم الثغرات:**
> *"CVSS scores provide an objective baseline; a score above 9.0 (Critical) indicates a vulnerability exploitable remotely without privileges or user interaction."*
