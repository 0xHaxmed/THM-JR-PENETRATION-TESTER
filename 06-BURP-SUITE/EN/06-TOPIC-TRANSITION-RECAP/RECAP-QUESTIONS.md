# أسئلة التقييم ومراجعة التحول لموديول بيرب سويت (Burp Suite Topic Transition Recap Questions)

يقدم هذا الملف توثيقاً شاملاً وتفصيلياً لكافة الأسئلة الستة التقييمية الخاصة بمراجعة موديول **Burp Suite** (النقاط المحققة: 48 / 48 بنسبة نجاح 100%)، مع تفكيك خيارات الإجابة، الشروحات التقنية، وملاحظات المنصة الرسمية.

---

### السؤال الأول (Question 1)
* **نوع السؤال:** اختيار فردي (`single-choice`)
* **النقاط:** 8 نقاط
* **نص السؤال باللغة الإنجليزية:**
  > "Why must the PortSwigger CA certificate be imported into the browser before proxying HTTPS traffic?"
* **الترجمة العربية للسؤال:**
  > "لماذا يجب استيراد وتثبيت شهادة المرجع المصدق (CA) الخاصة بـ PortSwigger في المتصفح قبل توجيه حركة مرور HTTPS عبر الوكيل؟"

#### خيارات الإجابة وتحليلها:
1. `It enables the proxy to log requests as well as intercept them`
   * *التقييم:* خاطئ. تسجيل الحزم (Logging) يحدث لجميع البروتوكولات بما فيها HTTP الصريح ولا يعتمد على وجود الشهادة.
2. `It stops the target server from detecting the proxy is in use`
   * *التقييم:* خاطئ. شهادة PortSwigger يراها متصفحك المحلي فقط ولا ترسل إلى الخادم البعيد أبداً.
3. **The browser rejects the proxy's certificate as untrusted without it** (★ **الإجابة الصحيحة المعتمدة**)
   * *التقييم:* صحيح. يقوم Burp Suite بتوليد شهادات TLS ديناميكية لكل موقع يتصفحه المستخدم لتفكيك التشفير، وإذا لم يثق المتصفح في السلطة الجذرية المصدرة لها، سيطلق تحذيراً أمنياً ويمنع الاتصال فوراً.
4. `It authenticates the tester to the remote target web server`
   * *التقييم:* خاطئ. الشهادة تعرف الوكيل للمتصفح المحلي وليست بيانات اعتماد للتوثيق لدى الخادم.
5. `It encrypts traffic between the browser and the proxy for the first time`
   * *التقييم:* خاطئ. الحركة مشفرة بالفعل عبر TLS، والمشكلة تكمن في هوية المرجع الموقع وليس في وجود التشفير من عدمه.

* **الإجابة النهائية المعتمدة:**
  `The browser rejects the proxy's certificate as untrusted without it`

---

### السؤال الثاني (Question 2)
* **نوع السؤال:** اختيار فردي (`single-choice`)
* **النقاط:** 8 نقاط
* **نص السؤال باللغة الإنجليزية:**
  > "What is the main purpose of extensions in Burp Suite?"
* **الترجمة العربية للسؤال:**
  > "ما هو الهدف الأساسي من نظام الإضافات (Extensions) في أداة Burp Suite؟"

#### خيارات الإجابة وتحليلها:
1. `To convert Burp into a network vulnerability scanner`
   * *التقييم:* خاطئ. يظل Burp أداة متخصصة في أمان تطبيقات الويب ولا يتحول لماسح شبكات مثل Nessus.
2. **To add functionality beyond the built-in tools** (★ **الإجابة الصحيحة المعتمدة**)
   * *التقييم:* صحيح. الهدف الرئيسي للإضافات هو ربط وظائف جديدة توسع إمكانيات الأدوات المدمجة، بدءاً من اختبارات فحص جديدة وحتى بناء تبويبات رسومية كاملة.
3. `To reduce the memory footprint of the proxy`
   * *التقييم:* خاطئ. الإضافات تضيف أكواداً برمجية إلى ذاكرة JVM مما يزيد من استهلاك الموارد ولا يقلله.
4. `To replace the need for a browser proxy configuration`
   * *التقييم:* خاطئ. تظل حركة المرور بحاجة للتوجيه عبر منفذ الوكيل الاستماعي.
5. `To store credentials for authenticated testing`
   * *التقييم:* خاطئ. إدارة بيانات الاعتماد تخص إعدادات الجلسات وليست الغاية المعمارية لنظام الإضافات.

* **الإجابة النهائية المعتمدة:**
  `To add functionality beyond the built-in tools`

---

### السؤال الثالث (Question 3)
* **نوع السؤال:** إعادة ترتيب تسلسلي (`re-order`)
* **النقاط:** 8 نقاط
* **نص السؤال باللغة الإنجليزية:**
  > "Order the steps to diff a failed and a successful login response in Burp Suite Comparer."
* **الترجمة العربية للسؤال:**
  > "رتب الخطوات لمقارنة الفروق بين استجابة تسجيل دخول فاشلة وناجحة في أداة المقارنة Burp Suite Comparer."

#### الترتيب المنطقي الصحيح والتحليل المنهجي:
1. **Capture the login POST with bad credentials in Proxy**
   * *التعليل:* التقاط طلب تسجيل الدخول الأصلي ببيانات خاطئة في الوكيل هو الأثر الأولي الخام الذي تبنى عليه كل الخطوات اللاحقة.
2. **Send that captured request to Repeater and issue it**
   * *التعليل:* يتم نقل الطلب الملتقط إلى Repeater لإرساله واستقبال استجابة الفشل الأولى.
3. **Send the resulting failure response to Comparer**
   * *التعليل:* بمجرد استلام استجابة الفشل، يتم نقلها إلى Comparer لتكون مجموعة البيانات الأولى للمقارنة.
4. **Replace the credentials in Repeater, resend, compare**
   * *التعليل:* استبدال بيانات الاعتماد في Repeater بالبيانات الصحيحة وإعادة الإرسال للحصول على الاستجابة الثانية ثم تنفيذ المقارنة البصرية بينهما في Comparer.

---

### السؤال الرابع (Question 4)
* **نوع السؤال:** مطابقة أزواج (`match-pair`)
* **النقاط:** 8 نقاط
* **نص السؤال باللغة الإنجليزية:**
  > "Match each Repeater response view to what it displays."
* **الترجمة العربية للسؤال:**
  > "طابق كل نمط عرض في استجابة أداة Repeater مع ما يقوم بعرضه."

#### الأزواج المتطابقة المعتمدة:
1. **Pretty**  `Response text with light readability formatting`
   * *الشرح:* النمط الافتراضي الذي يضيف تلوين الشفرة البرمجية وتنسيقاً خفيفاً لتسهيل قراءة JSON و HTML.
2. **Render**  `Page drawn graphically as a browser would show it`
   * *الشرح:* محرك التصيير الرسومي الذي يرسم صفحة الويب كما يراها المستخدم داخل متصفح حقيقي.
3. **Hex**  `Byte-level representation, useful for binaries`
   * *الشرح:* عرض ثنائي ست عشري على مستوى البايت، بالغ الأهمية لفحص الملفات الثنائية وتواقيع الملفات السحرية.
4. **Raw**  `Unmodified server output, no formatting applied`
   * *الشرح:* مخرجات الخادم الخام الحرفية دون أي معالجة أو تنسيق، أساسية للتحقق من الترويسات الدقيقة وأكواد CRLF.

---

### السؤال الخامس (Question 5)
* **نوع السؤال:** إعادة ترتيب تسلسلي (`re-order`)
* **النقاط:** 8 نقاط
* **نص السؤال باللغة الإنجليزية:**
  > "Order the steps to get a Python extension running and inspect what it reports."
* **الترجمة العربية للسؤال:**
  > "رتب الخطوات لتشغيل إضافة بايثون وفحص المخرجات التي تصدرها داخل Burp Suite."

#### الترتيب المنطقي الصحيح والتحليل المنهجي:
1. **Download the Jython standalone JAR to disk**
   * *التعليل:* تنزيل مفسر جيثون المستقل (Jython Standalone JAR) هو المتطلب الأساسي الأول لتشغيل بيئة بايثون داخل JVM.
2. **Set the JAR path in the Python environment settings**
   * *التعليل:* توجيه Burp إلى مسار ملف الـ JAR المحفوظ عبر إعدادات بيئة بايثون (`Python environment`).
3. **Load the .py extension file using Add**
   * *التعليل:* تحميل ملف الإضافة المكتوبة بلغة بايثون (`.py`) عبر زر Add في واجهة الإضافات.
4. **Read the module's messages in the Output pane**
   * *التعليل:* قراءة رسائل التشغيل ومخرجات الإضافة في لوحة المخرجات (`Output pane`) بعد اكتمال التحميل.

---

### السؤال السادس (Question 6)
* **نوع السؤال:** اختيار فردي (`single-choice`)
* **النقاط:** 8 نقاط
* **نص السؤال باللغة الإنجليزية:**
  > "Which section shows a selected extension's name, version, and description?"
* **الترجمة العربية للسؤال:**
  > "أي قسم يعرض اسم الإضافة المحددة وإصدارها والوصف الخاص بها؟"

#### خيارات الإجابة وتحليلها:
1. `APIs`
   * *التقييم:* خاطئ. تبويب APIs يسرد الواجهات البرمجية للمطورين ولا يعرض بيانات وصفية عن إضافة محددة.
2. **Details** (★ **الإجابة الصحيحة المعتمدة**)
   * *التقييم:* صحيح. قسم **Details** في اللوحة السفلية لواجهة Extensions مخصص لعرض البيانات الوصفية (Metadata) للإضافة المحددة حالياً بما في ذلك الاسم والإصدار والمؤلف والوصف.
3. `Output`
   * *التقييم:* خاطئ. لوحة Output تعرض النصوص والمخرجات التي يطبعها السكربت أثناء التنفيذ (`stdout`).
4. `Errors`
   * *التقييم:* خاطئ. لوحة Errors مخصصة لرسائل الخطأ وتتبع الاستثناءات (`stderr`).
5. `Python environment`
   * *التقييم:* خاطئ. هذا القسم مخصص لتحديد مسار مفسر جيثون فقط.

* **الإجابة النهائية المعتمدة:**
  `Details`
