# Task 12: Extra Mile Challenge (التحدي المتقدم: كسر المصادقة المحمية برمز CSRF عبر ماكرو الجلسات)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Now you face a realistic enterprise scenario: brute-forcing an administrative portal hardened with anti-automation defenses.
Navigate to the administrator login page:
`http://MACHINE_IP/admin/login/`

Upon inspecting the HTML source code and HTTP interaction during authentication attempts, you will observe two protective mechanisms:
1. **Dynamic Session Cookie:** Each request to `/admin/login/` issues or requires an active `session` cookie.
2. **Synchronizer CSRF Token:** The login form contains a hidden input field:
   ```html
   <input type="hidden" name="loginToken" value="38f9d0c2b1e4..." />
   ```
   The back-end server validates this token upon submission. Once a token is consumed or a login fails, the token is invalidated, meaning that static brute-forcing with Intruder will immediately fail with CSRF / Token Invalid errors!

#### The Enterprise Solution: Burp Macros & Session Handling Rules
To defeat single-use CSRF tokens and session invalidation during an Intruder attack, you must configure a **Burp Macro**:
1. **Create the Macro:**
   - In Burp Suite, navigate to **Settings -> Sessions -> Macros** (or `Project Options -> Sessions` in legacy versions).
   - Click **Add** to record a new macro.
   - Select a `GET /admin/login/` request from your Proxy history (or issue one to capture it).
   - Click **Configure item** on the request, and use the **Define custom parameter** tool to extract the dynamic value of `loginToken` from the response HTML.
2. **Configure Session Handling Rule:**
   - In **Settings -> Sessions -> Session Handling Rules**, click **Add**.
   - Under **Rule Actions**, add an action: **Run a macro**.
   - Select the Macro created above.
   - Configure the rule to update:
     - The parameter `loginToken` in the request body.
     - The `session` cookie in the request headers.
   - In the **Scope** tab of the rule:
     - Check **Include all URLs** (or restrict to your target IP).
     - Under **Tools Scope**, enable only **Intruder**.
3. **Execute the Attack:**
   - Send the POST `/admin/login/` request to Intruder (`Ctrl + I`).
   - In Positions: Select `Pitchfork` attack type, marking only `username=§test§&password=§test§` (leave `loginToken` as is; the Macro will dynamically overwrite it before each socket transmission).
   - Load `usernames.txt` into Set 1, and `passwords.txt` into Set 2.
   - Run the attack and identify the successful credentials.

#### الأسئلة المطروحة (Questions):
1. What username and password combination indicates a successful login attempt? The answer format is "username:password". (ما هو زوج اسم المستخدم وكلمة المرور الذي يشير إلى محاولة تسجيل دخول ناجحة؟ صيغة الإجابة هي "username:password".)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تشريح حماية رموز التحقق ضد التزوير (Anti-CSRF Tokens as Brute-Force Defense):**
  * بالرغم من أن الغرض الأساسي لرموز CSRF هو منع تزوير الطلبات عبر المواقع، إلا أن المطورين يعتمدون عليها غالباً كآلية دفاعية ثانوية ضد الأتمتة وهجمات التخمين.
  * إذا لم تقم بتحديث التوكن ديناميكياً مع كل طلب، سيرد الخادم فوراً برمز `403 Forbidden` أو `Invalid CSRF Token`.
  * تحل **Burp Macros** هذه المعضلة عبر إنشاء دورة حياة تلقائية: قبل أن يرسل Intruder طلب تسجيل الدخول، يقوم الماكرو أولاً في الخلفية بإرسال طلب `GET` إلى صفحة الدخول، ويقرأ التوكن الجديد ورمز الجلسة الجديد، ويحقنهما في طلب تسجيل الدخول التالي قبل إطلاقه على السلك (Over the wire).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * لا تضع علامات `§` حول قيمة `loginToken` في تبويب Positions، لأن قاعدة إدارة الجلسات ستتولى استبدالها تلقائياً.
2. **التلميح الثاني (المستوى العملي):**
   * تأكد من ضبط نطاق (Scope) قاعدة الجلسات ليشمل أداة **Intruder**.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * عند تشغيل هجوم Pitchfork باستخدام القائمتين، رتب النتائج حسب عمود Length. ستجد استجابة فريدة بطول مختلف تنتمي للمستخدم `o.bennett` وكلمة المرور `bella1`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### المخطط الهيكلي لعمل الماكرو في الخلفية (Burp Macro Pipeline):
```text
[ Intruder prepares Request (o.bennett / bella1) ]
                        │
                        ▼  (Intercepted by Session Handling Rule)
[ Burp executes Macro: GET /admin/login/ ]
                        │
                        ▼
[ Server returns new session cookie + fresh loginToken ]
                        │
                        ▼
[ Rule injects new session & loginToken into Intruder Request ]
                        │
                        ▼
[ Intruder sends finalized POST request to /admin/login/ ]
                        │
                        ▼
[ Server validates token successfully & processes credentials ]
```

#### تفاصيل الإعداد خطوة بخطوة:
1. **إنشاء الماكرو (Macro Configuration):**
   * انتقل إلى: `Settings` ⟶ `Sessions` ⟶ `Macros` ⟶ `Add`.
   * اختر طلب `GET /admin/login/` ثم انقر `OK`.
   * اختر `Configure item`: في قسم `Custom parameter locations in response`، انقر `Add`.
   * اسم المعامل: `loginToken`.
   * ظلل قيمة التوكن في استجابة الخادم لضبط محددات البداية والنهاية تلقائياً:
     `Start after expression: name="loginToken" value="`
     `End at delimiter: "`
2. **إنشاء قاعدة التعامل مع الجلسات (Session Handling Rule):**
   * انتقل إلى: `Settings` ⟶ `Sessions` ⟶ `Session Handling Rules` ⟶ `Add`.
   * في تبويب `Details` ⟶ `Rule Actions` ⟶ انقر `Add` ⟶ اختر `Run a macro`.
   * اختر الماكرو الذي تم إنشاؤه أعلاه.
   * اختر: `Update only the following parameters: loginToken`.
   * اختر: `Update current request cookies`.
   * في تبويب `Scope`: حدد أداة `Intruder` فقط وضع النطاق المناسب.
3. **تنفيذ الهجوم عبر Intruder:**
   * قالب الطلب في Positions:
     ```http
     POST /admin/login/ HTTP/1.1
     Host: 10.10.123.45
     Content-Type: application/x-www-form-urlencoded
     Cookie: session=CURRENT_SESSION
     Connection: close

     username=§test§&password=§test§&loginToken=PLACEHOLDER
     ```
   * نوع الهجوم: `Pitchfork`.
   * الحمولات: `usernames.txt` و `passwords.txt`.

#### تحليل نتيجة الهجوم:
* يظهر الطلب الخاص ببيانات الاعتماد التالية بطول مختلف تماماً ناتج عن إعادة التوجيه إلى لوحة الإدارة (`/admin/dashboard`):
  * اسم المستخدم: `o.bennett`
  * كلمة المرور: `bella1`

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول (زوج بيانات الاعتماد الناجح):**
  `o.bennett:bella1`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
1. افتح المتصفح وتوجه إلى `http://MACHINE_IP/admin/login/`.
2. أدخل بيانات الاعتماد:
   - المستخدم: `o.bennett`
   - كلمة المرور: `bella1`
3. يتم تجاوز حماية التوكن بنجاح والدخول إلى لوحة الإدارة الرئيسية، مما يؤكد صحة الزوج في منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* رموز الحماية ضد التزوير (CSRF Tokens) لا توفر حماية حقيقية ضد التخمين إذا لم تقترن بقيود معدل الطلبات وحظر المحاولات الفاشلة (Account Lockout & Rate Limiting). وتثبت وحدات الماكرو وقواعد إدارة الجلسات في Burp Suite قدرتها على تفكيك هذه الحمايات وأتمتة تجاوزها بسلاسة.
