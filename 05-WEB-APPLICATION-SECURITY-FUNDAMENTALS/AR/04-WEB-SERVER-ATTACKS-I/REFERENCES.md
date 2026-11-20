# المراجع والمصادر القياسية: هجمات خوادم الويب (Web Server Attacks - I)

---

### 1. معايير وإرشادات OWASP الرسمية (OWASP Standards & Guides)

* **OWASP Top 10 (2021) Alignment:**
  * **A05:2021 – Security Misconfiguration:** تغطي الغرفة هذا التصنيف بالكامل؛ حيث تشمل أخطاء التهيئة ترك الميزات الافتراضية مفعلة، وإتاحة صفحات التصحيح والإحصائيات، وغياب ترويسات الأمان الأساسية.
  * **A01:2021 – Broken Access Control:** استعراض المجلدات غير المصرح بها وتسريب بيانات الاعتماد.
  * **A04:2021 – Insecure Design:** غياب الحماية من Clickjacking عبر إهمال `X-Frame-Options`.

* **OWASP Web Security Testing Guide (WSTG v4.2):**
  * **WSTG-CONF-001:** *Test Network Infrastructure Configuration* — اختبار تهيئة خوادم الويب الأساسية والتحقق من صلاحيات المنافذ.
  * **WSTG-CONF-002:** *Test Application Platform Configuration* — فحص إصدارات الخوادم، وترويسات الاستجابة، وصفحات الخطأ الافتراضية.
  * **WSTG-CONF-004:** *Review Old Backup and Unreferenced Files for Sensitive Information* — البحث عن ملفات النسخ الاحتياطي غير المرتبطة (`.bak`, `.old`, `.zip`).
  * **WSTG-CONF-007:** *Testing for HTTP Strict Transport Security (HSTS)* — تقييم ترويسات الأمان والتشفير الإلزامي.
  * **OWASP Clickjacking Defense Cheat Sheet:** الدليل الإرشادي لحماية التطبيقات من التضمين في إطارات خبيثة.

---

### 2. معايير أمان الأنظمة القياسية (CIS Benchmarks)

* **CIS Apache HTTP Server 2.4 Benchmark:**
  * القسم 2.2: ضبط `ServerTokens Prod` لإخفاء تفاصيل الإصدار ونظام التشغيل.
  * القسم 2.3: ضبط `ServerSignature Off` لإلغاء التوقيعات من صفحات الأخطاء.
  * القسم 2.4: تعطيل الفهرسة العامة عبر `Options -Indexes`.
  * القسم 2.5: تقييد الوصول لموديول `mod_status` على الشبكات المحلية الموثوقة فقط.

* **CIS Nginx Benchmark:**
  * القسم 2.1: تعطيل كشف الإصدار عبر `server_tokens off;`.
  * القسم 2.2: تعطيل الفهرسة التلقائية `autoindex off;`.
  * القسم 3.1: تأمين مسار `stub_status` وحظر الوصول العام.

---

### 3. الوثائق والمصادر التقنية الرسمية (Official Server Documentation)

* **Apache HTTP Server Project:**
  * [Apache Core Directives (ServerTokens, Options, DirectoryIndex)](https://httpd.apache.org/docs/2.4/mod/core.html)
  * [Apache Module mod_status Documentation](https://httpd.apache.org/docs/2.4/mod/mod_status.html)
  * [Apache Module mod_autoindex Documentation](https://httpd.apache.org/docs/2.4/mod/mod_autoindex.html)

* **Nginx Documentation:**
  * [Module ngx_http_core_module (server_tokens)](https://nginx.org/en/docs/http/ngx_http_core_module.html#server_tokens)
  * [Module ngx_http_autoindex_module](https://nginx.org/en/docs/http/ngx_http_autoindex_module.html)
  * [Module ngx_http_stub_status_module](https://nginx.org/en/docs/http/ngx_http_stub_status_module.html)

* **Node.js & Express Documentation:**
  * [Express Production Best Practices: Security](https://expressjs.com/en/advanced/best-practice-security.html)
  * [Helmet Security Middleware Package](https://github.com/helmetjs/helmet)

* **Python Standard Library:**
  * [Python http.server Security Warning](https://docs.python.org/3/library/http.server.html) — تنبيه رسمي صريح: *"http.server is not recommended for production. It only implements basic security checks."*

* **Nikto Web Scanner:**
  * [Nikto Official Documentation and Source](https://github.com/sullo/nikto)

---

### 4. بطاقة الأوامر السريعة للمعمل (Command Quick Reference Sheet)

```bash
# ==============================================================================
# Web Server Attacks - I Rapid Command Reference
# ==============================================================================

# 1. Server Fingerprinting via Response Headers
curl -sI http://TARGET_IP:80/      # Apache2
curl -sI http://TARGET_IP:8000/    # Python SimpleHTTP
curl -sI http://TARGET_IP:3000/    # Node.js Express (X-Powered-By)
curl -sI http://TARGET_IP:8080/    # Nginx

# 2. Python SimpleHTTP Reconnaissance
curl -s http://TARGET_IP:8000/
curl -s http://TARGET_IP:8000/.env
wget http://TARGET_IP:8000/backup.zip && unzip backup.zip

# 3. Apache Status & Directory Listing
curl -s http://TARGET_IP:80/server-status
curl -s http://TARGET_IP:80/files/
curl -s http://TARGET_IP:80/files/internal-notes.txt
gobuster dir -u http://TARGET_IP:80 -w common.txt -x bak,txt

# 4. Node.js Debugging & Routes Inspection
curl -s http://TARGET_IP:3000/api/routes
curl -s http://TARGET_IP:3000/api/debug/env
curl -s http://TARGET_IP:3000/static/config.js

# 5. Nginx Status & Directory Listing
curl -s http://TARGET_IP:8080/nginx_status
curl -s http://TARGET_IP:8080/files/
curl -s http://TARGET_IP:8080/files/deploy-notes.txt

# 6. Automated Security Header & Misconfiguration Audit
nikto -h http://TARGET_IP:80 -nointeractive
```
