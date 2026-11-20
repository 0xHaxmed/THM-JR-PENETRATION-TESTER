# Task 02: Something Looks Wrong...
## المهمة 02: تحليل السجلات وتحديد عنوان IP المهاجم

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** Something Looks Wrong...
* **Questions:**
  1. *What IP address is the attack coming from?*
* **Objective:** Inspect web application traffic logs, identify anomalous high-frequency requests, and isolate the adversary's IP address.
* **Core Concept:** Web log analysis, rate anomaly detection, source IP attribution.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل سجلات الخادم:**
  عند تفحص شاشة المراقبة في المعمل، نلاحظ تدفق طلبات HTTP متتالية بمعدل سريع جداً (Brute Force / Fuzzing Traffic).
  * الطلبات العادية للمستخدمين تأتي بفاصل ثوانٍ وموجهة لصفحات مثل `/`, `/index.html`.
  * نجد عنوان IP واحداً يرسل عشرات الطلبات في الثانية باحثاً عن مسارات غير موجودة (`/test`, `/login`, `/admin`, `/secret`).
  * عنوان IP الخاص بهذا المصدر المعادي هو: **`32.122.195.63`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** انظر إلى عمود `Source IP` في جدول السجلات.
* **Hint 2:** العنوان يبدأ بالرقم 32 ومكون من 4 خانات مفصولة بنقاط (IPv4).

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. في شاشة مراقبة السجلات:
   ```text
   TIME     SOURCE IP        METHOD  URI                   STATUS
   12:00:01 32.122.195.63    GET     /test                 404
   12:00:01 32.122.195.63    GET     /api                  404
   12:00:02 32.122.195.63    GET     /dashboard            404
   12:00:02 32.122.195.63    GET     /admin                200
   ```
2. نلاحظ أن كافة الطلبات المشبوهة المتكررة تنبع من المصدر: `32.122.195.63`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What IP address is the attack coming from?*
  * **Answer:** `32.122.195.63`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Identified Malicious Ingress IP: 32.122.195.63
Traffic Signature: Rapid HTTP enumeration (Directory Fuzzing)
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في تحليل السجلات:**
> *"An attacker running automated tools generates an unmistakable footprint: high request volume, sequential timestamps, and a barrage of 404 HTTP status codes from a single origin IP."*
