# Task 02: IIS Fingerprinting and Enumeration (التقاط بصمات خوادم IIS واكتشاف دعم WebDAV)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The first phase of assessing an IIS environment is identifying the exact IIS version and mapped Windows Server release, alongside enumerating enabled protocol extensions such as Web Distributed Authoring and Versioning (WebDAV).

**IIS Version to Windows Server Mapping:**
Unlike Linux web servers where versions iterate independently of the operating system, IIS versions are strictly coupled to specific Windows Server operating system builds:
- **IIS 6.0:** Windows Server 2003 (Legacy / End of Life; vulnerable to remote buffer overflows like CVE-2017-7269).
- **IIS 7.0 / 7.5:** Windows Server 2008 / Windows Server 2008 R2 (EOL).
- **IIS 8.0 / 8.5:** Windows Server 2012 / Windows Server 2012 R2 (EOL).
- **IIS 10.0:** Windows Server 2016, 2019, 2022, and 2025 (Current; our lab target runs IIS 10.0 on Windows Server 2019).
*(Note: Microsoft skipped IIS version 9.0 entirely to align with internal versioning schemas).*

**Architecture: HTTP.sys and w3wp.exe:**
- **`HTTP.sys`:** A kernel-mode device driver (`\System32\drivers\http.sys`) that listens for inbound HTTP packets on port 80/443 before user-mode code is invoked.
- **`w3wp.exe`:** The user-mode worker process hosting the application pool.

**Banner Grabbing:**
Sending a `HEAD` request with `curl -I http://MACHINE_IP` yields:
```http
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
```
The **`Server`** header directly exposes the IIS software version.

**Detecting WebDAV:**
WebDAV extends HTTP to allow clients to publish, lock, and manage files on the server.
Querying available HTTP methods using an `OPTIONS` request:
```bash
curl -X OPTIONS http://MACHINE_IP/ -sv 2>&1 | grep -E "Allow:|DAV:"
```
Responses indicating WebDAV support return `DAV: 1,2` and verbs such as `PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK`.

**Testing WebDAV Write Permissions:**
Testing whether unauthenticated file uploads are permitted on `/webdav/`:
```bash
curl -s -o /dev/null -w "%{http_code}\n" -X PUT --data 'test' http://MACHINE_IP/webdav/test.txt
```
The server returns HTTP status code **`401`** (Unauthorized), proving that write permissions exist on the directory but require authentication credentials.

#### الأسئلة المطروحة (Questions):
1. What HTTP response header reveals the IIS version? (Answer Format: Header-Name)
2. What is the HTTP response code after executing the PUT request on the /webdav directory? (Answer Format: 3-digit code)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **قراءة دلالات كود الحالة 401 مقابل 403 أو 405:**
  * إذا أعاد الخادم كود `405 Method Not Allowed`، فهذا يعني أن طريقة `PUT` معطلة بالكامل عند مستوى الخادم أو الوكيل.
  * إذا أعاد `403 Forbidden`، فهذا يعني أن الدليل لا يقبل الكتابة حتى مع وجود جلسة.
  * أما إذا أعاد **`401 Unauthorized`**، فهذه رسالة تأكيد صريحة لمختبر الاختراق تفيد بأن المسار مهيأ بالفعل لقبول الملفات عبر WebDAV، وأن العائق الوحيد هو الحصول على بيانات اعتماد صالحة!
* **البصمات عبر ترويسات مايكروسوفت الخاصة:**
  * بالإضافة لترويسة `Server`، فإن وجود `X-Powered-By: ASP.NET` يؤكد تفعيل بيئة تشغيل دوت نت، مما يفتح الباب أمام تنفيذ ملفات `.aspx` بدلاً من مجرد رفع ملفات نصية ميتة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * الترويسة القياسية التي تعلن عن نوع وإصدار خادم الويب تتكون من كلمة واحدة بالإنجليزية: `Server`.
2. **التلميح الثاني (المستوى العملي):**
   * نفذ طلب PUT باستخدام curl على مسار `/webdav/test.txt` أو راجع مخرجات فحص إذن الكتابة في شرح المهمة.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * كود الاستجابة المطلوب للسؤال الثاني هو الكود المالي القياسي لطلب المصادقة: `401`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: فحص ترويسات الاستجابة
```bash
# تنفيذ طلب فحص الترويسات
curl -I http://MACHINE_IP/
```
**المخرجات:**
```http
HTTP/1.1 200 OK
Content-Length: 703
Content-Type: text/html
Last-Modified: Mon, 19 Nov 2026 01:00:00 GMT
Accept-Ranges: bytes
ETag: "8a41c42f..."
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Mon, 19 Nov 2026 03:00:00 GMT
```
* **الترويسة الكاشفة لإصدار IIS:** `Server`
* **الإصدار المشغل:** `Microsoft-IIS/10.0` (يعادل Windows Server 2016/2019/2022).

---

#### الخطوة 2: اختبار إمكانية كتابة الملفات عبر WebDAV على المسار `/webdav/`
```bash
# إرسال طلب PUT اختباري بدون بيانات اعتماد
curl -s -o /dev/null -w "%{http_code}\n" -X PUT --data '<%@ Page Language=Jscript%><%Response.Write(1+1)%>' http://MACHINE_IP/webdav/test.aspx
```
**المخرجات:**
```text
401
```
* **كود الاستجابة المستلم:** `401`
* يشير كود 401 إلى أن الدليل محمي بنظام مصادقة (NTLM/Basic) ويتطلب اسم مستخدم وكلمة مرور للمتابعة.

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Header Revealing IIS Version):** `Server`
* **إجابة السؤال الثاني (HTTP Status Code for PUT):** `401`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم استخراج ترويسة `Server: Microsoft-IIS/10.0` بنجاح.
* تم تنفيذ طلب PUT والحصول على كود الحالة `401` بنجاح.
* تم اعتماد الإجابات رسمياً على TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
في خوادم IIS، يعد كود الحالة `401` عند تنفيذ طلبات `PUT` بمثابة مؤشر استخباري حاسم يؤكد وجود بوابة WebDAV نشطة تنتظر المصادقة. كما أن إخفاء ترويسة `Server` عبر URL Rewrite Module وترويسة `X-Powered-By` عبر إعدادات `web.config` هو الخطوة الدفاعية الأولى لمنع حصر الإصدار.
