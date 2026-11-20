# Room Tasks Index: Protocols and Servers 2
## فهرس مهام غرفة: البروتوكولات والخوادم 2

* **Room Name:** Protocols and Servers 2
* **Room URL:** [https://tryhackme.com/room/protocolsandservers2](https://tryhackme.com/room/protocolsandservers2)
* **Parent Module:** Module 03: Network Reconnaissance
* **Total Tasks:** 7 Tasks
* **Task Methodology:** Guided Attempt + Complete Solution (Stages A through G)

---

### 📋 المهام وحالتها الموثقة

| رقم المهمة | عنوان المهمة الأصلي | المفهوم الأساسي | رابط ملف الحل المكتمل | الحالة |
|---|---|---|---|---|
| **Task 01** | Introduction | مدخل إلى استغلال البروتوكولات النصية وآليات التشفير | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/04-PROTOCOLS-AND-SERVERS-2/TASKS/TASK-01.md) | مكتملة ومحققة |
| **Task 02** | Sniffing Attack | اعتراض الحزم عبر tcpdump (port 23) وفلتر Wireshark (imap) | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/04-PROTOCOLS-AND-SERVERS-2/TASKS/TASK-02.md) | مكتملة ومحققة |
| **Task 03** | Man-in-the-Middle (MITM) Attack | تسميم الـ ARP، واجهات Ettercap الـ 3 وطرق استدعاء Bettercap الـ 3 | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/04-PROTOCOLS-AND-SERVERS-2/TASKS/TASK-03.md) | مكتملة ومحققة |
| **Task 04** | Transport Layer Security (TLS) | معايير التشفير وتأمين استعلامات الـ DNS عبر بروتوكول DoT | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/04-PROTOCOLS-AND-SERVERS-2/TASKS/TASK-04.md) | مكتملة ومحققة |
| **Task 05** | Secure Shell (SSH) | إدارة الخوادم المشفرة، كشف إصدار الكيرنل وتنزيل الملفات عبر scp | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/04-PROTOCOLS-AND-SERVERS-2/TASKS/TASK-05.md) | مكتملة ومحققة |
| **Task 06** | Password Attack | كسر كلمات مرور خوادم البريد IMAP عبر THC Hydra واستخراج butterfly | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/04-PROTOCOLS-AND-SERVERS-2/TASKS/TASK-06.md) | مكتملة ومحققة |
| **Task 07** | Summary | خلاصة أمن البروتوكولات ومصفوفة التحصين الشامل | [TASK-07.md](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/04-PROTOCOLS-AND-SERVERS-2/TASKS/TASK-07.md) | مكتملة ومحققة |

---

### 🔑 Verified Answers Summary | ملخص الإجابات والرايات المعتمدة
* **Task 1 (Introduction):** `No answer needed`
* **Task 2 (Sniffing Attack):** 
  * *What do you need to add to the command sudo tcpdump to capture only Telnet traffic?* ⟶ `port 23`
  * *What is the simplest display filter you can use with Wireshark to show only IMAP traffic?* ⟶ `imap`
* **Task 3 (Man-in-the-Middle Attack):** 
  * *How many different interfaces does Ettercap offer?* ⟶ `3`
  * *In how many ways can you invoke Bettercap?* ⟶ `3`
* **Task 4 (TLS):** 
  * *What is the three-letter acronym of the DNS protocol that uses TLS?* ⟶ `DoT`
* **Task 5 (SSH):** 
  * *Using uname -r , find the Kernel release?* ⟶ `5.15.0-119-generic`
  * *How many KBs did scp display as download size?* ⟶ `415`
* **Task 6 (Password Attack):** 
  * *What is the password used to access the IMAP service on MACHINE_IP?* ⟶ `butterfly`
* **Task 7 (Summary):** `No answer needed`
