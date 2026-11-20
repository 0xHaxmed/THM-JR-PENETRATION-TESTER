# الملاحظات الإضافية والتعمق التقني: أطر الويب الحديثة وثغراتها المعمارية (Modern Web Stacks)

---

### 1. التعمق المعماري في تلوث النموذج الأولي (Deep Dive: Prototype Pollution in Node.js V8)

#### النموذج الوراثي في جافا سكريبت (JavaScript Prototypal Inheritance)
في بيئة V8 و Node.js، لا تعتمد جافا سكريبت على الوراثة الكلاسيكية المعتمدة على الأصناف (Classes)، بل ترث الكائنات خصائصها من كائنات أخرى عبر رابط داخلي يسمى `[[Prototype]]`، والذي يمكن الوصول إليه وتعديله برمجياً عبر الخاصية العامة `__proto__`.

```text
userInstance ──▶ Object.prototype ──▶ null
     │                    │
     ▼                    ▼
 { name: "alice" }    { toString(), valueOf(), hasOwnProperty() }
```

عندما يستعلم محرك V8 عن خاصية داخل كائن (مثل `userInstance.isAdmin`)، يقوم بالخطوات التالية:
1. يبحث داخل الخصائص المباشرة الخاصة بالكائن نفسه (`Own Properties`).
2. إذا لم يعثر عليها، يصعد خطوة في سلسلة النماذج الأولية إلى `Object.prototype`.
3. إذا وجد الخاصية هناك، يرجع قيمتها؛ وإذا لم يجدها واستمر الصعود حتى وصل إلى `null`، يرجع `undefined`.

#### كيف تحدث ثغرة Prototype Pollution برمجياً؟
تحدث الثغرة في الدوال التكرارية المسؤولة عن دمج أو استنساخ الكائنات (مثل `merge` أو `clone` أو `extend` أو مكتبات قديمة مثل `lodash` و `extend`):

```javascript
// دالة دمج كائنات عرضة لتلويث النموذج الأولي
function insecureMerge(target, source) {
    for (let key in source) {
        if (typeof source[key] === 'object' && source[key] !== null) {
            if (!target[key]) target[key] = {};
            insecureMerge(target[key], source[key]);
        } else {
            target[key] = source[key];
        }
    }
    return target;
}
```

إذا أرسل المهاجم حمولة JSON تحتوي على:
```json
{
  "__proto__": {
    "isAdmin": true
  }
}
```
فإن `target["__proto__"]` يعود بمؤشر مباشر على `Object.prototype` العام. والسطر:
`target["__proto__"]["isAdmin"] = true;`
يجعل أي كائن موجود في كامل خادم Node.js يمتلك تلقائياً `isAdmin = true` عند الاستعلام!

#### الإجراءات الدفاعية الوقائية:
1. **تجميد النموذج الأولي عند الإقلاع:**
   ```javascript
   Object.freeze(Object.prototype);
   ```
2. **استخدام كائنات خالية من النموذج الأولي (Pure Dictionaries):**
   ```javascript
   const safeMap = Object.create(null);
   ```
3. **حظر المفاتيح المحجوزة:**
   ```javascript
   if (key === '__proto__' || key === 'constructor' || key === 'prototype') {
       continue;
   }
   ```
4. **استخدام البنية الحديثة `Map` بدلاً من الكائنات العادية لتخزين البيانات المفتاحية.**

---

### 2. معمارية Next.js App Router وتجاوز الـ Middleware (CVE-2025-29927)

#### التحول المعماري: Pages Router مقابل App Router
* في الإصدارات القديمة (Pages Router)، كان التوجيه يعتمد على نظام ملفات بسيط داخل مجلد `pages/` مع وظائف مثل `getServerSideProps` أو `getStaticProps`.
* في الإصدارات الحديثة (App Router المستند إلى React 18/19 Server Components)، يتم تقديم المكونات على الخادم ويتم بثها إلى العميل عبر بروتوكول نصي تدفقي يُعرف بـ **React Server Component (RSC) Flight Protocol**.

#### بصمة `window.__next_f`
عند تصفح أي موقع مبني بـ Next.js App Router، ستجد في الكود المصدري وسوم سكربت تحتوي على استدعاءات متكررة لـ `self.__next_f.push(...)`.
هذه المصفوفة وظيفتها تجميع قطع الـ HTML ورموز البيانات المهيأة على الخادم لترطيب شجرة مكونات React داخل المتصفح دون الحاجة إلى إعادة تنزيل كود الخادم البرمجي.

#### التشريح التقني لـ CVE-2025-29927
1. ملف `middleware.ts` في Next.js يعمل كبوابة (Gatekeeper) عند مستوى الحافة (Edge Runtime) أو خادم Node.js قبل توجيه الطلب إلى المكون الفعلي.
2. عند معالجة طلب معين، قد يحتاج الـ Middleware إلى تنفيذ تحويل مسار داخلي أو إعادة توجيه (Rewrite). لمنع حدوث حلقات توجيه لا نهائية (Infinite Loops)، يضيف محرك Next.js الداخلي ترويسة خاصة بالطلب الفرعي:
   `x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware`
3. في الإصدارات المصابة، إذا جاء الطلب من متصفح أو عميل خارجي يحمل هذه الترويسة، كان كود معالجة المسارات يفترض خطأً أن الطلب قد تم فصحه واعتماده من الـ Middleware داخلياً مسبقاً، فيقوم بتجاوز استدعاء `middleware.ts` كلياً!
4. **الترقيع:** قامت Vercel في التحديثات اللاحقة بتجريد ترويسة `x-middleware-subrequest` من أي طلب قادم من جهة العميل (Client Ingress Stripping) وتوليد رموز تحقق عشوائية داخلية لا يمكن تخمينها.

---

### 3. تحليل حقن SQL في جمل الترتيب (Django CVE-2021-35042)

#### لماذا تفشل الاستعلامات المهيأة (Prepared Statements) في جمل `ORDER BY`؟
في معايير بروتوكولات قواعد البيانات (مثل MySQL و PostgreSQL)، تدعم الاستعلامات المهيأة المتغيرات في شروط القيم فقط (مثل `WHERE id = ?` أو `VALUES (?)`).
لا تقبل محركات قواعد البيانات وضع علامات الاستفهام `?` في مواضع هيكل الاستعلام نفسه، مثل:
- أسماء الجداول (`FROM ?`)
- أسماء الأعمدة (`SELECT ? FROM`)
- أعمدة الترتيب والتجميع (`ORDER BY ?`, `GROUP BY ?`)
- اتجاه الترتيب (`ORDER BY id ?`)

نتيجة لذلك، يضطر المطورون إلى دمج اسم العمود نصياً. في إصدارات Django 3.1 و 3.2 السابقة للترقيع، سمحت دوال `order_by()` بمرور مدخلات غير مفحوصة بدقة، مما فتح الباب أمام حقن استعلامات فرعية.

#### تقنية XPath Error-Based عبر `updatexml()`
تأخذ دالة `updatexml()` ثلاث معاملات:
```sql
updatexml(xml_target, xpath_expr, new_xml)
```
عندما يتم تمرير تعبير XPath يبدأ بمحرف غير صالح (مثل `~` أو `0x7e`):
```sql
updatexml(1, concat(0x7e, (SELECT database())), 1)
```
يقوم محرك MySQL بوقف تنفيذ دالة التحديث وإرجاع خطأ صيغة (Syntax Error) يتضمن التعبير المرمز كاملاً:
```text
ERROR 1105 (HY000): XPATH syntax error: '~vuln_db'
```
إذا كان خادم الويب يطبع رسائل أخطاء SQL، تظهر النتيجة فوراً للمهاجم في المتصفح.

---

### 4. تشريح ثغرات Apache 2.4.49 و 2.4.50 (CVE-2021-41773 و CVE-2021-42013)

#### التوقيت غير الآمن لفك التشفير (Normalization vs. Decoding)
في كود C الخاص بخادم Apache 2.4.49، تم تعديل دالة `ap_normalize_path`:

```c
/* كود افتراضي يوضح الخلل المنطقي */
if (has_dot_dot_traversal(raw_path)) {
    return HTTP_BAD_REQUEST; // تم حظر ../
}
decode_percent_encoding(raw_path); // فك الترميز بعد التحقق!
```

* المهاجم يرسل: `.%2e/`
* دالة الفحص تفحص الحروف النصية المجردة: لا ترى سوى نقطة تليها `%` و `2` و `e`. فتعتبره مساراً آمناً وتمرره!
* بعد اجتياز الفحص، تأتي دالة فك التشفير فتحول `%2e` إلى `.`.
* يصبح المسار `../` وتتجاوز الآلية مجلد الموقع الافتراضي لتصل إلى جذر النظام `/`.

#### ثغرة المتابعة CVE-2021-42013:
عندما حاولت مؤسسة Apache ترقيع الثغرة في الإصدار `2.4.50`، قامت بإضافة حظر لـ `.%2e/`. ولكن المهاجمين استخدموا ترميزاً مزدوجاً (Double URL Encoding) مثل:
`%%32%65%%32%65/` أو `.%%32%65/`، مما أدى لظهور CVE-2021-42013 فوراً، ولم يتم حل المشكلة نهائياً إلا في إصدار `2.4.51`.

#### دور `mod_cgi` في تنفيذ الأوامر (RCE Mechanism):
إذا كان مجلد مخصص كمسار تنفيذي للـ CGI (مثل `/cgi-bin/` المعرف بـ `Options +ExecCGI`)، فإن خادم Apache لا يتعامل مع الملف المطلوب كملف ثابت للقراءة، بل يقوم بتشغيله كعملية مستقلة (Child Process) ويمرر له جسم طلب الـ HTTP (Body) عبر القناة القياسية `stdin`.
بذلك، فإن استدعاء `/cgi-bin/.%2e/.../bin/sh` مع طلب POST يجعل `sh` تقرأ الأوامر من جسم الطلب وتنفذها كأوامر شل مباشرة!

---

### 5. جدول البصمات السريعة لأشهر أطر الويب (Web Framework Fingerprinting Matrix)

| إطار العمل (Framework) | لغة البرمجة (Language) | الترويسات المميزة (Headers) | ملفات تعريف الارتباط (Cookies) | بصمات الكود المصدري والخطأ |
| :--- | :--- | :--- | :--- | :--- |
| **Express.js** | Node.js | `X-Powered-By: Express` | `connect.sid` | `<pre>Cannot GET /path</pre>` |
| **Next.js** | TypeScript/React | `X-Powered-By: Next.js`, `Vary: RSC...` | `__prerender_bypass` | `window.__next_f`, `/_next/static/` |
| **Django** | Python | `Server: WSGIServer/...` | `csrftoken`, `sessionid` | `name="csrfmiddlewaretoken"`, صفحة خطأ أصفر/رمادي |
| **Flask** | Python | `Server: Werkzeug/...` | `session` (موقعة بـ itsdangerous) | صفحة خطأ Werkzeug التفاعلية |
| **Ruby on Rails** | Ruby | `X-Powered-By: Phusion Passenger` | `_session_id` | `name="authenticity_token"` |
| **Laravel** | PHP | `X-Powered-By: PHP/...` | `laravel_session`, `XSRF-TOKEN` | صفحة خطأ Ignition الفاخرة |
| **Spring Boot** | Java | `Server: Apache-Coyote/...` | `JSESSIONID` | صفحة خطأ Whitelabel Error Page الافتراضية |
| **ASP.NET Core** | C# / .NET | `Server: Kestrel`, `X-Powered-By: ASP.NET` | `.AspNetCore.Antiforgery...` | `__RequestVerificationToken`, `/__browserLink` |
| **Apache HTTP** | C | `Server: Apache/2.4.x (Unix)` | — | صفحات خطأ 403/404 الكلاسيكية بالخط المائل |
| **Nginx** | C | `Server: nginx/1.x.x` | — | صفحة `404 Not Found` بالخط الكلاسيكي البسيط |
