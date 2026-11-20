# Task 03: What are Hackers Looking For?
## المهمة 03: تتبع أهداف المهاجم وكشف المسار المخترق

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 03
* **Original Title:** What are Hackers Looking For?
* **Questions:**
  1. *What is the latest URL the hacker found?*
* **Objective:** Track the adversary's reconnaissance progression in the web server logs and determine the sensitive administrative URI exposed.
* **Core Concept:** Target enumeration tracking, HTTP 200 vs 404 analysis, critical asset exposure.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل استراتيجية المهاجم:**
  بعد مئات محاولات التخمين التي أسفرت عن رمز الخطأ `404 Not Found`، نجح المهاجم أخيراً في الوصول إلى صفحة إدارية حساسة رد الخادم عليها بنجاح.
  * يظهر في السجل أن آخر طلب ناجح كان موجهاً إلى المسار: **`https://fakebank.thm/admin`**.
  * هذا المسار يمثل لوحة الإدارة الحساسة التي يسعى المخترق لاستغلالها لتعديل البيانات وسحب الأموال.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** ابحث في السجلات عن آخر سطر لعنوان IP المهاجم رد عليه الخادم بكود `200 OK`.
* **Hint 2:** الرابط يبدأ ببروتوكول `https://` وينتهي بـ `/admin`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. متابعة السجلات المحدثة في المعمل التفاعلي:
   ```text
   Log Line: [ALERT] 32.122.195.63 requested https://fakebank.thm/admin [HTTP 200 OK]
   ```
2. الرابط الكامل المكتشف هو: `https://fakebank.thm/admin`.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *What is the latest URL the hacker found?*
  * **Answer:** `https://fakebank.thm/admin`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Target Exposed Asset:
Protocol: HTTPS
Host: fakebank.thm
URI: /admin
Full Verified Answer: https://fakebank.thm/admin
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في حماية اللوحات الإدارية:**
> *"Never expose administrative consoles directly to the public Internet without multi-factor authentication, IP whitelisting, or VPN termination."*
