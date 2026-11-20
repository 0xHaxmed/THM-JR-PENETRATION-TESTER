# Task 02: OSSTMM
## المهمة 02: دليل منهجية اختبار أمان المصادر المفتوحة (OSSTMM)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** OSSTMM
* **Questions:**
  1. *What OSSTMM metric quantifies the balance between an organization's attack surface and its controls?*
  2. *During an OSSTMM assessment, you complete Induction and Interaction, identifying 6 hosts running unpatched services. Which phase do you enter next, and what is the primary objective?*
* **Objective:** Understand the Open Source Security Testing Methodology Manual (OSSTMM), its quantitative metric approach ("Facts over Opinions"), its five testing channels, and its operational phases.
* **Core Concepts:** OSSTMM (ISECOM, Pete Herzog), Risk Assessment Values (RAV), Channels (Human, Physical, Wireless, Telecommunications, Data Networks), Assessment Phases.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول (المقياس الكمي في OSSTMM):**
  ما هو المقياس الرياضي الذي ابتكره دليل OSSTMM ليقيس بشكل كمي التوازن بين مساحة الهجوم للمؤسسة (Attack Surface) وبين الضوابط والحمايات الأمنية المطبقة؟
  * هذا المقياس هو **قيم تقييم المخاطر (Risk Assessment Values)** وتختصر بـ **RAVs**. يعتمد OSSTMM على مبدأ "الحقائق والأرقام بدلاً من الآراء"، حيث يحسب درجة أمان رياضية محددة.
* **تحليل السؤال الثاني (مراحل الاختبار في OSSTMM):**
  تمر مراحل الاختبار العملي في OSSTMM بالترتيب التالي:
  1. *Induction (الاستقراء الأولي)*
  2. *Interaction (التفاعل وتحديد الأصول)*
  3. *Inquiry (الاستعلام والتدقيق والتحقيق في الأصول المكتشفة)*
  * بعد تحديد الأجهزة الستة المشغلة لخدمات غير مرقعة، ندخل مرحلة **Inquiry** للتحقيق العميق واستجواب تلك الخدمات.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** ثلاث كلمات بالإنجليزية: Risk تليها Assessment تليها Values: `Risk Assessment Values`.
* **Hint 2 (للسؤال 2):** كلمة واحدة تبدأ بحرف I وتعني الاستعلام والتحقيق: `Inquiry`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **معمارية إطار OSSTMM:**
   * تم تطويره بواسطة معهد ISECOM بقيادة Pete Herzog.
   * **القنوات الخمس (The 5 Channels):**
     1. Human Security (أمان العنصر البشري / الهندسة الاجتماعية).
     2. Physical Security (الأمان المادي والوصول للمباني).
     3. Wireless Communications (الاتصالات اللاسلكية: Wi-Fi, Bluetooth, RFID).
     4. Telecommunications (الاتصالات الهاتفية وخطوط الفاكس و VoIP).
     5. Data Networks (شبكات البيانات والإنترنت والأنظمة).
   * **المقياس الرياضي:** **Risk Assessment Values (RAVs)** لقياس الأمان المتبقي.
   * **مرحلة الاستعلام:** تلي مرحلة التفاعل وتسمى **Inquiry**.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *What OSSTMM metric quantifies the balance between an organization's attack surface and its controls?*
  * **Answer:** `Risk Assessment Values`
* **Question 2:** *During an OSSTMM assessment, you complete Induction and Interaction, identifying 6 hosts running unpatched services. Which phase do you enter next, and what is the primary objective?*
  * **Answer:** `Inquiry`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
OSSTMM Metrics:
[+] Quantitative Balance Metric: Risk Assessment Values (RAVs)
[+] Phase Progression: Induction ⟶ Interaction ⟶ Inquiry ⟶ Intervention
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في منهجية OSSTMM:**
> *"Security is a state of operational balance, not an absolute product; measure controls against exposure quantitatively."*
> الأمان هو حالة من التوازن التشغيلي وليس منتجاً تشتري؛ وقيمة OSSTMM تكمن في قياس الضوابط رياضياً بالأرقام مقابل مساحة التعرض.
