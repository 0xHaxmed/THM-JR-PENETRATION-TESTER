# Task 06: Nginx (خادم إنجنكس وتوجيه autoindex وتسريب إحصائيات nginx_status)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Nginx is a high-performance HTTP server, reverse proxy, and load balancer designed for maximum concurrency and low memory usage. In production architectures, Nginx frequently sits at the edge, terminating SSL, serving static files, and proxying API traffic to application backends like Node.js or Django.

Despite its security reputation, standard configuration errors can expose internal files and infrastructure metrics:
1. **Version Disclosure (`server_tokens`):**
   By default, `server_tokens on;` causes Nginx to append its version number to the `Server` header (`nginx/1.24.0 (Ubuntu)`) and render it across all standard error page footers.
2. **Directory Listing (`autoindex on`):**
   When `autoindex on;` is placed within a `location` block (e.g. `location /files/`), Nginx automatically generates a directory index if no index file exists.
   - On port 8080, navigating to `/files/` exposes configuration files, deployment notes, and flags.
3. **Exposed Connection Metrics (`stub_status`):**
   The `ngx_http_stub_status_module` provides basic status information about active client connections, accepted connections, and total requests. When enabled with `allow all;` or missing access restrictions, it is typically accessible at the conventional path **`/nginx_status`**.

#### الأسئلة المطروحة (Questions):
1. What Nginx directive enables directory listing in a location block? (Answer Format: directive value)
2. What URL path exposes Nginx connection statistics? (Answer Format: /path_name)
3. What is the flag found in the directory listing on port 8080? (Answer Format: THM{...})

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المخاطر التكتيكية لـ `stub_status`:**
  * معرفة إحصائيات الاتصال الحية عبر `/nginx_status` تفيد المهاجمين في تقدير حجم حركة المرور الحقيقي للموقع، ومراقبة نجاح هجمات حجب الخدمة (DoS/DDoS)، ومعرفة عدد الاتصالات الجارية، مما يكشف أوقات انخفاض نشاط المسؤولين.
* **الفرق بين فهرسة Apache و Nginx:**
  * في Apache، يتم تفعيل الفهرسة عبر توجيه `Options +Indexes`.
  * في Nginx، يتم تفعيل الفهرسة عبر التوجيه المباشر `autoindex on;`.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * التوجيه المسؤول عن فهرسة الملفات في ملفات تهيئة Nginx يتكون من كلمتين متبوعتين بـ on.
2. **التلميح الثاني (المستوى العملي):**
   * المسار التقليدي لصفحة حالة Nginx يبدأ بـ `/nginx_...`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * استعرض المجلد `http://MACHINE_IP:8080/files/` وافحص الملفات المعروضة (مثل `deploy-notes.txt` أو `server-config.txt`) لاستخراج العلم.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: تحديد توجيه الفهرسة ومسار الحالة
* **توجيه الفهرسة في ملف التهيئة:**
  ```nginx
  location /files/ {
      autoindex on;
  }
  ```
  * القيمة المعتمدة: **`autoindex on`**.

* **فحص مسار إحصائيات Nginx:**
  ```bash
  curl -s http://MACHINE_IP:8080/nginx_status
  ```
  **المخرجات:**
  ```text
  Active connections: 1 
  server accepts handled requests
   42 42 128 
  Reading: 0 Writing: 1 Waiting: 0 
  ```
  * المسار المعتمد: **`/nginx_status`**.

---

#### الخطوة 2: استعراض المجلد المفهرس وقراءة الملفات الحساسة
```bash
# استعراض المجلد /files/ على المنفذ 8080
curl -s http://MACHINE_IP:8080/files/
```
**المخرجات:**
```html
<html>
<head><title>Index of /files/</title></head>
<body>
<h1>Index of /files/</h1><hr><pre><a href="../">../</a>
<a href="deploy-notes.txt">deploy-notes.txt</a>
<a href="old-backup.tar.gz">old-backup.tar.gz</a>
<a href="server-config.txt">server-config.txt</a>
</pre><hr></body>
</html>
```

---

#### الخطوة 3: قراءة ملف الملاحظات واستخراج العلم
```bash
# قراءة ملف deploy-notes.txt
curl -s http://MACHINE_IP:8080/files/deploy-notes.txt
```
**المحتويات:**
```text
Nginx Deployment Checklist:
1. Verify SSL certificates.
2. Turn off server_tokens.
3. Remove autoindex directive from public locations.
Audit Flag: THM{nginx_autoindex}
```
* **العلم المستخرج:** `THM{nginx_autoindex}`

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Nginx Directory Listing Directive):** `autoindex on`
* **إجابة السؤال الثاني (Connection Statistics URL Path):** `/nginx_status`
* **إجابة السؤال الثالث (Port 8080 Flag):** `THM{nginx_autoindex}`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تأكيد توجيه `autoindex on` المسؤول عن الفهرسة.
* تم استعراض وقراءة إحصائيات `/nginx_status`.
* تم فحص ملفات `/files/` واستخراج العلم بنجاح: `THM{nginx_autoindex}`.
* تم اعتماد الإجابات رسمياً على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
في خوادم Nginx، يجب التأكد من ضبط `autoindex off;` في جميع كتل `location` المفتوحة، وحجب مسار `/nginx_status` وجعله مقتصراً على عناوين IP المصرح لها عبر `allow 127.0.0.1; deny all;`، مع إخفاء الإصدار عبر `server_tokens off;`.
