# فهرس المهام والإجابات المعتمدة: أطر الويب الحديثة (Modern Web Stacks)

---

### جدول ملخص المهام والأسئلة والإجابات المعتمدة (Tasks & Answers Summary)

| رقم المهمة | عنوان المهمة (Task Title) | نص السؤال (Question) | الإجابة المعتمدة (Verified Answer) | نوع الإجابة والملاحظات |
| :---: | :--- | :--- | :---: | :--- |
| **01** | **Introduction** | I have started the machine and am ready to begin. | `No answer needed` | تأكيد بدء تشغيل الآلة المعملية |
| **02** | **MERN Stack** | What HTTP response header confirms an Express.js backend is running? | `X-Powered-By: Express` | ترويسة تعريف خادم Express |
| **02** | **MERN Stack** | What is the name of the Express session cookie you will use to replay requests after polluting the prototype? | `connect.sid` | اسم كعكة الجلسة الافتراضية |
| **02** | **MERN Stack** | Send the prototype pollution payload to the merge endpoint. What is the flag returned by the admin route after the bypass succeeds? | `THM{pr0t0_p0llut3d}` | علم استغلال Prototype Pollution |
| **03** | **React / Next.js** | What HTML artifact in the page source confirms a Next.js App Router application? | `window.__next_f` | مصفوفة بث وترطيب RSC في المتصفح |
| **03** | **React / Next.js** | Send the CVE-2025-29927 bypass header to the protected /dashboard route. What flag is displayed on the page? | `THM{m1ddl3w4r3_byp4ss3d}` | علم تجاوز مصادقة الـ Middleware |
| **04** | **Django** | What hidden form field in Django POST forms is a near-certain stack fingerprint? | `csrfmiddlewaretoken` | حقل الحماية من هجمات CSRF في Django |
| **04** | **Django** | Using manual curl payloads, what is the name of the vulnerable database? | `vuln_db` | اسم قاعدة البيانات المستخرج بحقن XPath |
| **05** | **LAMP** | What exact Server header value identifies this target as vulnerable to CVE-2021-41773? | `Apache/2.4.49 (Unix)` | ترويسة الخادم المصاب بثغرة تخطي المسار |
| **05** | **LAMP** | What curl flag is required to prevent curl from normalising the traversal sequences in the URL before sending? | `--path-as-is` | معامل curl لمنع تصحيح المسار محلياً |
| **05** | **LAMP** | What are the contents of the flag.txt file? | `THM{4p4ch3_p4th_tr4v3rs4l}` | علم تنفيذ الأوامر عبر mod_cgi وقراءة الملف |
| **06** | **Automation** | I have practiced that automation techniques with Nikto! | `No answer needed` | تدريب عملي على فحص البصمات بـ Nikto |
| **07** | **Conclusion** | I have successfully completed the room! | `No answer needed` | إتمام وتأكيد اجتياز المعمل بنجاح |

---

### روابط ملفات المهام التفصيلية (7-Stage Methodology Links):
* [المهمة 01: المقدمة ومنهجية فحص الأطر (TASK-01.md)](../../03-MODERN-WEB-STACKS/TASKS/TASK-01.md)
* [المهمة 02: مكدس MERN وتلويث النموذج الأولي (TASK-02.md)](../../03-MODERN-WEB-STACKS/TASKS/TASK-02.md)
* [المهمة 03: أطر عمل Next.js وتجاوز Middleware عبر CVE-2025-29927 (TASK-03.md)](../../03-MODERN-WEB-STACKS/TASKS/TASK-03.md)
* [المهمة 04: إطار عمل Django وحقن SQL في ORDER BY عبر CVE-2021-35042 (TASK-04.md)](../../03-MODERN-WEB-STACKS/TASKS/TASK-04.md)
* [المهمة 05: مكدس LAMP وثغرة Apache 2.4.49 RCE عبر CVE-2021-41773 (TASK-05.md)](../../03-MODERN-WEB-STACKS/TASKS/TASK-05.md)
* [المهمة 06: الأتمتة وفحص البصمات بواسطة Nikto (TASK-06.md)](../../03-MODERN-WEB-STACKS/TASKS/TASK-06.md)
* [المهمة 07: الخاتمة ومصفوفة المقارنة الأمنية (TASK-07.md)](../../03-MODERN-WEB-STACKS/TASKS/TASK-07.md)
