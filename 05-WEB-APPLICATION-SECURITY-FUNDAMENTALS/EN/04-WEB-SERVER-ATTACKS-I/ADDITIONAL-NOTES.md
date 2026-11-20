# الملاحظات الإضافية والتعمق التقني: أخطاء تهيئة خوادم الويب (Web Server Attacks - I)

---

### 1. التحليل المعماري لخادم Apache2 وأخطاء التهيئة (Apache Architecture & Directives)

#### توجيهات إخفاء البصمة وإصدار الخادم:
* **`ServerTokens`:** يتحكم في محتوى ترويسة `Server`. يمتلك 6 مستويات:
  * `Full`: يكشف كل شيء (`Apache/2.4.58 (Ubuntu) OpenSSL/3.0.13 PHP/8.2.1`).
  * `OS`: يكشف الإصدار ونظام التشغيل (`Apache/2.4.58 (Ubuntu)`).
  * `Min`: يكشف الإصدار فقط (`Apache/2.4.58`).
  * `Minor`: يكشف الإصدار الرئيسي والفرعي (`Apache/2.4`).
  * `Major`: يكشف الإصدار الرئيسي فقط (`Apache/2`).
  * **`Prod` (الخيار الآمن):** يكشف اسم الخادم فقط (`Server: Apache`).
* **`ServerSignature`:** يتحكم في الفوتر المعروض أسفل صفحات الخطأ الافتراضية (404, 403, 500). يجب دائماً ضبطه على `Off`.

#### آلية فهرسة المجلدات (`Options Indexes`):
عندما يطلب العميل مسار مجلد (`http://target/files/`)، يبحث أباتشي عن الملفات المحددة في توجيه `DirectoryIndex` (مثل `index.html`, `index.php`).
* إذا لم يجد أياً منها وكان التوجيه `Options +Indexes` مفعلاً، يقوم موديول `mod_autoindex` بتوليد كود HTML ديناميكي يعرض شجرة الملفات بالكامل.
* لتعطيل الفهرسة كلياً عبر الخادم:
  ```apache
  <Directory /var/www/>
      Options -Indexes
  </Directory>
  ```

#### تشريح موديول الحالة `mod_status`:
يوفر الموديول واجهة قياس داخلية. إذا تم تفعيله دون تقييد، يمكن استغلاله عبر:
* قراءة الروابط النشطة الحالية (`Current Requests`).
* معرفة أنماط المرور وعناوين IP للمستخدمين.
* استخراج الرموز المميزة ومفاتيح الـ API الممررة في مسارات الـ GET.
* **التهيئة الآمنة لـ `mod_status`:**
  ```apache
  <Location /server-status>
      SetHandler server-status
      Require local
  </Location>
  ```

---

### 2. التشريح الداخلي لخادم بايثون (Python `http.server` Internals)

يعتمد خادم بايثون المدمج (`SimpleHTTPRequestHandler`) على معيار `http.server` المصمم للأغراض التعليمية والاختبارات السريعة فقط:
```python
# كود مبسط يوضح سلوك بايثون في الفهرسة
def send_head(self):
    path = self.translate_path(self.path)
    if os.path.isdir(path):
        for index in "index.html", "index.htm":
            index = os.path.join(path, index)
            if os.path.exists(index):
                path = index
                break
        else:
            return self.list_directory(path) # يولد الفهرسة تلقائياً!
```

#### المخاطر الحرجة لخادم بايثون المدمج:
1. **انعدام الحماية للملفات المخفية (No Dotfile Protection):**
   يقوم الخادم بمشاركة جميع الملفات الموجودة في المجلد الحالي دون استثناء، حتى تلك التي تبدأ بنقطة (`.env`, `.git`, `.htaccess`).
2. **غياب آليات المصادقة (No Authentication Mechanism):**
   لا يدعم الخادم ملفات `.htpasswd` أو المصادقة الأساسية (Basic Auth).
3. **أحادية المعالجة المتزامنة (Single-Threaded / Concurrency Limits):**
   في الإصدارات الافتراضية، يمكن لطلب بطيء واحد أن يعطل السيرفر عن خدمة باقي المستخدمين (Slowloris Denial of Service).

---

### 3. خادم Node.js Express والعمق المعماري (Express Architecture & Debug Risks)

#### فحص شجرة المسارات عبر `app._router.stack`:
في Express، يتم تخزين كافة المسارات المسجلة وميدلوير التطبيق داخل مصفوفة كائنات داخلية تدعى `_router.stack`.
عندما يقوم المطور بكتابة مسار استكشافي مثل:
```javascript
app.get('/api/routes', (req, res) => {
    const routes = app._router.stack
        .filter(r => r.route)
        .map(r => ({ path: r.route.path, methods: Object.keys(r.route.methods) }));
    res.json(routes);
});
```
فإنه يمنح المهاجم خريطة كاملة لكافة واجهات برمجة التطبيقات المخفية ونقاط النهاية الإدارية بطلب واحد!

#### تسريب `process.env`:
* يحتوي كائن `process.env` على متغيرات بيئة التشغيل لنظام التشغيل وحاوية Docker.
* كتابة مسار يرجع هذا الكائن يمثل خرقاً أمنياً مطلقاً:
  ```javascript
  app.get('/api/debug/env', (req, res) => {
      res.json(process.env); // تسريب كلمات المرور ومفاتيح التشفير
  });
  ```

#### معالجة الملفات الثابتة عبر `express.static`:
* تقوم دالة `express.static('public')` بتقديم الملفات الثابتة.
* افتراضياً، تتجاهل دالة Express الملفات المخفية (`dotfiles: 'ignore'`)، ولكنها لا تمنع تنزيل ملفات التكوين المكشوفة مثل `config.js` أو `app.bundle.js.map`.

---

### 4. خادم Nginx والتحليل المعماري (Nginx Directives & Vulnerabilities)

#### الفهرسة التلقائية `autoindex`:
```nginx
location /files/ {
    autoindex on;
    autoindex_exact_size off; # يعرض الحجم بالكيلوبايت والميجابايت
    autoindex_localtime on;   # يعرض الوقت المحلي
}
```
* تفعيل هذا التوجيه يحول المجلد إلى مستودع عام للملفات.

#### ثغرة الـ Alias الشائعة (Off-by-Slash Path Traversal):
من أشهر أخطاء تهيئة Nginx الشائعة:
```nginx
location /files {
    alias /var/www/static/;
}
```
* لاحظ غياب الشرطة المائلة المائلة (`/`) بعد كلمة `files` في سطر `location` مع وجودها في سطر `alias`!
* هذا يسمح للمهاجم بطلب: `http://target/files../` مما يؤدي إلى قراءة المجلد الأب `/var/www/` بالكامل!

#### موديول الحالة `ngx_http_stub_status_module`:
```nginx
location /nginx_status {
    stub_status on;
    allow 127.0.0.1;
    deny all;
}
```
* إذا تم حذف سطور `allow` و `deny`، يصبح المسار مكشوفاً للإنترنت ويعرض إحصائيات الاتصالات اللحظية.

---

### 5. الدليل الشامل لترويسات أمان HTTP (HTTP Security Headers Framework)

```http
# الحزمة القياسية لترويسات الأمان الدفاعية (Defense-in-Depth)

# 1. منع هجمات الاختطاف النقري (Anti-Clickjacking)
X-Frame-Options: DENY

# 2. منع استنتاج نوع المحتوى (MIME-Sniffing Prevention)
X-Content-Type-Options: nosniff

# 3. سياسة أمان المحتوى الصارمة (Cross-Site Scripting Mitigation)
Content-Security-Policy: default-src 'self'; frame-ancestors 'none';

# 4. فرض التشفير التام (Enforce HTTPS)
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

# 5. حماية خصوصية الروابط المحولة (Referrer Privacy)
Referrer-Policy: strict-origin-when-cross-origin

# 6. تقييد صلاحيات المتصفح للأجهزة (Permissions Policy)
Permissions-Policy: camera=(), microphone=(), geolocation=()
```
