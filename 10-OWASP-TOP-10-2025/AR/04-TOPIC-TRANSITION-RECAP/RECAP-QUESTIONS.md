# Module 10 Topic Transition Recap · Questions & Technical Synthesis

وثيقة الأسئلة التقنية والتحليل الشامل لاختبار المراجعة والانتقال لوحدة **معيار OWASP Top 10 لعام 2025 (Module 10: OWASP Top 10 - 2025)** ضمن مسار Junior Penetration Tester على منصة TryHackMe.

---

## 📌 بيانات الاختبار والنتيجة المعتمدة

- **معرف الاختبار (Recap ID):** `6afe01d5f306fc3f65927ffe`
- **الوحدة التعليمية:** Module 10: OWASP Top 10 (2025)
- **الغرف المشمولة في المراجعة:**
  1. OWASP Top 10 2025: IAAA Failures (`owasptopten2025one`)
  2. OWASP Top 10 2025: Application Design Flaws (`owasptopten2025two`)
  3. OWASP Top 10 2025: Insecure Data Handling (`owasptopten2025three`)
- **عدد الأسئلة:** 6 أسئلة متنوعة (اختيار من متعدد، صح وخطأ، مطابقة أزواج، وترتيب زمني)
- **النتيجة المحققة:** **48 / 48 نقطة (100% الدرجة الكاملة)**
- **حالة التدقيق:** مطابقة حية وموثقة بنسبة 100% مع حساب المستخدم المكتمل

---

## 📋 تفكيك الأسئلة والتحليل التقني المتعمق (Question Bank & Technical Dissection)

### السؤال الأول (Question 1) · إدارة أوراق الاعتماد والأسرار الرقمية (Secret Management)
- **معرف السؤال:** `979148d4-fdfd-4b95-a5d6-d0d46d0a75f8`
- **نص السؤال:**
  > What is the recommended way for an application to obtain credentials for a third-party service?
  > (ما هي الطريقة الموصى بها معمارياً لتطبيق ما للحصول على أوراق الاعتماد ومفاتيح الوصول لخدمة خارجية؟)
- **الخيارات المتاحة:**
  - [x] **`Read them from a secure secret management system at runtime` (قراءتها في زمن التشغيل من نظام مخصص لإدارة الأسرار)**
  - [ ] `Commit them encrypted into source control alongside the code` (إيداعها مشفرة في نظام إدارة النسخ Git إلى جانب الكود)
  - [ ] `Store them as plaintext constants in a shared configuration file` (تخزينها كنصوص صريحة وثوابت في ملف إعدادات مشترك)
  - [ ] `Generate them dynamically on each deployment and log them to disk` (توليدها ديناميكياً مع كل نشر وتسجيلها في السجلات على القرص)
- **الإجابة المعتمدة:** **`Read them from a secure secret management system at runtime`**
- **التحليل التقني والتعليل الأمني (Root Cause Analysis):**
  تخزين أوراق الاعتماد أو المفاتيح في مستودعات الكود (حتى لو كانت مستودعات خاصة أو ملفات مشفرة) يمثل مخالفة صريحة لمعايير AS04 و AS02. الطريقة المعيارية المعتمدة في البيئات السحابية (Enterprise Cloud Architecture) هي استخدام أنظمة إدارة أسرار مخصصة (مثل HashiCorp Vault أو AWS Secrets Manager أو GCP Secret Manager أو Azure Key Vault). يقوم التطبيق بمصادقة هويته في زمن التشغيل (Runtime) عبر هوية مدارة (Managed Identity / IAM Role) واستدعاء الأسرار مباشرة إلى الذاكرة المؤقتة دون كتابتها مطلقاً على القرص الصلب، مما يتيح تدوير المفاتيح (Key Rotation) والتدقيق المركزي لسجلات الوصول.

---

### السؤال الثاني (Question 2) · أمن جانب الخادم مقابل جانب العميل (Client vs Server Enforcement)
- **معرف السؤال:** `6a6c0b96-650a-4a6c-9407-7e61ff8cba38`
- **نص السؤال:**
  > Hiding the admin link from the navigation menu is an adequate way to stop normal users reaching admin functions.
  > (هل يُعد إخفاء رابط لوحة الإدارة من قائمة التنقل وسيلة كافية لمنع المستخدمين العاديين من الوصول للوظائف الإدارية؟)
- **الخيارات المتاحة:**
  - [ ] `True` (صحيح)
  - [x] **`False` (خطأ)**
- **الإجابة المعتمدة:** **`False`**
- **التحليل التقني والتعليل الأمني (Root Cause Analysis):**
  يُعد الاعتماد على إخفاء الروابط في الواجهة الأمامية نموذجاً فاشلاً يُعرف بـ "الأمان عبر الغموض" (Security through Obscurity) وهو جوهر ثغرات انهيار ضوابط الوصول (A01: Broken Access Control). متصفح الويب يقع بنسبة 100% تحت سيطرة المستخدم، وبإمكان أي مختبر اختراق استخدام أدوات مثل Burp Suite أو تخمين المسارات الإدارية يدوياً (`/admin`, `/supersecretadminstuff`) للوصول إلى الوظائف. يجب دائماً وأبداً فرض قيود المصادقة والتفويض الصارمة في جانب الخادم (Server-Side Authorization Checks) مع كل طلب HTTP وارد قبل إرجاع أي بيانات أو تنفيذ أي وظيفة.

---

### السؤال الثالث (Question 3) · الخوارزميات التشفيرية المتقادمة (Cryptographic Failures)
- **معرف السؤال:** `90e25b1b-fb29-4ee2-b915-d4198c812166`
- **نص السؤال:**
  > Which pair of algorithms should be avoided for protecting sensitive data in new systems?
  > (أي زوج من الخوارزميات التالية يجب تجنبه تماماً لحماية البيانات الحساسة في الأنظمة الحديثة؟)
- **الخيارات المتاحة:**
  - [x] **`MD5 and SHA-1` (خوارزميتا MD5 و SHA-1)**
  - [ ] `RSA-OAEP and ECDH`
  - [ ] `Argon2 and bcrypt`
  - [ ] `TLS 1.3 and HSTS`
  - [ ] `AES-GCM and ChaCha20-Poly1305`
- **الإجابة المعتمدة:** **`MD5 and SHA-1`**
- **التحليل التقني والتعليل الأمني (Root Cause Analysis):**
  وفقاً لتصنيف A04:2025 (Cryptographic Failures) والمعايير الصادرة عن معهد NIST، تُعد كل من `MD5` و `SHA-1` خوارزميات تجزئة مكسورة رياضياً وعرضة لهجمات التصادم العملي (Practical Collision Attacks) مثل هجوم SHAttered وهجمات Flame malware. لا يجوز استخدامها إطلاقاً لتوقيع الشهادات، أو التحقق من سلامة البرمجيات، أو حماية كلمات المرور. في المقابل، تُمثل عائلات `SHA-256` و `SHA-3` و `Argon2id` البدائل الآمنة والمعتمدة عالمياً.

---

### السؤال الرابع (Question 4) · أمان فك تسلسل كائنات بايثون (Insecure Deserialization)
- **نص السؤال (Dynamic Live Variant):**
  > Python's pickle module can safely deserialise data received from untrusted sources.
  > (هل تستطيع مكتبة pickle في بايثون فك تسلسل البيانات الواردة من مصادر غير موثوقة بأمان؟)
- **الخيارات المتاحة:**
  - [ ] `True` (صحيح)
  - [x] **`False` (خطأ)**
- **الإجابة المعتمدة:** **`False`**
- **التحليل التقني والتعليل الأمني (Root Cause Analysis):**
  تُصنف مكتبة `pickle` على أنها غير آمنة بطبيعتها المعمارية (Inherently Insecure) لأنها تعمل كآلة افتراضية لتنفيذ التعليمات المكدسة (Stack Virtual Machine). عند استدعاء `pickle.loads()`، يقوم المحرك باستدعاء أي كائن أو دالة يحددها المهاجم عبر التابع السحري `__reduce__()` (مثل `subprocess.Popen` أو `os.system`)، مما يؤدي فورياً لتنفيذ الأوامر عن بعد (RCE) في سياق الخادم. لا تحتوي المكتبة على أي تدقيق للسلامة، وتوصي وثائق بايثون الرسمية بحظر استخدامها مع البيانات غير الموثوقة واستبدالها بصيغ غير تنفيذية مثل `JSON`.

---

### السؤال الرابع البديل (Question 4 Alternate) · الحماية من تزوير الطلبات عبر المواقع (CSRF Prevention)
- **معرف السؤال:** `3281be78-0cb9-4700-aa10-e74f26ca4a0b`
- **نص السؤال:**
  > Which design choice prevents Cross-Site Request Forgery?
  > (أي خيار تصميمي يمنع ثغرات تزوير الطلبات عبر المواقع CSRF؟)
- **الخيارات المتاحة:**
  - [x] **`Enforcing SameSite cookie attributes and validating anti-CSRF tokens` (فرض سمة SameSite على ملفات الكوكيز والتحقق من رموز مكافحة CSRF)**
  - [ ] `Rejecting all incoming requests that omit the User-Agent header`
  - [ ] `Validating that the request body conforms to an expected JSON schema`
  - [ ] `Encrypting all query parameters using AES in CBC mode`
- **الإجابة المعتمدة:** **`Enforcing SameSite cookie attributes and validating anti-CSRF tokens`**
- **التحليل التقني والتعليل الأمني (Root Cause Analysis):**
  تعتمد هجمات CSRF على إرسال المتصفح لملفات تعريف الارتباط الخاصة بالجلسة تلقائياً مع الطلبات العابرة للمواقع (Cross-Site Requests). يتم إحباط هذا الهجوم معمارياً عبر خطوتين متكاملتين: الأولى هي تفعيل سمة `SameSite=Strict` أو `SameSite=Lax` على الكوكيز لمنع المتصفح من إرسالها تلقائياً مع الطلبات الخارجية، والثانية هي التحقق من وجود رمز عشوائي لا يمكن للمهاجم التنبؤ به (Cryptographically Random Anti-CSRF Token) مرتبط بالجلسة ويتم إرساله داخل نموذج الطلب أو ترويسات HTTP المخصصة.

---

### السؤال الخامس (Question 5) · آلية عمل حقن القوالب (Server-Side Template Injection)
- **نص السؤال (Dynamic Live Variant):**
  > Server-side template injection abuses which application feature?
  > (ما هي ميزة التطبيق التي تستغلها ثغرات حقن القوالب على خادم الويب SSTI؟)
- **الخيارات المتاحة:**
  - [x] **`The engine that renders dynamic content from templates` (المحرك البرمجي الذي يقوم بتصيير المحتوى الديناميكي من القوالب)**
  - [ ] `The TLS handshake negotiation` (تفاوض مصافحة تشفير TLS)
  - [ ] `The browser's JavaScript execution engine` (محرك جافاسكريبت في متصفح الضحية)
  - [ ] `The database query optimiser` (محسن استعلامات قواعد البيانات)
  - [ ] `The web server's static file handler` (معالج الملفات الثابتة في خادم الويب)
- **الإجابة المعتمدة:** **`The engine that renders dynamic content from templates`**
- **التحليل التقني والتعليل الأمني (Root Cause Analysis):**
  تنشأ ثغرة SSTI (المصنفة ضمن A05: Injection) عندما يقوم المطور بدمج نصوص المستخدمين مباشرة داخل نص القالب قبل تمريره لمحرك التصيير (مثل Jinja2 أو Twig أو Freemarker). يقوم المحرك بمعالجة علامات القوالب (مثل `{{ ... }}`) كتعبير برمجي حي على الخادم، مما يتيح للمهاجم حقن دوال استكشاف كائنات بايثون (`__globals__`, `__mro__`) وقراءة الملفات الحساسة أو تشغيل أوامر النظام.

---

### السؤال الخامس البديل (Question 5 Alternate) · خطورة الحسابات الافتراضية (Default Credentials)
- **معرف السؤال:** `89e13b86-7a08-410e-89a1-03dbdeeb9fa9`
- **نص السؤال:**
  > What is the primary reason default credentials present such a widespread threat?
  > (ما هو السبب الرئيسي الذي يجعل الحسابات وكلمات المرور الافتراضية تمثل تهديداً واسع الانتشار؟)
- **الخيارات المتاحة:**
  - [x] **`They are publicly documented and attackers scan for them automatically` (لأنها موثقة علناً في أدلة المنتجات ويقوم المهاجمون بفحصها وأتمتة البحث عنها)**
  - [ ] `They disable firewalls and intrusion prevention systems on the host`
  - [ ] `They force the underlying service to run with elevated root privileges`
  - [ ] `They encrypt management traffic with an untrusted certificate`
- **الإجابة المعتمدة:** **`They are publicly documented and attackers scan for them automatically`**
- **التحليل التقني والتعليل الأمني (Root Cause Analysis):**
  ضمن تصنيف AS02 (Security Misconfigurations)، تأتي كلمات المرور الافتراضية للأجهزة والبرمجيات (مثل `admin:admin` أو `root:toor`) موثقة في أدلة الاستخدام الرسمية للمصنعين. يستخدم المهاجمون أدوات الفحص التلقائي (مثل Shodan و Masscan و Nmap scripts) لمسح الإنترنت بالكامل وتجربة هذه التركيبات ضد المنافذ المفتوحة تلقائياً وبلا أي تكلفة بحثية، مما يمنحهم وصولاً إدارياً فورياً دون الحاجة لتطوير استغلالات مخصصة.

---

### السؤال السادس (Question 6) · مطابقة أنماط الأكواد غير الآمنة بالحلول المعمارية (Matching Pairs)
- **نص السؤال (Dynamic Live Variant):**
  > Match each insecure coding pattern to the control that fixes it.
  > (طابق كل نمط برمجي غير آمن بضابط الحماية المعماري الذي يعالجه ويمنع الثغرة جذرياً).
- **جدول الأزواج المتطابقة المعتمدة (Verified Match Matrix):**

| النمط البرمجي غير الآمن (Insecure Coding Pattern) | ضابط الحماية المعماري الجذري (Remediation Control) | تصنيف OWASP 2025 |
| :--- | :--- | :---: |
| **اسم ملف قادم من استمارة يتم تمريره مباشرة لأمر صدفة النظام**<br>`Filename from a form passed into a shell command` | **استدعاء الملف التنفيذي مباشرة بمصفوفة من المعاملات دون مفسر الصدفة**<br>`Call the binary directly with an argument array` | **A05: Injection** |
| **تثبيت حزمة تحديث تلقائياً بمجرد تنزيلها من الإنترنت**<br>`Update package installed automatically after download` | **التحقق التشفيري الصارم من التوقيع الرقمي قبل التثبيت**<br>`Verify a cryptographic signature before installing` | **A08: Integrity Failures** |
| **بناء استعلام تسجيل الدخول بدمج اسم المستخدم نصياً**<br>`Login query built by concatenating the username string` | **استخدام الاستعلامات المجهزة بالمعاملات المقيدة (Prepared Statements)**<br>`Use prepared statements with bound parameters` | **A05: Injection** |
| **كتابة كلمات مرور المستخدمين في قاعدة البيانات بدون تجزئة**<br>`User passwords written to the database unhashed` | **تخزين كلمات المرور باستخدام دالة تجزئة بطيئة مثل Argon2**<br>`Store with a slow hash such as Argon2` | **A04: Crypto Failures** |

---

### السؤال السادس البديل (Question 6 Alternate) · الترتيب الزمني لاستهلاك التبعيات الخارجية بأمان (Dependency Consumption)
- **معرف السؤال:** `ec5f884a-9774-4b53-b3c9-0fa3debf47df`
- **نص السؤال:**
  > Order the steps to safely consume a new open-source dependency in your project.
  > (رتب الخطوات الزمنية لاستهلاك مكتبة أو تبعية مفتوحة المصدر جديدة بأمان داخل مشروعك).
- **الترتيب الزمني المعياري المعتمد (Correct Chronological Order):**
  1. **الخطوة الأولى:** مراجعة التحذيرات الأمنية المفتوحة للحزمة وسجل المطورين المسؤولين عنها (`Review open advisories for the package and its authors`).
  2. **الخطوة الثانية:** التحقق من التوقيع الرقمي للمطور أو بصمة الهاش التشفيرية للحزمة (`Verify the maintainer's digital signature or checksum`).
  3. **الخطوة الثالثة:** فحص التبعية بأدوات تحليل التكوين البرمجي لاكتشاف أي ثغرات معروفة (`Scan the dependency for known vulnerabilities`).
  4. **الخطوة الرابعة:** تثبيت إصدار الحزمة أو بصمتها في ملف التبعيات لمنع التحديثات التلقائية غير المدققة (`Pin the dependency to a specific version or hash`).

---

## 🎯 الخلاصة المعرفية والربط بالوحدة التالية (Synthesis & Module Bridge)

اختتمت هذه المراجعة التأسيسية لوحدة **OWASP Top 10 (2025)** ترسيخ المفاهيم المعمارية الكبرى:
1. **نموذج الثقة (Trust Model):** لا تثق أبداً ببيانات العميل، وافصل بشكل قاطع بين طبقة البيانات وطبقة التعليمات التنفيذية.
2. **التشفير الحقيقي (Real Cryptography):** التشفير ليس مجرد تشويش للنصوص، بل هو بروتوكول رياضي متكامل يتطلب إنتروبيا كافية وإدارة مركزية للمفاتيح (KMS).
3. **الجسر إلى الوحدة الحادية عشرة (Bridge to Module 11):**
   بعد دراسة كيفية تهاوي المصادقة وتخزين كلمات المرور غير المجزأة أو المشفرة بمفاتيح ضعيفة، تنتقل الرحلة في **الوحدة الحادية عشرة: هجمات كلمات المرور (Module 11: Password Attacks)** إلى التسلح بالأدوات والمنهجيات الهجومية المتقدمة لكسر التجزئات، وهجمات القوة الغاشمة عبر الشبكة بواسطة **Hydra**، وهندسة القوائم المخصصة، وتحديات **Checkmate**.
