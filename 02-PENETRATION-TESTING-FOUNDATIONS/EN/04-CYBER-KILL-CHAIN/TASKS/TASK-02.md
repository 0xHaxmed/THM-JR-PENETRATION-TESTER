# Task 02: Reconnaissance
## المهمة 02: مرحلة الاستطلاع واستخبارات المصادر المفتوحة

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** Reconnaissance
* **Questions:**
  1. *What is the name of the Intel Gathering Tool that is a web-based interface to the common tools and resources for open-source intelligence?*
  2. *What is the definition for the email gathering process during the stage of reconnaissance?*
* **Objective:** Master Phase 1 of the Cyber Kill Chain, contrast passive and active reconnaissance techniques, utilize OSINT repositories, and understand email intelligence gathering.
* **Core Concepts:** Passive vs. Active Reconnaissance, OSINT Framework, Email Harvesting, Public Footprinting.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال الأول (أداة جمع الاستخبارات عبر الويب):**
  ما هو اسم المنصة التفاعلية الشهيرة القائمة على الويب التي تنظم وتجمع أدوات ومصادر استخبارات المصادر المفتوحة (OSINT) في شجرة تصنيفية تفاعلية؟
  * هذا هو **OSINT Framework** (`osintframework.com`).
* **تحليل السؤال الثاني (جمع البريد الإلكتروني):**
  ما هو المصطلح التقني المستخدم لوصف عملية جمع وحصد عناوين البريد الإلكتروني التابعة للمؤسسة المستهدفة من المواقع ومحركات البحث وقواعد البيانات العامة؟
  * تسمى العملية **حصد البريد الإلكتروني (email harvesting)**. تستخدم هذه العناوين لاحقاً كأهداف لحملات الهندسة الاجتماعية والتصيد الموجه (Spear Phishing).

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** اسم المنصة يتكون من كلمتين: OSINT تليها كلمة Framework: `OSINT Framework`.
* **Hint 2 (للسؤال 2):** مصطلح من كلمتين باللغة الإنجليزية: email تليها كلمة harvesting: `email harvesting`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **استعراض تقنيات مرحلة الاستطلاع (Reconnaissance Breakdown):**
   * **الاستطلاع السلبي (Passive Recon):** جمع المعلومات دون إرسال أي حزم مباشرة لأجهزة الهدف (استعلام سجلات WHOIS، البحث في LinkedIn، تصفح أرشيف Wayback Machine، استخدام محرك Shodan).
   * **الاستطلاع النشط (Active Recon):** إرسال طلبات وحزم مباشرة تفحص الخوادم (فحص المنافذ عبر Nmap، فحص مسارات الويب).
   * **أداة OSINT Framework:** شجرة تفرعية تصنف أدوات البحث حسب نوع البيانات (Usernames, Email Addresses, Domain Names, IP Addresses).
   * **عملية Email Harvesting:** استخدام أدوات مثل `theHarvester` أو `Hunter.io` للبحث في محركات البحث وLinkedIn عن موظفي الشركة لشن حملات التصيد.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *What is the name of the Intel Gathering Tool that is a web-based interface to the common tools and resources for open-source intelligence?*
  * **Answer:** `OSINT Framework`
* **Question 2:** *What is the definition for the email gathering process during the stage of reconnaissance?*
  * **Answer:** `email harvesting`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Tool Mapping:
[+] Web Interface for OSINT Resources: OSINT Framework (https://osintframework.com)
[+] Email Intelligence Process: email harvesting (Targeting employee lists for Spear Phishing)
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في مرحلة الاستطلاع:**
> *"The more time an attacker spends in passive reconnaissance, the higher the probability of delivery success."*
> كلما قضى المهاجم وقتاً أطول في جمع المعلومات السلبية بدقة، ارتفعت فرص نجاح الهجمات في المراحل اللاحقة.
