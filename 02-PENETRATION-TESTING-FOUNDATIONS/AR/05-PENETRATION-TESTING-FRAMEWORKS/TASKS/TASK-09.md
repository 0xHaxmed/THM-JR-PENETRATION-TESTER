# Task 09: Choosing the Right Framework
## المهمة 09: هندسة اختيار ومواءمة الأطر الأمنية للمشاريع المتكاملة

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 09
* **Original Title:** Choosing the Right Framework
* **Questions:**
  1. *A U.S. federal agency needs a security assessment of its internal network. The results must align with federal guidelines, and the assessment should include both document reviews and active penetration testing. Which framework is the most natural primary choice?*
  2. *Your client is an e-commerce company with a web storefront, a mobile shopping app, and a payment processing system. Which combination of frameworks would you recommend to cover all three components? (comma-separated).*
* **Objective:** Select and integrate multiple penetration testing frameworks based on client industry, compliance mandates, target architectures, and multi-platform scopes.
* **Core Concepts:** Framework Synthesis, Multi-Tier Scoping, Compliance Alignment (NIST vs. PCI DSS vs. OWASP).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول (تقييم وكالة فيدرالية أمريكية):**
  عندما تكون المؤسسة المستهدفة وكالة حكومية فيدرالية أمريكية تطلب مراجعة المستندات واختبار الاختراق الفعلي وفق اللوائح الفيدرالية الرسمية، ما هو الإطار الصادر عن المعهد الوطني للمعايير والتقنية (NIST) المصمم خصيصاً لهذا الغرض؟
  * هو: **NIST SP 800–115** (أو `NIST SP 800-115`).
* **تحليل السؤال الثاني (شركة تجارة إلكترونية متعددة المنصات):**
  العميل يمتلك منظومة ثلاثية تتكون من:
  1. موقع ويب تجاري (Web Storefront) ⟶ يغطيه: **WSTG** (Web Security Testing Guide).
  2. تطبيق تسوق للهواتف الذكية (Mobile App) ⟶ يغطيه: **MASTG** (Mobile Application Security Testing Guide).
  3. نظام معالجة مدفوعات وبطاقات ائتمانية (Payment Processing) ⟶ يغطيه: **PCI DSS** (أو PCI DSS Guidelines).
  * الدمج المعياري الموصى به والمفصول بفواصل: **WSTG,MASTG,PCI DSS**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** المعيار الفيدرالي لنيست: `NIST SP 800–115`.
* **Hint 2 (للسؤال 2):** ثلاثة أطر مفصولة بفواصل دون مسافات زائدة: `WSTG,MASTG,PCI DSS`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **مصفوفة اتخاذ القرار لاختيار الإطار الأمني (Decision Matrix):**
   * **القطاع الفيدرالي والحكومي (US Federal / FISMA):** ⟶ **NIST SP 800-115 (إجابة السؤال 1)**.
   * **الأنظمة البيئية الهجينة (Hybrid Multi-Tier Environments):**
     * نادراً ما يكفي إطار واحد في المشاريع الكبرى؛ فالمشاريع المعاصرة تتطلب الجمع بين:
       * **WSTG** لحماية واجهة الويب والـ API.
       * **MASTG** لحماية تطبيقات الأجهزة المحمولة.
       * **PCI DSS** لضمان عزل وتشفير بيانات البطاقات.
     * الصيغة المدمجة المطلوبة: **WSTG,MASTG,PCI DSS (إجابة السؤال 2)**.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *A U.S. federal agency needs a security assessment of its internal network. The results must align with federal guidelines, and the assessment should include both document reviews and active penetration testing. Which framework is the most natural primary choice?*
  * **Answer:** `NIST SP 800–115`
* **Question 2:** *Your client is an e-commerce company with a web storefront, a mobile shopping app, and a payment processing system. Which combination of frameworks would you recommend to cover all three components? (comma-separated).*
  * **Answer:** `WSTG,MASTG,PCI DSS`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Framework Selection Matrix:
[+] US Federal Audit Mandate ⟶ NIST SP 800-115
[+] Multi-Platform Scope:
    - Web Application Tier     ⟶ WSTG
    - Mobile Application Tier  ⟶ MASTG
    - Payment Card Segment     ⟶ PCI DSS
    Combined String: WSTG,MASTG,PCI DSS
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في اختيار الأطر:**
> *"The most mature penetration testing engagements do not choose one framework; they orchestrate a symphony of frameworks tailored to the target architecture."*
> أكثر مشاريع اختبار الاختراق نضجاً واحترافية لا تقتصر على إطار واحد، بل تعزف سيمفونية متكاملة من الأطر المتخصصة والمفصلة على معمارية الهدف.
