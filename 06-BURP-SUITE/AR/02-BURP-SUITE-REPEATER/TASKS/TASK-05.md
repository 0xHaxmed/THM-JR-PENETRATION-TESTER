# Task 05: Inspector (لوحة المفتش وتفكيك مكونات الطلب)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **Inspector** panel in Burp Suite is an advanced companion tool integrated into the right-hand side of both the Request and Response views. It bridges the gap between raw text manipulation and graphical parameter control.

When analyzing or crafting HTTP requests, the Inspector categorizes the payload into dedicated, collapsible subsections:
1. **Request Attributes:**
   - Controls fundamental HTTP message parameters, including the HTTP Method (`GET`, `POST`, `PUT`, `DELETE`), URL Protocol, Host, Port, and Path.
2. **Query Parameters:**
   - Parses parameters appended to the URL query string (e.g., `?search=admin&page=2`).
3. **Body Parameters:**
   - Displays parameters embedded within the HTTP request body. This section is specifically designed for and populated by **POST** requests (and variants like `PUT` or `PATCH`) carrying form-encoded payloads (`application/x-www-form-urlencoded`), multipart data, or structured content.
4. **Request Headers:**
   - Parses all HTTP request headers (`User-Agent`, `Accept`, `Authorization`, custom headers) into editable key-value pairs.
5. **Request Cookies:**
   - Extracts all session cookies and tracking tokens sent via the `Cookie` header into individual editable components.

In addition to editing values, the Inspector offers built-in decoding and encoding widgets, allowing you to instantly view and convert values between URL encoding, HTML entity encoding, Base64, and Hex without navigating to the Decoder tab.

#### الأسئلة المطروحة (Questions):
1. Which section in the Inspector is specific to POST requests? (أي قسم في لوحة Inspector مخصص للطلبات من نوع POST؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الفروق الجوهرية بين Query Parameters و Body Parameters:**
  * طلبات `GET` ترسل بياناتها عبر شريط العنوان في مصفوفة الاستعلام (Query Parameters)، مما يجعلها عرضة للحفظ في سجلات الخادم وسجلات البروكسي وتاريخ المتصفح.
  * طلبات `POST` ترسل حمولتها داخل جسم الرسالة (Message Body). في لوحة Inspector، يظهر قسم **Body Parameters** خصيصاً لمعالجة هذه البيانات المنفصلة وتعديل قيمها دون المساس بترويسة `Content-Length` الحساسة لحجم البايتات.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * راجع أقسام Inspector الخمسة: Request Attributes, Query Parameters, Body Parameters, Request Headers, Request Cookies.
2. **التلميح الثاني (المستوى العملي):**
   * طلبات GET لا تحتوي على جسم (Body)، بينما طلبات POST تحتوي على معطيات داخل الجسم.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * المصطلح المطلوب يتكون من كلمتين تبدأ الأولى بـ Body والثانية بـ Parameters: `Body Parameters`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشريح لوحة Inspector في Repeater:
```text
┌──────────────────────────────────────────────┐
│ [INSPECTOR SIDEBAR]                          │
├──────────────────────────────────────────────┤
│ ▼ Request Attributes                         │
│   Method: POST   Protocol: HTTP/1.1          │
│   Path: /login.php                           │
├──────────────────────────────────────────────┤
│ ▶ Query Parameters (0)                       │
├──────────────────────────────────────────────┤
│ ▼ Body Parameters (2)    <── [خاص بـ POST]    │
│   username = admin                           │
│   password = secret                          │
├──────────────────────────────────────────────┤
│ ▼ Request Headers (6)                        │
│   Content-Type: application/x-www-form...    │
│   Content-Length: 30                         │
├──────────────────────────────────────────────┤
│ ▼ Request Cookies (1)                        │
│   PHPSESSID = a1b2c3d4e5f6                   │
└──────────────────────────────────────────────┘
```

#### دور لوحة Body Parameters في اختبار الاختراق:
- اختبار حقن الأوامر و SQLi في حقول الإدخال المخفية داخل جسم الطلب.
- تعديل مصفوفات JSON أو مدخلات XML بسرعة.
- التبديل الفوري بين صيغ الترميز (Encoding) عبر أداة فك التشفير التلقائية المدمجة بالأسفل.

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال:**
  `Body Parameters`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
1. في Repeater، أنشئ طلباً من نوع `POST /login HTTP/1.1` مع إضافة جسم بسيط مثل `user=test&pass=123`.
2. انظر إلى لوحة Inspector على اليمين.
3. ستلاحظ ظهور قسم مخصص باسم **Body Parameters** يحتوي على الحقلين `user` و `pass`، بينما في طلبات GET يختفي هذا القسم أو يظهر بقيمة (0).

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* قسم **Body Parameters** في لوحة Inspector يوفر تحكماً هيكلياً آمناً في معطيات جسم الطلب لعمليات POST، مما يجنب المختبر أخطاء عدم تطابق طول المحتوى (Content-Length Mismatch) ويسمح باختبار حقول الإدخال بكفاءة عالية.
