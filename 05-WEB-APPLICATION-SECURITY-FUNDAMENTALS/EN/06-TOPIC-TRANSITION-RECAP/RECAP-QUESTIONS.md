# التقييم الشامل وتثبيت المفاهيم: أساسيات أمان تطبيقات الويب (Module 05 Recap)

---

### بيانات المراجعة والتقييم الانتقالي (Recap Metadata)
* **المسار التعليمي (Learning Path):** TryHackMe — Jr Penetration Tester (PT1 Aligned Curriculum)
* **الموديول (Module):** Module 05: Web Application Security Fundamentals (الموديول 05)
* **معرف المراجعة على المنصة (Slug):** `/recap/6afe01d5f306fc3f65927f41`
* **إجمالي الأسئلة (Total Questions):** 6 أسئلة تقييمية تخصصية شاملة لكافة أطر وخوادم الويب المشروحة.
* **النقاط المحرزة (Points Awarded):** 48 / 48 نقطة (100% نسبة إتقان ومطابقة لحساب المستخدم).

---

### جدول ملخص الأسئلة والإجابات المعتمدة (Recap Questions & Answers Summary)

| رقم السؤال | نص السؤال (Question Prompt) | نوع السؤال | الإجابة المعتمدة (Verified Answer) | الشرح والتعليل الفني (Technical Rationale) |
| :---: | :--- | :---: | :---: | :--- |
| **01** | Which curl flag stops curl from normalising encoded traversal sequences in a URL before sending the request? | خيارات متعددة | `--path-as-is` | يمنع curl من حل وحذف `.%2e/` محلياً قبل الإرسال لتصل حمولة تخطي المسار كاملة للخادم |
| **02** | Most common web servers send a robust set of security headers by default, requiring no extra configuration. | صح / خطأ | `False` | خوادم الويب الافتراضية تخلو تماماً من ترويسات الأمان وتتطلب تدخلاً يدوياً لضبطها |
| **03** | What does the Nginx server_tokens directive control? | خيارات متعددة | `Version string in the Server header and error pages` | يتحكم في كشف رقم إصدار Nginx في ترويسة Server وأسفل صفحات الخطأ الافتراضية |
| **04** | What HTTP status code indicates successful file upload via PUT? | خيارات متعددة | `201` | كود 201 Created يؤكد نجاح إنشاء المورد وتخزينه على الخادم وفق مواصفات RFC |
| **05** | Which cookie name is the default session cookie produced by the express-session middleware? | خيارات متعددة | `connect.sid` | الاسم الافتراضي لكعكة الجلسة في بيئات Express.js ويعتبر بصمة ثانوية قوية للإطار |
| **06** | Order the steps of an Apache path-traversal-to-RCE chain, where each step consumes the previous finding. | ترتيب تسلسلي | 1 ⟶ 2 ⟶ 3 ⟶ 4 | تسلسل منهجي صارم: فحص الإصدار ⟶ تأكيد معالج CGI ⟶ إرسال POST وتنفيذ id ⟶ قراءة العلم |

---

### التحليل المفصل والتفكيك التعليمي لكل سؤال (Deep Pedagogical Analysis)

---

#### السؤال 1: معامل curl لمنع تصحيح مسارات التخطي (Path Normalization Prevention in Curl)
* **نص السؤال (Question):**
  > *"Which curl flag stops curl from normalising encoded traversal sequences in a URL before sending the request?"*
* **الخيارات المتاحة (Choices):**
  1. `--path-as-is` (الإجابة الصحيحة المعتمدة)
  2. `--raw`
  3. `--no-buffer`
  4. `-k`
  5. `--globoff`
* **الإجابة الصحيحة المعتمدة (Verified Answer):**
  ```bash
  --path-as-is
  ```
* **التفكيك والتعليل الفني والعمق الأمني (In-depth Technical Explanation):**
  * افتراضياً، تطبق مكتبة `libcurl` وأداة `curl` خوارزمية تطهير وتسوية للمسارات (Path Normalization / Canonicalization) مطابقة لمعايير RFC 3986 قبل إرسال طلب الـ HTTP الفعلي عبر مقبس الشبكة (Socket).
  * إذا قام المختبر بإرسال مسار يحتوي على محارف تخطي مثل `http://target/icons/.%2e/etc/passwd`، فإن curl تفك الترميز محلياً في الذاكرة وتحول `.%2e/` إلى `../` ثم تزيلها لتصبح `http://target/etc/passwd` أو تختصرها.
  * يؤدي هذا السلوك التلقائي إلى إفشال استغلال ثغرة Apache 2.4.49 (CVE-2021-41773)، لأن خادم Apache المصاب يحتاج لاستلام السلسلة المشفرة حرفياً `.%2e/` لتجاوز دالة الفحص الأمني `ap_normalize_path`.
  * إدراج المعامل **`--path-as-is`** يجبر curl على إرسال المسار الحرفي تماماً كما كُتب دون أي تسوية أو دمج للمحارف، مما يضمن وصول حمولة التخطي بنجاح إلى هدفها.
* **تعليق المنصة الرسمي (TryHackMe Feedback):**
  > *"It tells curl to transmit the path exactly as typed, so the server receives the encoded dot-segments intact."*

---

#### السؤال 2: حقيقة ترويسات الأمان الافتراضية في خوادم الويب (Default Security Headers Reality)
* **نص السؤال (Question):**
  > *"Most common web servers send a robust set of security headers by default, requiring no extra configuration."*
* **الخيارات المتاحة (Choices):**
  1. `True`
  2. `False` (الإجابة الصحيحة المعتمدة)
* **الإجابة الصحيحة المعتمدة (Verified Answer):**
  ```text
  False
  ```
* **التفكيك والتعليل الفني والعمق الأمني (In-depth Technical Explanation):**
  * العبارة خاطئة جملة وتفصيلاً (`False`)؛ فكافة خوادم الويب الرئيسية (Apache HTTP Server، Nginx، Microsoft IIS، Python SimpleHTTP، Node.js Express) تأتي بتهيئة مصنعية تركز على سهولة التشغيل والتوافقية العكسية بدلاً من الحماية القصوى.
  * ترويسات الأمان الدفاعية الأساسية—مثل `X-Frame-Options` (لمنع Clickjacking)، و `X-Content-Type-Options: nosniff` (لمنع استنتاج نوع الوسائط MIME Sniffing)، و `Content-Security-Policy` (لمنع XSS وحقن البيانات)، و `Strict-Transport-Security` (لفرض التشفير التام HSTS)—**تكون غائبة بالكامل افتراضياً**!
  * لا يتم إرسال هذه الترويسات للمتصفحات إلا إذا قام مدير النظام بتكوينها صراحة داخل ملفات الإعداد (`apache2.conf`, `nginx.conf`, `web.config`, أو عبر حزم وسيطة مثل `helmet` في Node.js).
  * لهذا السبب بالتحديد، يعتبر تدقيق ترويسات الاستجابة (Security Headers Audit) من أول وأسهل النتائج الإيجابية المؤكدة (Low-Hanging Fruits) في أي تقرير اختبار اختراق.
* **تعليق المنصة الرسمي (TryHackMe Feedback):**
  > *"Security headers are absent unless deliberately configured, which is why a header audit is a reliable early finding."*

---

#### السؤال 3: توجيه `server_tokens` في Nginx وإخفاء الإصدار (Nginx `server_tokens` Directive)
* **نص السؤال (Question):**
  > *"What does the Nginx server_tokens directive control?"*
* **الخيارات المتاحة (Choices):**
  1. `Which TLS cipher suites are offered`
  2. `The number of worker processes started`
  3. `Version string in the Server header and error pages` (الإجابة الصحيحة المعتمدة)
  4. `Whether directory listings are generated`
  5. `Access control on the status endpoint`
* **الإجابة الصحيحة المعتمدة (Verified Answer):**
  ```text
  Version string in the Server header and error pages
  ```
* **التفكيك والتعليل الفني والعمق الأمني (In-depth Technical Explanation):**
  * يتحكم توجيه `server_tokens` في وحدة `ngx_http_core_module` بما إذا كان خادم Nginx سيعلن عن رقم إصداره الدقيق للعملاء الخارجيين أم لا.
  * القيمة الافتراضية هي `server_tokens on;`، وتتسبب في إرسال رقم الإصدار في موضعين حاسمين:
    1. في ترويسة استجابة الـ HTTP: `Server: nginx/1.24.0 (Ubuntu)`.
    2. في ذيل صفحات الخطأ الافتراضية للمتصفح: `<hr><center>nginx/1.24.0 (Ubuntu)</center>`.
  * عند ضبط التوجيه الدفاعي:
    ```nginx
    server_tokens off;
    ```
    يقوم Nginx فورياً بحذف رقم الإصدار من الترويسة ليصبح مجرد `Server: nginx`، كما يحذفه تماماً من ذيل صفحات الخطأ، مما يحرم المهاجمين وماسحات الثغرات الآلية من معرفة الثغرات المرتبطة بإصدار الخادم دون إجراء اختبارات سلوكية نشطة.
* **تعليق المنصة الرسمي (TryHackMe Feedback):**
  > *"Turning it off removes the version from both the header and the default error page footer at once."*

---

#### السؤال 4: كود حالة HTTP لنجاح رفع الملفات عبر PUT (HTTP Status Code for Successful PUT Upload)
* **نص السؤال (Question):**
  > *"What HTTP status code indicates successful file upload via PUT?"*
* **الخيارات المتاحة (Choices):**
  1. `301` (Moved Permanently)
  2. `204` (No Content)
  3. `200` (OK)
  4. `202` (Accepted)
  5. `201` (Created - الإجابة الصحيحة المعتمدة)
* **الإجابة الصحيحة المعتمدة (Verified Answer):**
  ```text
  201
  ```
* **التفكيك والتعليل الفني والعمق الأمني (In-depth Technical Explanation):**
  * وفقاً للمواصفة القياسية RFC 7231 (القسم 4.3.4) ومواصفة WebDAV RFC 4918:
    * إذا أرسل العميل طلباً بطريقة `PUT` يتضمن ملفاً أو كائناً جديداً لم يكن موجوداً من قبل في الدليل المستهدف على الخادم، ونجح الخادم في إنشاء هذا الملف وتخزينه على القرص الصلب، **يجب** على الخادم إرجاع كود الحالة **`201 Created`** مع تضمين ترويسة `Location` تشير للمسار الجديد.
    * (إذا كان الملف موجوداً مسبقاً وتم تعديله أو استبداله بالبيانات الجديدة، يعيد الخادم كود `200 OK` أو `204 No Content`).
  * في اختبارات اختراق خوادم الويب (مثل WebDAV في IIS)، يمثل استلام كود `201 Created` الدليل القاطع على نجاح عملية رفع الصدفة (Web Shell Upload Confirmation) واكتمال مرحلة التثبيت الأولي.
* **تعليق المنصة الرسمي (TryHackMe Feedback):**
  > *"HTTP 201 Created indicates the PUT request successfully created a new resource."*

---

#### السؤال 5: كعكة الجلسة الافتراضية في أطر Express.js (Default Session Cookie in Express)
* **نص السؤال (Question):**
  > *"Which cookie name is the default session cookie produced by the express-session middleware?"*
* **الخيارات المتاحة (Choices):**
  1. `connect.sid` (الإجابة الصحيحة المعتمدة)
  2. `PHPSESSID` (خاص ببيئات PHP)
  3. `csrftoken` (خاص بإطار عمل Django)
  4. `JSESSIONID` (خاص بخوادم Java Servlet مثل Tomcat و Spring)
  5. `sessionid` (خاص بملفات تعريف الجلسات في بايثون/أودو)
* **الإجابة الصحيحة المعتمدة (Verified Answer):**
  ```text
  connect.sid
  ```
* **التفكيك والتعليل الفني والعمق الأمني (In-depth Technical Explanation):**
  * تعتمد تطبيقات Node.js المبنية بإطار العمل Express على مكتبة البرمجيات الوسيطة `express-session` لإدارة جلسات المستخدمين وحفظها في الذاكرة أو في Redis/MongoDB.
  * افتراضياً، إذا لم يقم المطور بتخصيص خيار `name` في إعدادات الجلسة:
    ```javascript
    app.use(session({
      secret: 'keyboard cat',
      resave: false,
      saveUninitialized: true
    }));
    ```
    يقوم الميدلوير بتسمية كعكة الجلسة في ترويسة `Set-Cookie` باسم **`connect.sid`** (مشتقة من مكتبة `Connect` التاريخية التي تفرع منها Express).
  * صيغة قيمة الكعكة تبدأ بـ `s%3A` متبوعة برمز الجلسة المشفر وتوقيع HMAC لضمان عدم تلاعب العميل بها.
  * رؤية كعكة `connect.sid` في ترويسات الاستجابة تعتبر بصمة سلبية عالية الثقة (High Confidence Fingerprint) تؤكد أن الواجهة الخلفية تعمل بـ Node.js و Express حتى لو تم حذف ترويسة `X-Powered-By`.
* **تعليق المنصة الرسمي (TryHackMe Feedback):**
  > *"This is the default session cookie name for express-session, making it a strong secondary Express fingerprint."*

---

#### السؤال 6: الترتيب التسلسلي لسلسلة استغلال Apache RCE (Sequential Order of Apache Path Traversal to RCE)
* **نص السؤال (Question):**
  > *"Order the steps of an Apache path-traversal-to-RCE chain, where each step consumes the previous finding."*
* **الخطوات المعروضة للترتيب (Steps to Re-order):**
  * `Request /cgi-bin/ and see 403, confirming a CGI handler`
  * `Read the Server header to confirm the Apache version`
  * `Reuse the working shell path to cat the flag file`
  * `POST to the traversal path ending in /bin/sh and run id`
* **الترتيب الصحيح والمعتمد بنسبة 100% (Verified Sequential Order):**
  ```text
  1. Read the Server header to confirm the Apache version
  2. Request /cgi-bin/ and see 403, confirming a CGI handler
  3. POST to the traversal path ending in /bin/sh and run id
  4. Reuse the working shell path to cat the flag file
  ```
* **التفكيك والتعليل الفني والعمق الأمني (In-depth Technical Explanation):**
  * يجسد هذا السؤال مفهوم **سلسلة الاستغلال المنطقي (Exploitation Chain)** في اختبار الاختراق الميداني، حيث تستهلك كل خطوة مخرجات الخطوة السابقة دون أي قفز عشوائي:
    1. **الخطوة الأولى (تأكيد الإصدار المصاب):** قراءة ترويسة `Server` عبر `curl -I` للتحقق من أن الخادم يعمل بالإصدار `Apache/2.4.49` المعرض لخلل تسوية المسارات قبل فك الترميز (CVE-2021-41773).
    2. **الخطوة الثانية (تأكيد معالج التنفيذ CGI):** إرسال طلب إلى `/cgi-bin/` وملاحظة استجابة `403 Forbidden`؛ مما يؤكد وجود موديول `mod_cgi` ونشاط المسار التنفيذي، مما يفتح إمكانية تحويل ثغرة قراءة الملفات إلى تنفيذ أوامر نظام (RCE).
    3. **الخطوة الثالثة (إثبات تشغيل الأوامر عبر sh):** إرسال طلب `POST` مع معامل `--path-as-is` إلى المسار المتخطي `/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh` وتمرير أمر التحقق السريع `id` للتأكد من الحصول على هوية المستخدم `daemon`.
    4. **الخطوة الرابعة (استخراج الهدف والراية):** إعادة استخدام نفس القناة التنفيذية المؤكدة لتنفيذ أمر قراءة الملف الحساس المطلوب `cat /flag.txt` واستخراج العلم بنجاح.
* **تعليق المنصة الرسمي (TryHackMe Feedback):**
  > *"Correct order! Nice work! Recap complete. The knowledge is locked in and you've level-up in points! Total points: 48"*

---

### خلاصة الانتقال إلى الموديول القادم: Burp Suite
بهذا التقييم الشامل، يكتمل بنجاح الموديول الخامس (**Web Application Security Fundamentals**). لقد انتقلنا من استكشاف التطبيقات وفحص DOM بالمتصفح، إلى تخمين المسارات الآلي، ثم تفكيك أطر العمل الحديثة (MERN, Next.js, Django, LAMP) وخوادم الويب الكلاسيكية والمؤسسية (Apache, Nginx, Python, IIS).

المحطة القادمة في مسار Jr Penetration Tester هي **Module 06: Burp Suite**؛ الأداة المحورية والمنصة القياسية الأولى عالمياً لكل مختبر اختراق تطبيقات الويب، لنتعلم كيفية اعتراض وتعديل وتكرار وفحص وتفجير طلبات الـ HTTP بدقة جراحية متقدمة.
