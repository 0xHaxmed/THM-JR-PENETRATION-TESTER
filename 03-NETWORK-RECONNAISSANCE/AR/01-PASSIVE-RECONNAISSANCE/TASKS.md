# Room Tasks Index: Passive Reconnaissance
## فهرس مهام غرفة: الاستطلاع السلبي

* **Room Name:** Passive Reconnaissance
* **Room URL:** [https://tryhackme.com/room/passiverecon](https://tryhackme.com/room/passiverecon)
* **Parent Module:** Module 03: Network Reconnaissance
* **Total Tasks:** 7 Tasks
* **Task Methodology:** Guided Attempt + Complete Solution (Stages A through G)

---

### 📋 المهام وحالتها الموثقة

| رقم المهمة | عنوان المهمة الأصلي | المفهوم الأساسي | رابط ملف الحل المكتمل | الحالة |
|---|---|---|---|---|
| **Task 01** | Introduction | مدخل إلى الاستطلاع السلبي والتخفي التشغيلي | [TASK-01.md](../../01-PASSIVE-RECONNAISSANCE/TASKS/TASK-01.md) | مكتملة ومحققة |
| **Task 02** | Passive Versus Active Recon | التمييز العملي بين الأنشطة السلبية (P) والأنشطة النشطة (A) | [TASK-02.md](../../01-PASSIVE-RECONNAISSANCE/TASKS/TASK-02.md) | مكتملة ومحققة |
| **Task 03** | Whois | استعلام بروتوكول WHOIS وسجلات التسجيل وخوادم الأسماء | [TASK-03.md](../../01-PASSIVE-RECONNAISSANCE/TASKS/TASK-03.md) | مكتملة ومحققة |
| **Task 04** | nslookup and dig | استعلام سجلات DNS النصية (TXT) واستخراج الراية الأمنية | [TASK-04.md](../../01-PASSIVE-RECONNAISSANCE/TASKS/TASK-04.md) | مكتملة ومحققة |
| **Task 05** | DNSDumpster | استكشاف النطاقات الفرعية ورسم الخريطة وتحليل خدمات cloudflare | [TASK-05.md](../../01-PASSIVE-RECONNAISSANCE/TASKS/TASK-05.md) | مكتملة ومحققة |
| **Task 06** | Shodan.io | استعلام محرك Shodan وكشف إحصائيات خوادم Apache و Nginx | [TASK-06.md](../../01-PASSIVE-RECONNAISSANCE/TASKS/TASK-06.md) | مكتملة ومحققة |
| **Task 07** | Summary | تلخيص خط أنابيب الاستطلاع والجاهزية للاستطلاع النشط | [TASK-07.md](../../01-PASSIVE-RECONNAISSANCE/TASKS/TASK-07.md) | مكتملة ومحققة |

---

### 🔑 Verified Answers Summary | ملخص الإجابات والرايات المعتمدة
* **Task 1:** `No answer needed`
* **Task 2:** 
  * *You visit the Facebook page of the target company...* ⟶ `P`
  * *You ping the IP address of the company web server to check if ICMP traffic is blocked...* ⟶ `A`
  * *You happen to visit the company’s shop, and you browse a few web pages to see if you can identify used web technologies...* ⟶ `A`
* **Task 3:** 
  * *When was tryhackme.com created? Give the answer in the format YYYYMMDD.* ⟶ `20180705`
  * *What is the registrar of tryhackme.com?* ⟶ `namecheap.com`
  * *Which company is tryhackme.com using for name servers?* ⟶ `cloudflare.com`
* **Task 4:** 
  * *Check the TXT records of thmlabs.com. What is the flag there?* ⟶ `THM{a5b83929888ed36acb0272971e438d78}`
* **Task 5:** 
  * *Lookup tryhackme.com on DNSDumpster. Under Services / Banners, which one has the highest count?* ⟶ `cloudflare`
* **Task 6:** 
  * *According to Shodan.io, what is the first country in the world in terms of the number of publicly accessible Apache servers?* ⟶ `United States`
  * *Based on Shodan.io, what is the 3rd most common port used for Apache?* ⟶ `8080`
  * *Based on Shodan.io, what is the most common port used for nginx?* ⟶ `80`
* **Task 7:** `No answer needed`
