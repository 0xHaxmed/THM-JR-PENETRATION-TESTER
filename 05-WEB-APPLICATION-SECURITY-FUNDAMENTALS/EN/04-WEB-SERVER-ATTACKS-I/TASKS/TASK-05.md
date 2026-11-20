# Task 05: Node.js Express (خادم Node.js ونقاط التصحيح المكشوفة وتسريب متغيرات البيئة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Node.js with the Express framework powers modern APIs, Single-Page Application (SPA) backends, and microservices. Unlike traditional web servers that map requests directly to static filesystem paths, Express processes requests through a flexible middleware pipeline.

However, development-oriented features left in production introduce critical security weaknesses:
1. **Framework Fingerprinting:**
   By default, Express adds the `X-Powered-By: Express` header to all outgoing responses.
2. **Verbose Error Messages & Stack Traces:**
   When an application crashes or an unhandled exception occurs (e.g. requesting `/api/users`), custom or default error handlers may leak full stack traces, server file paths (`/home/node/app/...`), and raw database queries directly to the client.
3. **Exposed Debug Endpoints:**
   Developers frequently build internal diagnostic endpoints during development:
   - `/api/routes`: Reads `app._router.stack` to dump every registered URL path and HTTP method in the application.
   - `/api/debug/env`: Dumps the entire Node.js `process.env` object, exposing database credentials, API secrets, and server configuration settings.
4. **Static File Assets (`express.static`):**
   Serving static directories often leaks frontend configuration scripts (`/static/config.js`) containing internal API endpoints, secret tokens, or test flags. (Note: `express.static()` blocks dotfiles by default using `dotfiles: 'ignore'`).

#### الأسئلة المطروحة (Questions):
1. What value does the NODE_ENV variable have in the debug endpoint response? (Answer Format: environment_name)
2. What is the flag found in the static files served by the Node.js application? (Answer Format: THM{...})

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **خطورة تسريب `process.env` في بيئات Node.js:**
  * كائن `process.env` في Node.js يحتوي على جميع المتغيرات الممررة إلى الحاوية أو السيرفر، بما في ذلك مفاتيح AWS، ومفاتيح Stripe، وكلمات مرور قواعد البيانات، وقيم التشفير السرية (JWT Secrets).
  * كشف هذا الكائن عبر نقطة فحص تصحيحية مثل `/api/debug/env` يمنح المهاجم مفاتيح المملكة بأكملها بطلب واحد دون الحاجة لأي ثغرة استغلال معقدة.
* **البحث في ملفات التكوين الثابتة (`/static/config.js`):**
  * يقوم مطورو الواجهات الأمامية عادة بوضع إعدادات الاتصال في ملفات جافا سكريبت ثابتة يتم تحميلها في المتصفح. فحص هذه الملفات يكشف مسارات واجهات برمجة التطبيقات الداخلية والبيئات غير الموثقة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * اطلب نقطة فحص البيئة على المنفذ 3000: `curl -s http://MACHINE_IP:3000/api/debug/env`.
2. **التلميح الثاني (المستوى العملي):**
   * ابحث داخل الـ JSON الناتج عن متغير البيئة `NODE_ENV` واستخرج قيمته النصية.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * استعرض مسار الملفات الثابتة عبر طلب `http://MACHINE_IP:3000/static/config.js` واستخرج العلم من كود الجافا سكريبت.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: فحص مسار المسارات `/api/routes` ومسار البيئة `/api/debug/env`
```bash
# 1. استعراض المسارات المتاحة في تطبيق Express
curl -s http://MACHINE_IP:3000/api/routes | jq .

# 2. استعراض متغيرات البيئة المسربة عبر /api/debug/env
curl -s http://MACHINE_IP:3000/api/debug/env | jq .
```
**المخرجات المستلمة (JSON Snippet):**
```json
{
  "NODE_ENV": "development",
  "PORT": "3000",
  "DB_HOST": "localhost",
  "DB_USER": "node_app",
  "DB_PASSWORD": "NodeDBPass2024!",
  "API_SECRET": "c7a82b9e14f0..."
}
```
* **قيمة متغير `NODE_ENV` المستخرجة:** `development`

---

#### الخطوة 2: فحص ملف التكوين الثابت `/static/config.js`
```bash
# جلب محتويات ملف التكوين الثابت
curl -s http://MACHINE_IP:3000/static/config.js
```
**المحتويات:**
```javascript
const CONFIG = {
  apiBaseUrl: "http://api.company-portal.thm:3000",
  environment: "development",
  debugMode: true,
  internalFlag: "THM{node_debug_exposed}",
  version: "1.2.0"
};
```
* **العلم المستخرج:** `THM{node_debug_exposed}`

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (NODE_ENV Value):** `development`
* **إجابة السؤال الثاني (Static Files Flag):** `THM{node_debug_exposed}`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم استخراج قيمة `development` من نقطة فحص البيئة `/api/debug/env`.
* تم فحص ملف `/static/config.js` واستخراج العلم بنجاح: `THM{node_debug_exposed}`.
* تم اعتماد الإجابات رسمياً على TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
في بيئات الإنتاج، يجب ضبط `NODE_ENV=production` دائماً لتعطيل صفحات الخطأ المفصلة، وإزالة كافة مسارات التصحيح الداخلية (`/debug/*`) قبل مرحلة البناء، وتجريد ترويسة `X-Powered-By` باستخدام حزمة `helmet` البرمجية.
