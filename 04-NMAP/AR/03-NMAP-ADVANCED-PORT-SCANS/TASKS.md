# فهرس مهام معمل: فحص المنافذ المتقدم والتخفي (Nmap Advanced Port Scans - TASKS.md)

* **الغرفة:** Nmap Advanced Port Scans
* **المسار:** Module 04: Nmap — الغرفة 03 (`nmap03`)
* **رابط المعمل:** `https://tryhackme.com/room/nmap03`
* **إجمالي المهام:** 9 مهام مكتملة وموثقة بنسبة 100%

---

### 📑 جدول ملخص المهام والحلول المعتمدة (Tasks & Answers Summary)

| رقم المهمة | عنوان المهمة | موضوع الفحص والتقنية | السؤال | الإجابة المعتمدة | ملف التوثيق المفصل |
|---|---|---|---|---|---|
| **01** | Introduction | مدخل وتشغيل الآلات المعملية | I have started the machines | `No answer needed` | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-01.md) |
| **02** | TCP Null Scan, FIN Scan, and Xmas Scan | الفحوصات الشاذة غير المعيارية | 1. Flags in Null scan?<br>2. Flags in FIN scan?<br>3. Flags in Xmas scan?<br>4. FIN open\|filtered count?<br>5. Null open\|filtered count? | `0`<br>`1`<br>`3`<br>`9`<br>`9` | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-02.md) |
| **03** | TCP Maimon Scan | فحص مايمون وثغرة أنظمة BSD | Flags set in Maimon scan? | `2` | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-03.md) |
| **04** | TCP ACK, Window, and Custom Scan | رسم قواعد الجدران النارية والنافذة | 1. Flags in Window scan?<br>2. Reset flag for custom scan?<br>3. Unfiltered ports in ACK scan?<br>4. Newly appeared port number?<br>5. Service behind new port? | `1`<br>`RST`<br>`5`<br>`443`<br>`yea` | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-04.md) |
| **05** | Spoofing and Decoys | تزوير عنوان المصدر والعناوين الوهمية | 1. Option spoofing IP 10.10.10.11?<br>2. Option with decoys and ME? | `-S 10.10.10.11`<br>`-D 10.10.20.21,10.10.20.28,ME` | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-05.md) |
| **06** | Fragmented Packets | تجزئة الحزم وتجاوز الفحص السطحي | Fragment count for size 64 with -ff? | `4` | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-06.md) |
| **07** | Idle/Zombie Scan | الاستطلاع الأعمى عبر طابعة زومبي | Nmap argument using zombie 10.10.5.5? | `-sI 10.10.5.5` | [TASK-07.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-07.md) |
| **08** | Getting More Details | تفسير الأسباب والإسهاب (`--reason`) | Reason provided for open port(s)? | `syn-ack` | [TASK-08.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-08.md) |
| **09** | Summary | الخلاصة والجدول المقارن | Note all options & join next room | `No answer needed` | [TASK-09.md](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-09.md) |
