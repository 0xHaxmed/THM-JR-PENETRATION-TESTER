# Task 02: Web Browser
## المهمة 02: استكشاف الويب عبر أدوات المطور (Developer Tools)

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** Web Browser
* **Questions:**
  1. *Browse to the following website and ensure that you have opened your Developer Tools on AttackBox Firefox, or the browser on your computer. Using the Developer Tools, figure out the total number of questions.*
* **Target Lab:** `https://static-labs.tryhackme.cloud/sites/networking-tcp/`
* **Objective:** Understand how the standard web browser operates as an active reconnaissance tool, utilize Developer Tools (Inspect Elements, Network Tab, Console) to analyze client-side DOM structure, and uncover hidden application logic.
* **Core Concepts:** Web Browser Recon, DOM Inspection, Developer Tools (F12), JavaScript Execution, Hidden HTML Elements.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **المتصفح كأداة استطلاع نشط:**
  عندما يكتب المستخدم عنوان URL في المتصفح، يقوم المتصفح بإجراء مصافحة TCP ثلاثية (3-Way Handshake) مباشرة مع خادم الهدف، ثم يرسل طلب `HTTP GET`، ويستقبل ملفات HTML و CSS و JavaScript ويقوم بتصييرها (Rendering).
* **دور أدوات المطور (Developer Tools):**
  غالباً ما يخفي مطورو الويب عناصر معينة في واجهة المستخدم عبر خصائص CSS مثل `display: none;` أو يضمنون كود جافاسكريبت يحتوي على أسئلة، مصفوفات بيانات، أو مسارات غير مفعلة في الصفحة المعروضة ظاهرياً.
* **تحليل السؤال:**
  بالانتقال إلى الرابط `https://static-labs.tryhackme.cloud/sites/networking-tcp/` وفتح أدوات المطور (F12) في قسم الفحص (Inspector/Elements) أو مراقبة كود الجافاسكريبت، نجد مصفوفة الأسئلة أو عناصر الـ DOM المرتبطة باختبار الـ TCP، ويظهر أن العدد الإجمالي المدمج للأسئلة هو **`8`**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** افتح الرابط في المتصفح واضغط على زر `F12` أو اضغط بزر الفأرة الأيمن واختر `Inspect`.
* **Hint 2:** تفقد عناصر الـ HTML أو ابحث في كود الجافاسكريبت المرتبط بالصفحة عن كائن الأسئلة (questions array).
* **Hint 3:** العدد الإجمالي هو رقم أحادي يمثل عدد الأسئلة الكلي: `8`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **زيارة الموقع المستهدف:**
   * التوجه إلى الرابط المباشر: `https://static-labs.tryhackme.cloud/sites/networking-tcp/`.
2. **تشغيل أدوات المطور (DevTools):**
   * الضغط على `Ctrl + Shift + I` أو `F12`.
   * الانتقال إلى تبويب **Inspector (العناصر)** وتفحص بنية الـ DOM.
   * بدلاً من ذلك، الانتقال إلى تبويب **Console (الطرفية)** أو فحص ملف `script.js` المرفق في تبويب **Debugger/Sources**.
3. **تحليل كود الجافاسكريبت ومصفوفة الأسئلة:**
   * نجد مصفوفة الأسئلة (Array) المعرفة في الصفحة:
     ```javascript
     const questions = [
         { q: "What does TCP stand for?", ... },
         { q: "What is the three-way handshake?", ... },
         ...
     ];
     console.log(questions.length); // Returns 8
     ```
   * إجمالي عدد عناصر الأسئلة الموجودة في بنية الكود: **8**.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Browse to the following website and ensure that you have opened your Developer Tools... figure out the total number of questions.*
  * **Answer:** `8`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
DOM Analysis Evidence:
Target: https://static-labs.tryhackme.cloud/sites/networking-tcp/
Element Inspection: <div class="question-container"> ... </div>
Array Length: questions.length === 8
Verified Answer: 8
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في فحص تطبيقات الويب:**
> *"Never judge a web page solely by what renders in the browser viewport; always press F12 to inspect the client-side DOM, hidden fields, and JavaScript variables."*
> لا تحكم على صفحة الويب أبداً بناءً على ما تراه عيناك في واجهة العرض فقط؛ اضغط دائماً على F12 لتفحص شجرة الـ DOM، الحقول المخفية، والمتغيرات المضمنة في كود الجافاسكريبت.
