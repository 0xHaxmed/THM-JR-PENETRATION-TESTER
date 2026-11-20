# Room Tasks Index: Active Reconnaissance
## فهرس مهام غرفة: الاستطلاع النشط

* **Room Name:** Active Reconnaissance
* **Room URL:** [https://tryhackme.com/room/activerecon](https://tryhackme.com/room/activerecon)
* **Parent Module:** Module 03: Network Reconnaissance
* **Total Tasks:** 7 Tasks
* **Task Methodology:** Guided Attempt + Complete Solution (Stages A through G)

---

### 📋 المهام وحالتها الموثقة

| رقم المهمة | عنوان المهمة الأصلي | المفهوم الأساسي | رابط ملف الحل المكتمل | الحالة |
|---|---|---|---|---|
| **Task 01** | Introduction | مدخل إلى الاستطلاع النشط والمفاهيم القانونية وإرسال الحزم المباشرة | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/02-ACTIVE-RECONNAISSANCE/TASKS/TASK-01.md) | مكتملة ومحققة |
| **Task 02** | Web Browser | استكشاف تطبيقات الويب عبر أدوات المطور (DevTools) وشجرة الـ DOM | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/02-ACTIVE-RECONNAISSANCE/TASKS/TASK-02.md) | مكتملة ومحققة |
| **Task 03** | Ping | بروتوكول ICMP، خيارات حجم الحزم (-s)، وحظر ويندوز الافتراضي | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/02-ACTIVE-RECONNAISSANCE/TASKS/TASK-03.md) | مكتملة ومحققة |
| **Task 04** | Traceroute | تتبع مسارات الحزم، آلية حقل TTL، ورسائل ICMP Type 11 | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/02-ACTIVE-RECONNAISSANCE/TASKS/TASK-04.md) | مكتملة ومحققة |
| **Task 05** | Telnet | الاتصال المباشر بمقابس TCP واستخراج ترويسات خوادم Apache 2.4.61 | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/02-ACTIVE-RECONNAISSANCE/TASKS/TASK-05.md) | مكتملة ومحققة |
| **Task 06** | Netcat | مقابس TCP الخام، استكشاف منفذ FTP 21 واستخراج الإصدار 0.17 | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/02-ACTIVE-RECONNAISSANCE/TASKS/TASK-06.md) | مكتملة ومحققة |
| **Task 07** | Putting It All Together | تكامل خط أنابيب الاستطلاع النشط والجاهزية لأداة Nmap | [TASK-07.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/02-ACTIVE-RECONNAISSANCE/TASKS/TASK-07.md) | مكتملة ومحققة |

---

### 🔑 Verified Answers Summary | ملخص الإجابات والرايات المعتمدة
* **Task 1 (Introduction):** `No answer needed`
* **Task 2 (Web Browser):** 
  * *Using the Developer Tools, figure out the total number of questions* ⟶ `8`
* **Task 3 (Ping):** 
  * *Which option would you use to set the size of the data carried by the ICMP echo request?* ⟶ `-s`
  * *What is the size of the ICMP header in bytes?* ⟶ `8`
  * *Does MS Windows Firewall block ping by default? (Y/N)* ⟶ `Y`
  * *How many ping replies did you get back?* ⟶ `10`
* **Task 4 (Traceroute):** 
  * *In Traceroute A, what is the IP address of the last router/hop before reaching tryhackme.com?* ⟶ `100.92.9.83`
  * *In Traceroute B, what is the IP address of the last router/hop before reaching tryhackme.com?* ⟶ `99.83.89.19`
  * *In Traceroute B, the destination is on line 26. How many routers sit between your AttackBox and tryhackme.com, not counting the destination itself?* ⟶ `25`
  * *Check how many routers/hops are there between the AttackBox and the target VM* ⟶ `No answer needed`
* **Task 5 (Telnet):** 
  * *What is the name of the running server?* ⟶ `Apache`
  * *What is the version of the running server (on port 80 of the VM)?* ⟶ `2.4.61`
* **Task 6 (Netcat):** 
  * *...use Netcat to connect to the VM port 21. What is the version of the running server?* ⟶ `0.17`
* **Task 7 (Putting It All Together):** `No answer needed`
