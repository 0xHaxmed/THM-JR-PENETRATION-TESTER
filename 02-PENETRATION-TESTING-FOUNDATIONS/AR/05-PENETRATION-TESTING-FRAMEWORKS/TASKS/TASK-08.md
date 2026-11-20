# Task 08: Other Notable Frameworks
## المهمة 08: الأطر المتخصصة لبطاقات الدفع، الهواتف الذكية، والحوسبة السحابية

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 08
* **Original Title:** Other Notable Frameworks
* **Questions:**
  1. *Your client is a European online retailer that processes credit card payments through their website. Which framework from this task would most directly govern the penetration testing requirements for this engagement?*
  2. *A client asks you to assess the security of their iOS banking application, including how it stores credentials locally and communicates with backend APIs. Which framework from this task is the most appropriate testing reference?*
  3. *Your team is evaluating a client's AWS infrastructure to determine whether their cloud configurations meet industry security standards. They are not asking for a penetration test but rather a controls assessment. Which framework is most relevant?*
* **Objective:** Map industry-specific security frameworks to distinct client engagement scenarios (Payment Cards, Mobile Applications, Cloud Governance).
* **Core Concepts:** PCI DSS Penetration Testing Guidelines, OWASP MASTG (Mobile), CSA Cloud Controls Matrix (CCM).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول (معالجة مدفوعات البطاقات الائتمانية):**
  عندما يكون العميل متجراً إلكترونياً يعالج بطاقات ائتمان العملاء، ما هو الإطار الإلزامي دولياً الذي يحكم متطلبات اختبار الاختراق وبيئة بيانات حاملي البطاقات (CDE)؟
  * هو إطار: **PCI DSS Penetration Testing Guidelines** (المعيار الصادر عن PCI SSC).
* **تحليل السؤال الثاني (اختبار تطبيقات الهواتف الذكية iOS/Android):**
  عند تقييم تطبيق مصرفي على هواتف iPhone (iOS) لفحص تخزين البيانات في Keychain واتصالات واجهات برمجة التطبيقات (APIs)، ما هو الدليل المعياري المتخصص من OWASP؟
  * هو: **OWASP Mobile Application Security Testing Guide** (ويختصر بـ **MASTG**، وكان يسمى سابقاً MSTG).
* **تحليل السؤال الثالث (تدقيق ضوابط الحوسبة السحابية AWS دون اختراق):**
  عندما يطلب العميل تقييم ضوابط وتكوينات بيئة الحوسبة السحابية AWS (Controls Assessment) وفق معايير الحوسبة السحابية المعتمدة، ما هو الإطار الصادر عن Cloud Security Alliance (CSA)؟
  * هو: **CSA Cloud Control Matrix** (أو **CCM**).

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** الاسم الرسمي للخطوط الإرشادية لبطاقات الدفع: `PCI DSS Penetration Testing Guidelines`.
* **Hint 2 (للسؤال 2):** الاسم الكامل لدليل OWASP لاختبار أمان تطبيقات الهواتف: `OWASP Mobile Application Security Testing Guide`.
* **Hint 3 (للسؤال 3):** مصفوفة ضوابط السحابة الصادرة عن تحالف أمان السحابة: `CSA Cloud Control Matrix`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **خريطة الأطر التخصصية (Domain-Specific Frameworks):**
   * **بيئات بطاقات الائتمان والدفع الإلكتروني:**
     * الإطار: **PCI DSS Penetration Testing Guidelines (إجابة السؤال 1)**
     * النطاق: فحص بيئة Cardholder Data Environment (CDE) وتقسيم الشبكة وعزلها.
   * **تطبيقات الهواتف الذكية (iOS & Android):**
     * الإطار: **OWASP Mobile Application Security Testing Guide (إجابة السؤال 2)**
     * النطاق: التحليل الثابت والديناميكي، فحص التخزين المحلي، الحماية من الهندسة العكسية، وتشفير الاتصالات.
   * **الحوسبة السحابية والحوكمة (Cloud Infrastructure):**
     * الإطار: **CSA Cloud Control Matrix (إجابة السؤال 3)**
     * النطاق: تدقيق ضوابط الأمان عبر 17 مجالاً في بيئات IaaS, PaaS, SaaS.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *Your client is a European online retailer that processes credit card payments through their website. Which framework from this task would most directly govern the penetration testing requirements for this engagement?*
  * **Answer:** `PCI DSS Penetration Testing Guidelines`
* **Question 2:** *A client asks you to assess the security of their iOS banking application, including how it stores credentials locally and communicates with backend APIs. Which framework from this task is the most appropriate testing reference?*
  * **Answer:** `OWASP Mobile Application Security Testing Guide`
* **Question 3:** *Your team is evaluating a client's AWS infrastructure to determine whether their cloud configurations meet industry security standards. They are not asking for a penetration test but rather a controls assessment. Which framework is most relevant?*
  * **Answer:** `CSA Cloud Control Matrix`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Engagement Scenario Matching:
[+] Payment Processing (E-Commerce)   ⟶ PCI DSS Penetration Testing Guidelines
[+] iOS Banking Application           ⟶ OWASP Mobile Application Security Testing Guide (MASTG)
[+] AWS Cloud Configuration Controls   ⟶ CSA Cloud Control Matrix (CCM)
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في اختيار الإطار التخصصي:**
> *"General frameworks define the engagement lifecycle; domain-specific frameworks define the technical checklist."*
> الأطر العامة (مثل PTES) تحدد دورة حياة المشروع، بينما الأطر المتخصصة (مثل MASTG و PCI DSS) تحدد نقاط التحقق التقنية الدقيقة لكل بيئة.
