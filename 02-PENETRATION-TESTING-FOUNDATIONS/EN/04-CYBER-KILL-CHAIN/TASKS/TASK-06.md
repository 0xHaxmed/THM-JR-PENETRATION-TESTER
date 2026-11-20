# Task 06: Installation
## المهمة 06: مرحلة التثبيت والحفاظ على البقاء (Persistence)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 06
* **Original Title:** Installation
* **Questions:**
  1. *Script giving backdoor remote access on web server*
  2. *Security approach allowing only approved programs*
* **Objective:** Understand Phase 5 of the Cyber Kill Chain (Installation), master web shells and persistence mechanisms, and study application allowlisting defenses.
* **Core Concepts:** Web shells, Backdoors, Persistence techniques, Application Allowlisting / AppLocker.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول:**
  ما هو السكريبت البرمجي (المكتوب بلغات مثل PHP, ASPX, JSP) الذي يزرعه المهاجم على خادم الويب ليمنحه وصولاً خلفياً مستمراً (Backdoor Remote Access) وتنفيذ الأوامر عبر المتصفح؟
  * هذا هو الـ **`web shell`**.
* **تحليل السؤال الثاني:**
  ما هو النهج الأمني الدفاعي الذي يقوم على حظر تشغيل كافة البرامج والملفات التنفيذية افتراضياً، والسماح فقط للبرامج المصرح بها والموقعة رقمياً (Approved Programs) بالعمل؟
  * هذا هو نهج القوائم البيضاء: **`allowlisting`** (Application Whitelisting).

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** الكلمة تتكون من كلمتين مفصولتين بمسافة: `web shell`.
* **Hint 2 (للسؤال 2):** الكلمة تبدأ بـ allow: `allowlisting`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **أهمية مرحلة التثبيت (Installation):**
   * بعد الاستغلال، يسعى المهاجم لضمان عدم فقدان الوصول عند إعادة تشغيل الجهاز أو انقطاع الاتصال (Persistence).
   * على خوادم الويب، يتم رفع `web shell`.
2. **الاستراتيجية الدفاعية الفعالة:**
   * قوائم السماح `allowlisting` تمنع تنفيذ الـ Web Shell أو البرمجيات الخبيثة حتى لو تم رفعها إلى القرص.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Script giving backdoor remote access on web server*
  * **Answer:** `web shell`
* **Question 2:** *Security approach allowing only approved programs*
  * **Answer:** `allowlisting`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Phase 5 Installation Findings:
[+] Malicious Implant: web shell
[+] Mitigation Control: allowlisting
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في مرحلة التثبيت:**
> *"Exploitation is temporary; installation of web shells and registry persistence turns a momentary intrusion into a prolonged breach."*
