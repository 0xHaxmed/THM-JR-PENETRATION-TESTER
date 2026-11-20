# فهرس المهام والإجابات المعتمدة: هجمات خوادم الويب - الجزء الأول (Web Server Attacks - I)

---

### جدول ملخص المهام والأسئلة والإجابات المعتمدة (Tasks & Answers Summary)

| رقم المهمة | عنوان المهمة (Task Title) | نص السؤال (Question) | الإجابة المعتمدة (Verified Answer) | نوع الإجابة والملاحظات |
| :---: | :--- | :--- | :---: | :--- |
| **01** | **Introduction** | I have deployed the lab machines! | `No answer needed` | تأكيد بدء تشغيل الآلة المعملية |
| **02** | **Identifying Web Servers** | What value does the Server header return for the Python HTTP Server running on port 8000? | `SimpleHTTP/0.6 Python/3.12.3` | قيمة ترويسة Server لخادم بايثون |
| **02** | **Identifying Web Servers** | Which HTTP header reveals the application framework running on port 3000? | `X-Powered-By` | ترويسة إطار Express |
| **02** | **Identifying Web Servers** | What web server software is running on port 8080? | `nginx` | اسم خادم الويب على المنفذ 8080 |
| **03** | **Python HTTP Server** | What database password is stored in the .env file served by the Python HTTP Server? | `S3cur3DBPass!` | كلمة مرور قاعدة البيانات من ملف .env |
| **03** | **Python HTTP Server** | What is the flag found inside the backup archive served by the Python HTTP Server? | `THM{py_server_exposed}` | علم أرشيف backup.zip |
| **04** | **Apache2** | What Apache module exposes real-time server statistics at /server-status ? | `mod_status` | الوحدة المسؤولة عن صفحة إحصائيات أباتشي |
| **04** | **Apache2** | What is the flag found in the /files/ directory on Apache? | `THM{apache_dir_listing}` | علم المجلد المفهرس internal-notes.txt |
| **05** | **Node.js (Express)** | What value does the NODE_ENV variable have in the debug endpoint response? | `development` | قيمة متغير البيئة في /api/debug/env |
| **05** | **Node.js (Express)** | What is the flag found in the static files served by the Node.js application? | `THM{node_debug_exposed}` | علم ملف static/config.js |
| **06** | **Nginx** | What Nginx directive enables directory listing in a location block? | `autoindex on` | توجيه تفعيل فهرسة المجلدات في Nginx |
| **06** | **Nginx** | What URL path exposes Nginx connection statistics? | `/nginx_status` | مسار إحصائيات اتصالات Nginx |
| **06** | **Nginx** | What is the flag found in the directory listing on port 8080? | `THM{nginx_autoindex}` | علم فهرسة Nginx من deploy-notes.txt |
| **07** | **Common Misconfigurations** | Which security header, when missing, allows a page to be embedded in an iframe on another domain? | `X-Frame-Options` | ترويسة الحماية من هجمات Clickjacking |
| **07** | **Common Misconfigurations** | What finding text does Nikto report when it detects directory indexing on the /files/ path? | `Directory indexing found` | نص تنبيه أداة Nikto عند اكتشاف الفهرسة |
| **08** | **Conclusion** | I have successfully completed the room! | `No answer needed` | إتمام وتأكيد اجتياز المعمل بنجاح |

---

### روابط ملفات المهام التفصيلية (7-Stage Methodology Links):
* [المهمة 01: المقدمة ومنهجية رصد أخطاء التهيئة (TASK-01.md)](../../04-WEB-SERVER-ATTACKS-I/TASKS/TASK-01.md)
* [المهمة 02: تحديد وبصمة خوادم الويب (TASK-02.md)](../../04-WEB-SERVER-ATTACKS-I/TASKS/TASK-02.md)
* [المهمة 03: خادم بايثون ومخاطر تسريب الملفات الحساسة (TASK-03.md)](../../04-WEB-SERVER-ATTACKS-I/TASKS/TASK-03.md)
* [المهمة 04: خادم أباتشي وفهرسة الدلائل وإحصائيات mod_status (TASK-04.md)](../../04-WEB-SERVER-ATTACKS-I/TASKS/TASK-04.md)
* [المهمة 05: خادم Node.js ونقاط التصحيح المكشوفة (TASK-05.md)](../../04-WEB-SERVER-ATTACKS-I/TASKS/TASK-05.md)
* [المهمة 06: خادم Nginx وتوجيه autoindex وإحصائيات الاتصال (TASK-06.md)](../../04-WEB-SERVER-ATTACKS-I/TASKS/TASK-06.md)
* [المهمة 07: أخطاء التهيئة المشتركة وترويسات الأمان الغائبة (TASK-07.md)](../../04-WEB-SERVER-ATTACKS-I/TASKS/TASK-07.md)
* [المهمة 08: الخاتمة ومصفوفة المقارنة الدفاعية (TASK-08.md)](../../04-WEB-SERVER-ATTACKS-I/TASKS/TASK-08.md)
