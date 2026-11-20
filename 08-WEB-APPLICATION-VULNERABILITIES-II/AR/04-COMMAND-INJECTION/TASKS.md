# Matrix of Tasks - Command Injection (مصفوفة المهام)

| رقم المهمة | عنوان المهمة | عدد الأسئلة | الموضوع الجوهري | حالة الإنجاز | رابط ملف الشرح التفصيلي |
| :---: | :--- | :---: | :--- | :---: | :---: |
| **01** | Introduction | 0 | مدخل حقن أوامر نظام التشغيل وسياق الصلاحيات وتصنيف CWE-78 | مكتمل ✅ | [TASK-01.md](../../04-COMMAND-INJECTION/TASKS/TASK-01.md) |
| **02** | Discovering Command Injection | 3 | تشريح الدوال المصابة في PHP (`$title`, `GET`) و Flask (`/id`) | مكتمل ✅ | [TASK-02.md](../../04-COMMAND-INJECTION/TASKS/TASK-02.md) |
| **03** | Exploiting Command Injection | 3 | الحمولات المرئية والعمياء (`whoami`, `ping`, `timeout`) | مكتمل ✅ | [TASK-03.md](../../04-COMMAND-INJECTION/TASKS/TASK-03.md) |
| **04** | Practical | 2 | المعمل العملي: اكتشاف مستخدم `www-data` واستخراج العلم | مكتمل ✅ | [TASK-04.md](../../04-COMMAND-INJECTION/TASKS/TASK-04.md) |
| **05** | Remediation | 1 | التحصين الهندسي ومفهوم تطهير المدخلات (`sanitisation`) | مكتمل ✅ | [TASK-05.md](../../04-COMMAND-INJECTION/TASKS/TASK-05.md) |
| **06** | Conclusion | 0 | الخاتمة ومراجعة استراتيجيات الدفاع والانتقال إلى API Pentesting | مكتمل ✅ | [TASK-06.md](../../04-COMMAND-INJECTION/TASKS/TASK-06.md) |

---

## ملخص الإجابات والحلول المعتمدة (Quick Reference Table)

| المهمة | السؤال | الإجابة الدقيقة | طريقة الاستخراج والتحليل |
| :--- | :--- | :--- | :--- |
| **Task 1** | Read Introduction | `No answer needed` | قراءة المقدمة والتأسيس النظري |
| **Task 2** | Variable storing user input in PHP | `$title` | فحص السطر `$title = $_GET["title"];` |
| **Task 2** | HTTP method used in PHP snippet | `GET` | فحص مصفوفة `$_GET` الفائقة |
| **Task 2** | Route to execute id command in Flask | `/id` | مطابقة مسار `@app.route('/<shell>')` |
| **Task 3** | Payload to determine executing user | `whoami` | الأمر القياسي لكشف مستخدم العملية |
| **Task 3** | Linux blind test network tool | `ping` | أداة إرسال حزم صدى ICMP بتأخير زمني |
| **Task 3** | Windows blind test payload | `timeout` | أمر فرض المهلة الزمنية في موجه أوامر ويندوز |
| **Task 4** | User the application is running as | `www-data` | حقن `; whoami` في نموذج أداة التشخيص الشبكية |
| **Task 4** | Contents of /home/tryhackme/flag.txt | `THM{COMMAND_INJECTION_COMPLETE}` | حقن `; cat /home/tryhackme/flag.txt` وقراءة الملف |
| **Task 5** | Term for cleaning user input | `sanitisation` | المصطلح المعياري لتنقية وتطهير المدخلات |
| **Task 6** | Conclusion completed | `No answer needed` | مراجعة مصفوفة الأمان الدفاعي |
