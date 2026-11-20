# فهرس مهام معمل: اكتشاف الأجهزة النشطة (Nmap Live Host Discovery - TASKS.md)

* **الغرفة:** Nmap Live Host Discovery
* **المسار:** Module 04: Nmap — Room 01 (`nmap01`)
* **رابط المعمل:** `https://tryhackme.com/room/nmap01`
* **إجمالي المهام:** 9 مهام مكتملة وموثقة بنسبة 100%

---

### 📑 جدول ملخص المهام والحلول المعتمدة (Tasks & Answers Summary)

| رقم المهمة | عنوان المهمة | موضوع الفحص والتقنية | السؤال | الإجابة المعتمدة | ملف التوثيق المفصل |
|---|---|---|---|---|---|
| **01** | Introduction | مدخل ومفاهيم استكشاف الأجهزة | Start AttackBox / Intro | `No answer needed` | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-01.md) |
| **02** | Subnetworks | تقسيم الشبكات ومجالات بث ARP | 1. How many devices see ARP Request?<br>2. Did computer6 receive ARP Request?<br>3. From comp4 to comp4, devices count?<br>4. Did computer6 reply? | `4`<br>`nay`<br>`4`<br>`yea` | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-02.md) |
| **03** | Understanding Hosts Discovery through TCP/IP Layer | طبقات TCP/IP وحزم ARP و ICMP | 1. Packet type before ping?<br>2. Packet type received before ping?<br>3. Computers responded to ping?<br>4. First device responding to 1st ARP?<br>5. First device responding to 2nd ARP?<br>6. Did 2nd ping require new ARP? | `ARP Request`<br>`ARP Response`<br>`1`<br>`router`<br>`computer5`<br>`nay` | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-03.md) |
| **04** | Enumerating Targets | صيغ تحديد الأهداف وحسابات CIDR | 1. First IP scanned in 10.10.12.13/29?<br>2. IP count in 10.10.0-255.101-125? | `10.10.12.8`<br>`6400` | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-04.md) |
| **05** | Nmap Host Discovery Using ARP | فحص الشبكة المحلية عبر ARP (`-PR`) | Live hosts in CONNECTION_IP/24? | `1` | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-05.md) |
| **06** | Nmap Host Discovery Using ICMP | تجاوز الحظر بـ Echo, Timestamp, Mask | 1. ICMP Timestamp option?<br>2. ICMP Address Mask option?<br>3. ICMP Echo option? | `-PP`<br>`-PM`<br>`-PE` | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-06.md) |
| **07** | Nmap Host Discovery Using TCP and UDP | فحص الطبقة الرابعة والصلاحيات | 1. Scan sending named packet unprivileged?<br>2. Scan requiring privileged for named packet?<br>3. TCP SYN ping on Telnet port option? | `TCP SYN Ping`<br>`TCP ACK Ping`<br>`-PS23` | [TASK-07.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-07.md) |
| **08** | Using Reverse-DNS Lookup | استعلامات الـ DNS العكسية وسجلات PTR | Option forcing reverse DNS for all hosts? | `-R` | [TASK-08.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-08.md) |
| **09** | Summary | الخلاصة والجدول المرجعي | Review summary & join next room | `No answer needed` | [TASK-09.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/TASKS/TASK-09.md) |
