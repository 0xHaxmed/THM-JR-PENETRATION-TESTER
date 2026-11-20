# Room Tasks Index: Protocols and Servers
## فهرس مهام غرفة: البروتوكولات والخوادم

* **Room Name:** Protocols and Servers
* **Room URL:** [https://tryhackme.com/room/protocolsandservers](https://tryhackme.com/room/protocolsandservers)
* **Parent Module:** Module 03: Network Reconnaissance
* **Total Tasks:** 8 Tasks
* **Task Methodology:** Guided Attempt + Complete Solution (Stages A through G)

---

### 📋 المهام وحالتها الموثقة

| رقم المهمة | عنوان المهمة الأصلي | المفهوم الأساسي | رابط ملف الحل المكتمل | الحالة |
|---|---|---|---|---|
| **Task 01** | Introduction | مدخل إلى بروتوكولات طبقة التطبيقات والاتصال بالمقابس | [TASK-01.md](../../03-PROTOCOLS-AND-SERVERS/TASKS/TASK-01.md) | مكتملة ومحققة |
| **Task 02** | Telnet | بروتوكول الإدارة النصية القديمة والمنفذ الافتراضي 23 | [TASK-02.md](../../03-PROTOCOLS-AND-SERVERS/TASKS/TASK-02.md) | مكتملة ومحققة |
| **Task 03** | Hypertext Transfer Protocol (HTTP) | صياغة طلبات GET يدوياً واسترجاع راية flag.thm | [TASK-03.md](../../03-PROTOCOLS-AND-SERVERS/TASKS/TASK-03.md) | مكتملة ومحققة |
| **Task 04** | File Transfer Protocol (FTP) | المصادقة عبر المنفذ 21 وتنزيل الملفات واستخراج الراية | [TASK-04.md](../../03-PROTOCOLS-AND-SERVERS/TASKS/TASK-04.md) | مكتملة ومحققة |
| **Task 05** | Simple Mail Transfer Protocol (SMTP) | رسائل الترحيب الكود 220 واستخراج راية خادم البريد | [TASK-05.md](../../03-PROTOCOLS-AND-SERVERS/TASKS/TASK-05.md) | مكتملة ومحققة |
| **Task 06** | Post Office Protocol 3 (POP3) | مصادقة البريد، أمر STAT، واستعلام عدد الرسائل المتاحة | [TASK-06.md](../../03-PROTOCOLS-AND-SERVERS/TASKS/TASK-06.md) | مكتملة ومحققة |
| **Task 07** | Internet Message Access Protocol (IMAP) | مزامنة البريد على الخادم والمنفذ الافتراضي المعياري 143 | [TASK-07.md](../../03-PROTOCOLS-AND-SERVERS/TASKS/TASK-07.md) | مكتملة ومحققة |
| **Task 08** | Summary | مصفوفة بروتوكولات النصوص الصريحة والتمهيد لـ Protocols 2 | [TASK-08.md](../../03-PROTOCOLS-AND-SERVERS/TASKS/TASK-08.md) | مكتملة ومحققة |

---

### 🔑 Verified Answers Summary | ملخص الإجابات والرايات المعتمدة
* **Task 1 (Introduction):** `No answer needed`
* **Task 2 (Telnet):** 
  * *To which port will the telnet command with the default parameters try to connect?* ⟶ `23`
* **Task 3 (HTTP):** 
  * *...connect using Telnet to MACHINE_IP 80 and retrieve the file flag.thm . What does it contain?* ⟶ `THM{e3eb0a1df437f3f97a64aca5952c8ea0}`
* **Task 4 (FTP):** 
  * *Using an FTP client, connect to the VM and try to recover the flag file. What is the flag?* ⟶ `THM{364db6ad0e3ddfe7bf0b1870fb06fbdf}`
* **Task 5 (SMTP):** 
  * *Using the AttackBox terminal, connect to the SMTP port of the target VM. What is the flag that you can get?* ⟶ `THM{5b31ddfc0c11d81eba776e983c35e9b5}`
* **Task 6 (POP3):** 
  * *...What is the response you get to STAT ?* ⟶ `+OK 0 0`
  * *How many email messages are available to download via POP3 on MACHINE_IP ?* ⟶ `0`
* **Task 7 (IMAP):** 
  * *What is the default port used by IMAP?* ⟶ `143`
* **Task 8 (Summary):** `No answer needed`
