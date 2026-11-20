# Task 06: IIS Misconfigurations (أخطاء التهيئة الشائعة في IIS وتسريب الملفات ومسار trace.axd)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Beyond binary exploits and WebDAV shell uploads, IIS servers are frequently plagued by operational and configuration oversights that expose sensitive infrastructure components, source code, and user sessions:

1. **Directory Listing Enabled on Sensitive Folders:**
   When directory browsing is left enabled on asset or upload directories (such as `http://MACHINE_IP/uploads/`), an attacker can directly view internal files, staging backups, and deployment artifacts.
   - Navigating to `/uploads/` exposes files ending in sensitive extensions such as **`.bak`** (e.g. `config.bak`).
2. **`trace.axd` Diagnostic Handler Left Enabled:**
   ASP.NET provides an internal tracing handler configured via `<trace enabled="true" requestLimit="50" pageOutput="false" localOnly="false"/>` in `web.config`. When enabled and exposed to external networks, navigating to **`trace.axd`** presents an interface logging the most recent 50 HTTP requests received by the application!
   This includes raw HTTP request headers, session cookies (`ASP.NET_SessionId`), authorization tokens, sensitive POST form data (passwords, credit card numbers), and internal server execution times.
3. **`web.config` Leakage:**
   The `web.config` XML file is the central repository for IIS database connection strings, machine keys, and app secrets. If request filtering rules are accidentally altered or files are renamed to `web.config.bak`, attackers can download the plaintext credentials.
4. **Verbose Custom Errors (`customErrors mode="Off"`):**
   When errors are unhandled, the server displays complete .NET exception traces, revealing backend source file paths (`C:\inetpub\wwwroot\...`), code line numbers, and database schema queries.
5. **HTTP TRACE Method Enabled:**
   Allows Cross-Site Tracing (XST) where malicious scripts reflect cookies and authentication headers.

#### الأسئلة المطروحة (Questions):
1. Navigate to the /uploads/ directory on the target. What sensitive file extension is visible in the directory listing? (Answer Format: include the dot, e.g., .ext)
2. What ASP.NET diagnostic handler URL path should be disabled in production as it can expose session data, cookies, and request history? (Answer Format: filename.ext)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الأثر الكارثي لمعالج التشخيص `trace.axd`:**
  * يمثل `trace.axd` منجماً استخباراتياً لأي مختبر اختراق؛ فبمجرد فتحه، يمكنك رؤية كعكات الجلسات النشطة للمستخدمين والمشرفين (Session Hijacking) دون الحاجة لتنفيذ أي هجمات XSS أو تنصت على الشبكة، مما يتيح انتحال شخصية مدير النظام فوراً.
* **خطورة امتدادات النسخ الاحتياطي (`.bak`, `.old`):**
  * يعتمد IIS على قائمة معالجات الطلبات (Handler Mappings) لتحديد كيفية معالجة كل امتداد. فعندما يرى `.aspx`، يمرره لمحرك الـ CLR لتنفيذه؛ ولكن عندما يرى `.bak` (مثل `web.config.bak`)، فإنه لا يعرف له معالجاً خاصاً فيقوم بإرساله كملف نصي عادي (`application/octet-stream`)، مسرباً الكود المصدري وكلمات المرور المشفرة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * افتح المتصفح أو اطلب الرابط `http://MACHINE_IP/uploads/` لمعاينة قائمة الملفات المفهرسة.
2. **التلميح الثاني (المستوى العملي):**
   * ابحث عن امتداد الملف الاحتياطي المعروض في قائمة الملفات واكتبه متضمناً النقطة.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * اسم معالج التتبع في ASP.NET هو `trace.axd`. والامتداد الحساس هو `.bak`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: فحص المجلد المفهرس `/uploads/`
```bash
# استعراض الملفات داخل مجلد /uploads/
curl -s http://MACHINE_IP/uploads/
```
**المخرجات المستلمة:**
```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<html><head><title>Index of /uploads</title></head><body>
<h1>Index of /uploads</h1>
<ul>
<li><a href="avatar1.png">avatar1.png</a></li>
<li><a href="config.bak">config.bak</a></li>
<li><a href="test_upload.pdf">test_upload.pdf</a></li>
</ul>
</body></html>
```
* **الامتداد الحساس الظاهر في الفهرسة:** **`.bak`**

---

#### الخطوة 2: التحقق من مسار معالج التتبع `trace.axd`
```bash
# إرسال طلب للتحقق من وجود مسار معالج التتبع
curl -s -I http://MACHINE_IP/trace.axd
```
**المخرجات:**
```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
```
* يعيد الخادم كود 200 OK، مما يؤكد أن معالج التتبع مفعل ويعرض سجلات الجلسات الحية.
* **اسم المسار التشخيصي:** **`trace.axd`**

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Sensitive File Extension):** `.bak`
* **إجابة السؤال الثاني (ASP.NET Diagnostic Handler Path):** `trace.axd`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم استعراض مجلد `/uploads/` ورصد ملف `config.bak` وتأكيد الامتداد `.bak`.
* تم تأكيد مسار التشخيص `trace.axd` وتأثيره الأمني الحرج.
* تم اعتماد الإجابات رسمياً على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
يجب تعطيل معالج `trace.axd` كلياً في بيئات الإنتاج عبر ضبط `<trace enabled="false" />` أو حذفه من ملف `web.config`، مع التأكد من إيقاف فهرسة المجلدات (`Directory Browsing: Disabled`)، وإضافة قواعد تصفية للطلبات (Request Filtering) لحظر تنزيل الامتدادات الحساسة مثل `.bak` و `.config` و `.old`.
