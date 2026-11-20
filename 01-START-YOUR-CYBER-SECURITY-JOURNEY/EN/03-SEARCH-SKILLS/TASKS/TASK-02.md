# Task 02: Shodan (TryScanMe)
## المهمة 02: استكشاف البنية التحتية عبر محرك Shodan

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** Shodan (TryScanMe)
* **Questions:**
  1. *What domain is associated with the IP address 185.243.115.47 ?*
* **Objective:** Query the Shodan search engine (or the simulated TryScanMe tool) with a specific IP address to discover associated hostnames and reverse DNS mappings.
* **Core Concept:** Passive network intelligence, reverse DNS resolution via Shodan, IP-to-domain correlation.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال:**
  المطلوب البحث عن عنوان الـ IPv4 التالي: `185.243.115.47` داخل محرك استكشاف الأجهزة Shodan أو الأداة المعملية التفاعلية `TryScanMe`، واستخراج اسم النطاق (Domain Name) المرتبط به.
  * يوضح نمط الإجابة المقبول: `_________.___` (نطاق من 9 أحرف متبوعاً بنقطة ثم 3 أحرف).
  * هذا النمط يتطابق مع النطاق الرسمي التابع لبيئة المعامل: **`tryscanme.com`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** ابحث عن عنوان IP في شريط بحث الأداة أو Shodan: `185.243.115.47`.
* **Hint 2:** انظر إلى حقل `Hostnames` أو `Domains` في تقرير العنوان.
* **Hint 3:** النطاق يبدأ بـ `try` وينتهي بـ `.com` ومكون من 9 أحرف قبل النقطة: `tryscanme.com`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. في واجهة الأداة المعملية المدمجة:
   * إدخال العنوان: `185.243.115.47`.
   * الضغط على زر الاستعلام **Lookup**.
2. فحص تقرير الاستطلاع:
   ```text
   IP Address: 185.243.115.47
   Hostnames: tryscanme.com
   Operating System: Linux
   Open Ports: 80, 443
   ```
3. النطاق المكتشف والمرتبط بالعنوان هو: `tryscanme.com`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What domain is associated with the IP address 185.243.115.47 ?*
  * **Answer:** `tryscanme.com`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Shodan IP Lookup:
IP: 185.243.115.47
Associated Hostname: tryscanme.com
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استخبارات Shodan:**
> *"Never rely solely on DNS forward lookups; reverse IP correlation in Shodan frequently reveals hidden subdomains, staging domains, and legacy client hostnames."*
