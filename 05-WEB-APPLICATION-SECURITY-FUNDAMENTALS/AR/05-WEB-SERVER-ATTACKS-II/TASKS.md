# فهرس المهام والإجابات المعتمدة: هجمات خوادم الويب - الجزء الثاني (Web Server Attacks - II)

---

### جدول ملخص المهام والأسئلة والإجابات المعتمدة (Tasks & Answers Summary)

| رقم المهمة | عنوان المهمة (Task Title) | نص السؤال (Question) | الإجابة المعتمدة (Verified Answer) | نوع الإجابة والملاحظات |
| :---: | :--- | :--- | :---: | :--- |
| **01** | **Introduction** | I have deployed the lab machines! | `No answer needed` | تأكيد بدء تشغيل الآلة المعملية |
| **02** | **IIS Fingerprinting & Enumeration** | What HTTP response header reveals the IIS version? | `Server` | ترويسة إعلان خادم IIS |
| **02** | **IIS Fingerprinting & Enumeration** | What is the HTTP response code after executing the PUT request on the /webdav directory? | `401` | كود طلب المصادقة لعملية الرفع |
| **03** | **IIS Tilde Enumeration** | What character in a URL path triggers the IIS tilde enumeration response difference? | `~` | علامة المدة المستخدمة للأسماء القصيرة |
| **03** | **IIS Tilde Enumeration** | What legacy Windows filename format does the tilde enumeration vulnerability exploit? | `8.3` | صيغة التسمية القديمة لنظام DOS |
| **03** | **IIS Tilde Enumeration** | What password is stored in the file discovered through tilde enumeration? | `P@ssw0rd!123` | كلمة المرور المستخرجة من webdav_notes.txt |
| **04** | **WebDAV Exploitation: Uploading ASPX** | What HTTP status code confirms a file was successfully created via PUT ? | `201` | كود نجاح إنشاء المورد عبر WebDAV |
| **04** | **WebDAV Exploitation: Uploading ASPX** | What curl flag is required to authenticate with NTLM when uploading via WebDAV? | `--ntlm` | معامل مصادقة NTLM في أداة curl |
| **05** | **ASPX Web Shells** | After triggering the ASPX shell, what user account is returned by the whoami command? | `iis apppool\defaultapppool` | حساب تشغيل حوض تطبيقات IIS الافتراضي |
| **05** | **ASPX Web Shells** | What Windows privilege, visible in whoami /priv output from the reverse shell, enables Potato-style privilege escalation from an IIS shell? | `SeImpersonatePrivilege` | امتياز انتحال الرموز لرفع الصلاحيات لـ SYSTEM |
| **06** | **IIS Misconfigurations** | Navigate to the /uploads/ directory on the target. What sensitive file extension is visible in the directory listing? | `.bak` | امتداد ملف النسخة الاحتياطية الحساسة |
| **06** | **IIS Misconfigurations** | What ASP.NET diagnostic handler URL path should be disabled in production as it can expose session data, cookies, and request history? | `trace.axd` | مسار معالج تتبع الجلسات والطلبات الحية |
| **07** | **Automation** | What nmap NSE script enumerates allowed HTTP methods on a web server? | `http-methods` | سكربت Nmap لفحص طرق الـ HTTP المسموحة |
| **08** | **Conclusion** | I have successfully completed the room! | `No answer needed` | إتمام وتأكيد اجتياز المعمل بنجاح |

---

### روابط ملفات المهام التفصيلية (7-Stage Methodology Links):
* [المهمة 01: المقدمة وسياق الهجمات الميدانية (TASK-01.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/TASKS/TASK-01.md)
* [المهمة 02: التقاط بصمات IIS واكتشاف WebDAV (TASK-02.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/TASKS/TASK-02.md)
* [المهمة 03: تخمين الأسماء القصيرة 8.3 عبر علامة المدة (TASK-03.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/TASKS/TASK-03.md)
* [المهمة 04: استغلال WebDAV ورفع صدفة ASPX عبر NTLM (TASK-04.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/TASKS/TASK-04.md)
* [المهمة 05: صدفات ASPX وامتياز انتحال الرموز SeImpersonatePrivilege (TASK-05.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/TASKS/TASK-05.md)
* [المهمة 06: أخطاء التهيئة الشائعة في IIS وتسريب الملفات ومسار trace.axd (TASK-06.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/TASKS/TASK-06.md)
* [المهمة 07: أتمتة فحص IIS عبر سكربتات Nmap NSE (TASK-07.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/TASKS/TASK-07.md)
* [المهمة 08: الخاتمة ومخطط مسار الهجوم المتكامل (TASK-08.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/TASKS/TASK-08.md)
