# Task 03: OWASP WSTG
## المهمة 03: دليل اختبار أمان تطبيقات الويب (OWASP WSTG)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 03
* **Original Title:** OWASP WSTG
* **Questions:**
  1. *The WSTG organizes its test cases using a specific identifier format. What identifier would you look for to find test cases related to input validation?*
  2. *A development team has just finished coding a new feature and wants to check it against WSTG before deployment. Which SDLC-aligned phase number are they in?*
* **Objective:** Master the OWASP Web Security Testing Guide (WSTG v4.2), understand its category identifiers, and map testing activities to the Software Development Life Cycle (SDLC).
* **Core Concepts:** OWASP WSTG, Test Case Identifiers (WSTG-INPV), SDLC Testing Alignment, Secure Coding Integration.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول (معرّف فحص التحقق من المدخلات):**
  يقسم دليل WSTG حالات الاختبار (Test Cases) إلى فئات رئيسية مسبوقة بـ `WSTG-`. ما هو المعرّف المخصص لفئة التحقق من المدخلات (Input Validation Testing - مثل SQLi, XSS, Command Injection)؟
  * هذا المعرّف هو **WSTG-INPV** (اختصار لـ Input Validation).
* **تحليل السؤال الثاني (المرحلة المقابلة في دورة حياة تطوير البرمجيات SDLC):**
  في نموذج مواءمة WSTG مع دورة حياة البرمجيات:
  * المرحلة 1: ما قبل التطوير (Pre-Development / Definition & Design).
  * المرحلة 2: أثناء التطوير وتوليد الكود (During Development / Code review).
  * المرحلة 3: ما بعد التطوير وقبل النشر الفعلي (After Development / System Testing & Deployment verification).
  * عندما ينتهي فريق التطوير من كتابة كود الميزة ويريد اختبارها قبل النشر الحي، فإنهم يقعون في المرحلة رقم **3**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** المعرّف يتكون من WSTG متبوعة بشرطة ثم أربعة أحرف ترمز لـ Input Validation: `WSTG-INPV`.
* **Hint 2 (للسؤال 2):** رقم المرحلة المقابلة للاختبار قبل النشر هو رقم أحادي: `3`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **الفئات الرئيسية لدليل OWASP WSTG:**
   * `WSTG-INFO`: Information Gathering (جمع المعلومات).
   * `WSTG-CONF`: Configuration and Deployment Management.
   * `WSTG-IDNT`: Identity Management.
   * `WSTG-ATHN`: Authentication Testing.
   * `WSTG-ATHZ`: Authorization Testing.
   * `WSTG-SESS`: Session Management.
   * **`WSTG-INPV` (إجابة السؤال 1):** **Input Validation Testing** (فحص مدخلات الويب ضد ثغرات الحقن).
   * `WSTG-ERRH`: Error Handling.
   * `WSTG-CRYP`: Cryptography.
   * `WSTG-BUSL`: Business Logic Testing.
   * `WSTG-CLNT`: Client-side Testing.

2. **التكامل مع دورة حياة التطوير (SDLC Integration):**
   * المرحلة **3 (إجابة السؤال 2)** تمثل مرحلة الاختبار الأمني الفعلي للمنتج المكتمل قبل اعتماده في بيئة الإنتاج الحية.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *The WSTG organizes its test cases using a specific identifier format. What identifier would you look for to find test cases related to input validation?*
  * **Answer:** `WSTG-INPV`
* **Question 2:** *A development team has just finished coding a new feature and wants to check it against WSTG before deployment. Which SDLC-aligned phase number are they in?*
  * **Answer:** `3`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
OWASP WSTG Structure:
[+] Identifier Format: WSTG-[CATEGORY]-[NUMBER] (e.g., WSTG-INPV-01: Testing for SQL Injection)
[+] SDLC Phase Alignment: Phase 3 = Post-Development / Pre-Deployment Functional Verification
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في اختبار الويب بـ WSTG:**
> *"WSTG is the gold standard for web applications; using its standardized identifiers ensures your findings map directly to recognized developer mitigations."*
> دليل WSTG هو المعيار الذهبي لاختبار تطبيقات الويب؛ واستخدام معرّفاته الرسمية يضمن أن المطورين يفهمون أصل الثغرة وطريقة إصلاحها بدقة.
