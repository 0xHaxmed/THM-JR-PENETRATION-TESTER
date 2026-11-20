# فهرس مهام معمل: فحص المنافذ الأساسي (Nmap Basic Port Scans - TASKS.md)

* **الغرفة:** Nmap Basic Port Scans
* **المسار:** Module 04: Nmap — Room 02 (`nmap02`)
* **رابط المعمل:** `https://tryhackme.com/room/nmap02`
* **إجمالي المهام:** 8 مهام مكتملة وموثقة بنسبة 100%

---

### 📑 جدول ملخص المهام والحلول المعتمدة (Tasks & Answers Summary)

| رقم المهمة | عنوان المهمة | موضوع الفحص والتقنية | السؤال | الإجابة المعتمدة | ملف التوثيق المفصل |
|---|---|---|---|---|---|
| **01** | Introduction | مدخل وتشغيل الآلة المعملية | I have started the machine | `No answer needed` | [TASK-01.md](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-01.md) |
| **02** | TCP and UDP Ports | المنافذ وحالات Nmap الست | 1. Service on UDP 53?<br>2. Service on TCP 22?<br>3. Port states count in Nmap?<br>4. Most interesting port state? | `DNS`<br>`SSH`<br>`6`<br>`Open` | [TASK-02.md](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-02.md) |
| **03** | TCP Flags | ترويسة TCP والرايات الست | 1. Reset flag 3 letters?<br>2. Flag initiating TCP connection? | `RST`<br>`SYN` | [TASK-03.md](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-03.md) |
| **04** | TCP Connect Scan | الفحص الكامل بالمصافحة الثلاثية (`-sT`) | 1. State of FTP on port 21?<br>2. Nmap guess for service on port 53? | `open`<br>`domain` | [TASK-04.md](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-04.md) |
| **05** | TCP SYN Scan | فحص التخفي نصف المفتوح (`-sS`) | 1. SYN-ACK packets received?<br>2. Open ports count on target? | `4`<br>`4` | [TASK-05.md](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-05.md) |
| **06** | UDP Scan | فحص بروتوكول UDP عديم الاتصال (`-sU`) | 1. State of port 161 UDP?<br>2. Service name on port 161? | `closed`<br>`snmp` | [TASK-06.md](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-06.md) |
| **07** | Fine-Tuning Scope and Performance | تخصيص المنافذ والتوقيت والتوازي | 1. Option for ports 5000 to 5500?<br>2. Option for at least 64 parallel probes?<br>3. Timing option for very slow & paranoid? | `-p5000-5500`<br>`--min-parallelism=64`<br>`-T0` | [TASK-07.md](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-07.md) |
| **08** | Summary | الخلاصة والجدول المقارن | Note all options & join next room | `No answer needed` | [TASK-08.md](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-08.md) |
