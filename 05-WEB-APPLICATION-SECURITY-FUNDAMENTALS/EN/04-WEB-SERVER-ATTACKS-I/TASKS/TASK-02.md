# Task 02: Identifying Web Servers (تحديد والتقاط بصمات خوادم الويب عبر ترويسات HTTP)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The first step in any web infrastructure assessment is identifying what server software and underlying runtime are handling incoming requests.

**Fingerprinting via HTTP Headers:**
The most straightforward method to fingerprint a web server is by sending a `HEAD` request to inspect response headers without downloading the full page payload:
```bash
curl -sI http://MACHINE_IP:PORT
```

On our Ubuntu target machine, inspecting each port yields:
1. **Port 80 (Apache2):**
   ```http
   Server: Apache/2.4.58 (Ubuntu)
   ```
2. **Port 8000 (Python HTTP Server):**
   ```http
   Server: SimpleHTTP/0.6 Python/3.12.3
   ```
3. **Port 3000 (Node.js Express):**
   Express does not emit a `Server` header by default. Instead, it advertises the underlying framework through:
   ```http
   X-Powered-By: Express
   ```
4. **Port 8080 (Nginx):**
   ```http
   Server: nginx/1.24.0 (Ubuntu)
   ```

**Default Error Pages:**
In addition to headers, servers often reveal their software in default 404 or 403 error pages:
- Python SimpleHTTP generates bare plain-text errors: `<p>Error code: 404</p>`.
- Nginx generates centered HTML with a footer: `<hr><center>nginx/1.24.0 (Ubuntu)</center>`.
- Apache displays a standard formatted page with server signature at the bottom.

#### الأسئلة المطروحة (Questions):
1. What value does the Server header return for the Python HTTP Server running on port 8000? (Answer Format: SimpleHTTP/X.X Python/X.X.X)
2. Which HTTP header reveals the application framework running on port 3000? (Answer Format: Header-Name)
3. What web server software is running on port 8080? (Answer Format: server_name)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **دلالة ترويسة `Server` الأمنية:**
  * كشف اسم الخادم وإصداره ونظام التشغيل (`Apache/2.4.58 (Ubuntu)`) يزود المهاجمين بمعلومات ذهبية لتحديد مسارات الهجوم المخصصة دون الحاجة لإجراء مسح نشط (Vulnerability Scanning) قد يثير أجهزة كشف التسلل.
* **الفرق بين خادم الويب وخادم التطبيقات (Web Server vs Application Framework):**
  * خوادم الويب (مثل Nginx و Apache) تضع هويتها في ترويسة `Server`.
  * أطر عمل التطبيقات (مثل Express و PHP و ASP.NET) تستخدم ترويسات مثل `X-Powered-By` أو `X-AspNet-Version`.
  * إزالة أو تزييف هذه الترويسات يعد خطوة دفاعية أولى لحرمان المهاجمين من البصمة السريعة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * استخدم أداة curl مع المعاملين `-s` (صامت) و `-I` (جلب الترويسات فقط) ضد كل منفذ.
2. **التلميح الثاني (المستوى العملي):**
   * في المنفذ 8000، ابحث عن سطر `Server:` وانسخ القيمة بالكامل متضمنة رقم إصدار Python.
   * في المنفذ 3000، ابحث عن الترويسة التي تبدأ بـ `X-` وتحدد Express.
   * في المنفذ 8080، افحص اسم خادم الويب الظاهر في ترويسة `Server:`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * القيم المحددة هي: `SimpleHTTP/0.6 Python/3.12.3` للمنفذ 8000، و `X-Powered-By` للمنفذ 3000، و `nginx` للمنفذ 8080.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تنفيذ أوامر الفحص والتقاط الترويسات:
```bash
# 1. فحص المنفذ 8000 (Python SimpleHTTP)
curl -sI http://MACHINE_IP:8000/ | grep -i "^server:"
```
**المخرجات:**
```http
Server: SimpleHTTP/0.6 Python/3.12.3
```
* **القيمة المستخرجة:** `SimpleHTTP/0.6 Python/3.12.3`

---

```bash
# 2. فحص المنفذ 3000 (Node.js Express)
curl -sI http://MACHINE_IP:3000/
```
**المخرجات:**
```http
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 58
```
* **الترويسة الكاشفة لإطار العمل:** `X-Powered-By`

---

```bash
# 3. فحص المنفذ 8080 (Nginx)
curl -sI http://MACHINE_IP:8080/ | grep -i "^server:"
```
**المخرجات:**
```http
Server: nginx/1.24.0 (Ubuntu)
```
* **اسم برنامج الخادم:** `nginx`

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Python Server Header):** `SimpleHTTP/0.6 Python/3.12.3`
* **إجابة السؤال الثاني (Framework Header):** `X-Powered-By`
* **إجابة السؤال الثالث (Port 8080 Server Software):** `nginx`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم استخراج قيمة ترويسة بايثون بدقة مطابقة للنص المطلوب.
* تم تأكيد ترويسة `X-Powered-By` لإطار Express.
* تم استخراج هوية `nginx` على المنفذ 8080.
* جميع الإجابات تم اعتمادها بنجاح 100% على منصة TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
الفحص السلبي لترويسات الردود (HTTP Response Headers) هو بطاقة تعريف النظام. يجب على المدافعين (Blue Team) إيقاف كشف الترويسات التلقائية (عبر `ServerTokens Prod` في Apache، و `server_tokens off;` في Nginx، و `app.disable('x-powered-by')` في Express) لمنع المهاجمين من معرفة التوليفة البرمجية للهدف.
