# Task 06: Shodan.io
## المهمة 06: استكشاف أجهزة وخدمات الإنترنت العالمية عبر محرك Shodan

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 06
* **Original Title:** Shodan.io
* **Questions:**
  1. *According to Shodan.io, what is the first country in the world in terms of the number of publicly accessible Apache servers?*
  2. *Based on Shodan.io, what is the 3rd most common port used for Apache?*
  3. *Based on Shodan.io, what is the most common port used for nginx?*
* **Objective:** Leverage Shodan search queries, analyze global IoT and service statistics, identify top countries hosting specific daemons, and determine alternative listening ports.
* **Core Concepts:** Shodan Search Engine, Banners, Port Frequency, Public Server Statistics, Product Probing (`product:Apache`, `product:nginx`).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل محرك Shodan:**
  على عكس محرك Google الذي يفهرس محتوى صفحات الويب، يفهرس محرك Shodan الترويسات (Banners) للخدمات والأجهزة المتصلة بالإنترنت (خوادم الويب، أجهزة التوجيه، الكاميرات، أنظمة التحكم الصناعي SCADA).
* **تحليل السؤال الأول (الدولة الأولى في عدد خوادم أباتشي العامة):**
  عند البحث عن خادم الويب الشهير Apache عبر استعلام `product:Apache`، يعرض Shodan لوحة إحصائية (Top Countries) تُظهر ترتيب الدول من حيث عدد الخوادم:
  1. الدولة الأولى بلا منازع هي: **الولايات المتحدة (United States)** بحصة تتجاوز ملايين الخوادم.
* **تحليل السؤال الثاني (المنفذ الثالث الأكثر شيوعاً لخوادم أباتشي):**
  في لوحة أهم المنافذ (Top Ports) لنفس الاستعلام:
  1. المنفذ الأول: المنفذ القياسي للـ HTTP غير المشفر: `80`.
  2. المنفذ الثاني: المنفذ القياسي للـ HTTPS المشفر: `443`.
  3. المنفذ الثالث: منفذ الويب البديل الأكثر شهرة في بيئات التطوير والبروكسي: **`8080`**.
* **تحليل السؤال الثالث (المنفذ الأكثر شيوعاً لخوادم nginx):**
  عند البحث عن خادم `product:nginx`، فإن المنفذ الأكثر استخداماً على الإطلاق عالمياً لخدمات الويب القياسية هو المنفذ القياسي للـ HTTP: **`80`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** الدولة صاحبة أكبر بنية تحتية لمراكز البيانات السحابية (AWS, Azure, GCP): `United States`.
* **Hint 2 (للسؤال 2):** منفذ الويب البديل الشهير المكون من أربعة أرقام: `8080`.
* **Hint 3 (للسؤال 3):** رقم المنفذ الافتراضي للـ HTTP غير المشفر: `80`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **استعلام Shodan والفلترة لخادم أباتشي:**
   * الدخول إلى `https://www.shodan.io/`.
   * إدخال الفلتر الدقيق:
     ```text
     product:Apache
     ```
   * قراءة الإحصائيات في القائمة الجانبية (Facet Analysis):
     * **Top Countries:**
       1. **United States (إجابة السؤال 1)**
       2. Germany
       3. China
     * **Top Ports:**
       1. 80 (HTTP)
       2. 443 (HTTPS)
       3. **8080 (HTTP Alternate / Proxy) (إجابة السؤال 2)**
2. **استعلام Shodan لخادم nginx:**
   * إدخال الفلتر:
     ```text
     product:nginx
     ```
   * **Top Ports:**
     1. **80 (HTTP الافتراضي) (إجابة السؤال 3)**
     2. 443 (HTTPS)
     3. 8080

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *According to Shodan.io, what is the first country in the world in terms of the number of publicly accessible Apache servers?*
  * **Answer:** `United States`
* **Question 2:** *Based on Shodan.io, what is the 3rd most common port used for Apache?*
  * **Answer:** `8080`
* **Question 3:** *Based on Shodan.io, what is the most common port used for nginx?*
  * **Answer:** `80`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Shodan Intelligence Query Results:
[+] Query: product:Apache
    - Top Country #1: United States (Verified)
    - Top Port #3: 8080 (Verified)
[+] Query: product:nginx
    - Top Port #1: 80 (Verified)
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استخبارات Shodan:**
> *"Shodan tells you what ports are listening before you ever send a SYN packet; always check Shodan to avoid unnecessary active network alerts."*
> يخبرك محرك Shodan بالمنافذ المفتوحة قبل أن ترسل حزمة SYN واحدة من جهازك؛ لذا ابدأ به لتفادي إطلاق أجهزة الإنذار في شبكة الهدف.
