# Task 04: Apache2 (خادم أباتشي وفهرسة الدلائل وتسريب إحصائيات mod_status والملفات غير المرتبطة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Apache HTTP Server (Apache2) is one of the most widely deployed open-source web servers in existence. While mature and stable, common configuration oversights frequently expose critical data and infrastructure details:

1. **Version and OS Disclosure:**
   By default on Debian/Ubuntu, Apache ships with `ServerTokens OS` and `ServerSignature On`, broadcasting the exact patch version and underlying Linux distribution in every response header and error page.
2. **Directory Listing (`Options +Indexes`):**
   When a directory lacks an index file (`index.html`, `index.php`), Apache checks the directory options. If `Indexes` is enabled (either globally or via an `.htaccess` file), Apache renders an automated listing (`Index of /directory`) exposing all internal files.
   - On port 80, navigating to `/files/` reveals an internal notes file: `internal-notes.txt`.
3. **Exposed Server Status (`mod_status`):**
   The `mod_status` module provides real-time server metrics, CPU usage, active worker threads, and recent client IP addresses and requested URLs. If configured with `Require all granted` rather than restricting access to `localhost`, this diagnostic page is exposed publicly at `/server-status`.
4. **Unlinked Backup Files:**
   Developers frequently create backup copies directly in the web root (e.g. `backup.bak`, `config.php.old`). Because these are not linked in any navigation menu, they must be uncovered using directory fuzzing tools like Gobuster.

#### الأسئلة المطروحة (Questions):
1. What Apache module exposes real-time server statistics at /server-status ? (Answer Format: module_name)
2. What is the flag found in the /files/ directory on Apache? (Answer Format: THM{...})

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المخاطر الاستخباراتية لـ `mod_status`:**
  * صفحة `/server-status` ليست مجرد صفحة إحصائية بسيطة؛ بل تعرض قائمة فورية بالطلبات الجارية (Current Requests)، والمسارات السرية التي يطلبها المستخدمون الحقيقيون، وعناوين IP للعملاء، مما يسمح للمهاجم بالتنصت على النشاط الإداري واكتشاف روابط عشوائية (Tokens/Session IDs) ممررة في الـ URL.
* **فهرسة المجلدات (`Options +Indexes`):**
  * مجلد `/files/` أو `/uploads/` بدون ملف `index.html` يسمح بتصفح كامل للملفات. حتى وإن لم يكن هناك رابط يقود إلى المجلد في الصفحة الرئيسية، فإن قواميس التخمين الشائعة ستعثر عليه فوراً.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * الوحدة البرمجية المسؤولة عن عرض حالة الخادم في أباتشي تتكون من كلمتين مدمجتين تبدأ بـ `mod_...`.
2. **التلميح الثاني (المستوى العملي):**
   * توجه إلى المسار `http://MACHINE_IP:80/files/` في المتصفح أو باستخدام curl لمعاينة قائمة الملفات المفهرسة.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * اقرأ محتوى الملف الداخلي `http://MACHINE_IP:80/files/internal-notes.txt` لاستخراج العلم.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: تحديد وحدة إحصائيات الخادم
```bash
# فحص مسار إحصائيات خادم أباتشي
curl -s http://MACHINE_IP:80/server-status | grep -i "Apache Server Status"
```
**المخرجات:**
```html
<h1>Apache Server Status for localhost (via 127.0.0.1)</h1>
<dl><dt>Server Version: Apache/2.4.58 (Ubuntu)</dt>...</dl>
```
* **اسم الوحدة البرمجية المسؤولة:** `mod_status`

---

#### الخطوة 2: فحص المجلد المفهرس `/files/`
```bash
# استعراض المجلد المفهرس عبر curl
curl -s http://MACHINE_IP:80/files/
```
**المخرجات:**
```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<html>
 <head>
  <title>Index of /files</title>
 </head>
 <body>
<h1>Index of /files</h1>
<ul><li><a href="/"> Parent Directory</a></li>
<li><a href="internal-notes.txt"> internal-notes.txt</a></li>
</ul>
</body></html>
```

---

#### الخطوة 3: قراءة ملف الملاحظات الداخلية واستخراج العلم
```bash
# قراءة الملف المستكشف
curl -s http://MACHINE_IP:80/files/internal-notes.txt
```
**المحتويات:**
```text
Internal System Maintenance Notes:
- Remember to rotate database passwords before deploying to production.
- Security audit flag: THM{apache_dir_listing}
- Ensure Indexes directive is disabled in production Apache configurations.
```
* **العلم المستخرج:** `THM{apache_dir_listing}`

---

#### الخطوة 4: التخمين الآلي للملفات غير المرتبطة (إثراء تقني)
```bash
# تشغيل Gobuster للبحث عن ملفات النسخ الاحتياطي
gobuster dir -u http://MACHINE_IP:80 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -x bak,txt,html -t 20
```
يكتشف ملف `/backup.bak` الذي يحتوي على بيانات دخول قواعد البيانات المسربة:
```text
user: dbadmin
pass: Backup2024!
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Apache Status Module):** `mod_status`
* **إجابة السؤال الثاني (Files Directory Flag):** `THM{apache_dir_listing}`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تأكيد اسم الوحدة البرمجية `mod_status` المسؤولة عن `/server-status`.
* تم تصفح المجلد `/files/` وقراءة `internal-notes.txt` واستخراج العلم بنجاح: `THM{apache_dir_listing}`.
* تم اعتماد الإجابات رسمياً على TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
في خوادم Apache، يجب دائماً ضبط الخيارات الآمنة: تعطيل فهرسة المجلدات باستخدام `Options -Indexes`، وإخفاء تفاصيل الإصدار عبر `ServerTokens Prod` و `ServerSignature Off`، وحظر الوصول إلى `/server-status` إلا من خلال واجهات التحكم المحلية المغلقة (`Require local`).
