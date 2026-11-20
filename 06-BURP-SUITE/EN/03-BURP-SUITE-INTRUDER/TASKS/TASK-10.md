# Task 10: Practical Example (المثال التطبيقي: حشو بيانات الاعتماد واكتشاف الشذوذ في الاستجابة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Now it is time to put theory into practice by executing an automated credential stuffing attack against the support portal login page located at:
`http://MACHINE_IP/support/login`

A wordlist archive containing compromised credentials has been hosted on the target machine. Download and extract it:
```bash
wget http://MACHINE_IP:9999/Credentials/BastionHostingCreds.zip
unzip BastionHostingCreds.zip
```
The archive provides:
- `usernames.txt`
- `passwords.txt`
- `emails.txt`
- `combined.txt`

#### Attack Execution Instructions:
1. Navigate to `http://MACHINE_IP/support/login` in your browser via Burp Proxy.
2. Submit a dummy login attempt (e.g., `username=test&password=test`).
3. Locate the POST request in **Proxy -> HTTP history** and send it to **Intruder** (`Ctrl + I`).
4. In the **Positions** tab:
   - Click **Clear §** to eliminate unwanted default markers.
   - Highlight the dummy username value and click **Add §**.
   - Highlight the dummy password value and click **Add §**.
   - Set the **Attack type** dropdown to **Pitchfork**.
5. In the **Payloads** tab:
   - For **Payload set 1** (Usernames): Load `usernames.txt`.
   - For **Payload set 2** (Passwords): Load `passwords.txt`.
6. Click **Start Attack**.
7. In the Attack Results window, examine the HTTP Status Code and Length columns:
   - Notice that every failed request returns a `302 Found` redirection back to the login page with an error parameter (resulting in a consistent response length, e.g., 272 bytes).
   - Look for an anomalous response length that deviates from the baseline failed attempts.
   - Inspect the anomalous request/response to identify the valid credentials.

#### الأسئلة المطروحة (Questions):
1. What username and password combination indicates a successful login attempt? The answer format is "username:password". (ما هو زوج اسم المستخدم وكلمة المرور الذي يشير إلى محاولة تسجيل دخول ناجحة؟ صيغة الإجابة هي "username:password".)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **استراتيجية فرز النتائج واكتشاف الشذوذ (Response Anomaly Detection):**
  * في كثير من تطبيقات الويب الحديثة، لا يرجع تسجيل الدخول الفاشل كود 401 أو 200، بل يرجع كود `302 Found` لإعادة توجيه المستخدم إلى صفحة تسجيل الدخول مصحوباً برسالة خطأ مثل:
    `Location: /support/login?error=invalid_credentials`
  * بينما تسجيل الدخول الناجح يعيد التوجيه برمز `302 Found` أيضاً، ولكن إلى لوحة التحكم:
    `Location: /support/dashboard`
  * هذا الاختلاف في رابط الوجهة (Location Header) ينعكس فوراً على **طول الاستجابة (Length)**!
  * لذلك، لا تبحث فقط عن أكواد الحالة، بل قم دائماً بفرز جدول النتائج حسب عمود **Length** لرصد القيم الشاذة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * تأكد من اختيار نمط **Pitchfork** لتحميل قائمتي أسماء المستخدمين وكلمات المرور بالتوازي.
2. **التلميح الثاني (المستوى العملي):**
   * بعد اكتمال الهجوم، انقر على رأس عمود **Length** في نافذة النتائج لترتيب الطلبات تصاعدياً أو تنازلياً.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * ستجد طلباً بطول مختلف تماماً عن بقية الطلبات (حوالي 240 بايت بدلاً من 272 بايت)، وبيانات اعتماده هي المستخدم `m.rivera` وكلمة المرور `letmein1`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### 1. استخراج القواميس المعملية:
```bash
wget http://MACHINE_IP:9999/Credentials/BastionHostingCreds.zip
unzip BastionHostingCreds.zip
# القوائم المستخرجة: usernames.txt (100 سطر)، passwords.txt (100 سطر)
```

#### 2. ضبط موضع الحقن في تبويب Positions:
```http
POST /support/login HTTP/1.1
Host: 10.10.123.45
Content-Type: application/x-www-form-urlencoded
Content-Length: 35
Connection: close

username=§test§&password=§test§
```
*نوع الهجوم المختار:* `Pitchfork`

#### 3. ضبط مجموعات الحمولات (Payloads Configuration):
- **Set 1 (Username):** تحميل `usernames.txt`.
- **Set 2 (Password):** تحميل `passwords.txt`.

#### 4. تحليل جدول نتائج الهجوم (Attack Results):
| رقم الطلب (Req #) | الحمولات (Payload 1 / Payload 2) | كود الحالة (Status) | طول الاستجابة (Length) | ملاحظات الفحص |
| :---: | :--- | :---: | :---: | :--- |
| 0 | baseline (test / test) | 302 | 272 | فشل: إعادة توجيه مع رسالة خطأ |
| 1 | j.smith / password123 | 302 | 272 | فشل |
| ... | ... | 302 | 272 | فشل متكرر |
| **47** | **m.rivera / letmein1** | **302** | **238** | **نجاح: إعادة توجيه إلى /support/tickets** |
| 48 | a.johnson / admin2026 | 302 | 272 | فشل |

#### فحص ترويسة الاستجابة للطلب الناجح (Req 47):
```http
HTTP/1.1 302 Found
Location: /support/tickets
Set-Cookie: support_session=s%3A7a8f9c0e...; Path=/; HttpOnly
Content-Length: 238
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول (اسم المستخدم وكلمة المرور):**
  `m.rivera:letmein1`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
1. افتح المتصفح وتوجه إلى `http://MACHINE_IP/support/login`.
2. أدخل اسم المستخدم: `m.rivera`.
3. أدخل كلمة المرور: `letmein1`.
4. يتم تسجيل الدخول بنجاح وتوجيهك إلى بوابة الدعم الفني، مما يثبت صحة الزوج بنسبة 100%.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* لا تكفي أكواد الحالة (HTTP Status Codes) وحدها للحكم على نجاح الهجمات. في كثير من سيناريوهات إعادة التوجيه 302، يكون التغير في **طول الاستجابة (Response Length)** هو الدليل القاطع لاكتشاف بيانات الاعتماد الصحيحة.
