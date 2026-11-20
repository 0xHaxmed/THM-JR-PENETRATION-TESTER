# فهرس مهام معمل: ما بعد فحص المنافذ ومحرك السكربتات (Nmap Post Port Scans - TASKS.md)

* **الغرفة:** Nmap Post Port Scans
* **المسار:** Module 04: Nmap — الغرفة 04 (`nmap04`)
* **رابط المعمل:** `https://tryhackme.com/room/nmap04`
* **إجمالي المهام:** 6 مهام مكتملة وموثقة بنسبة 100%

---

### 📑 جدول ملخص المهام والحلول المعتمدة (Tasks & Answers Summary)

| رقم المهمة | عنوان المهمة | موضوع الفحص والتقنية | السؤال | الإجابة المعتمدة | ملف التوثيق المفصل |
|---|---|---|---|---|---|
| **01** | Introduction | مدخل ومفاهيم ما بعد الفحص | Started machine successfully | `No answer needed` | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/04-NMAP-POST-PORT-SCANS/TASKS/TASK-01.md) |
| **02** | Service Detection | كشف الخدمات والإصدارات ومستويات الشدة | 1. Detected version for port 143?<br>2. Service without version under --version-light? | `Dovecot imapd`<br>`rpcbind` | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/04-NMAP-POST-PORT-SCANS/TASKS/TASK-02.md) |
| **03** | OS Detection and Traceroute | بصمة نظام التشغيل وتتبع المسار | Closest OS match per Nmap (-O)? | `Linux` | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/04-NMAP-POST-PORT-SCANS/TASKS/TASK-03.md) |
| **04** | Nmap Scripting Engine (NSE) | أتمتة الفحص الأمني بلغة Lua | 1. What http-robots.txt checks for?<br>2. Script name for MS15-034 (CVE-2015-1635)?<br>3. http-title value on port 80?<br>4. SSH server host key relying on SHA2-512? | `disallowed entries`<br>`http-vuln-cve2015-1635`<br>`Welcome to nginx on Debian!`<br>`rsa-sha2-512` | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/04-NMAP-POST-PORT-SCANS/TASKS/TASK-04.md) |
| **05** | Saving the Output | حفظ التقارير وصيغ التصدير | 1. Parameter for greppable format?<br>2. Possible to save in XML (yea/nay)? | `-oG`<br>`yea` | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/04-NMAP-POST-PORT-SCANS/TASKS/TASK-05.md) |
| **06** | Summary | الخلاصة والربط المعماري | Completed room successfully | `No answer needed` | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/04-NMAP-POST-PORT-SCANS/TASKS/TASK-06.md) |
