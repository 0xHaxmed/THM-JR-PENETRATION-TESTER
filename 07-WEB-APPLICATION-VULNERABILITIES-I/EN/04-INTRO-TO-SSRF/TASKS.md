# Room Tasks Overview: Intro to SSRF (فهرس وتفاصيل مهام غرفة ثغرات SSRF)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تُغطي غرفة **Intro to SSRF** في مسار Jr Penetration Tester على منصة TryHackMe الأسس النظرية والتطبيقية لثغرة **Server-Side Request Forgery**. تتألف الغرفة من 5 مهام مكثفة تشمل التعريف النظري بالثغرة ونوعيها (العادي والأعمى)، وأنماط استيعاب الروابط، وطرق اكتشاف واستكشاف معالم الشبكة الداخلية، وأساليب كسر وتخطي القوائم السوداء والبيضاء والتوجيه المفتوح، وصولاً إلى التحدي العملي لاختراق لوحة `/private` عبر تجاوز فلاتر البادئة وتسوية المسارات (Path Traversal).

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف التعليمي (Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Introduction](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-01.md) | المفاهيم الأساسية والأنواع (عادي وأعمى) | What does SSRF stand for? | `Server-Side Request Forgery` | ✅ منجز |
| | | | As opposed to a regular SSRF, what is the other type? | `Blind` | ✅ منجز |
| **02** | [Task 02: SSRF Examples](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-02.md) | أنماط المعاملات وتحييد المسارات الإضافية بـ `&x=` | What is the flag from the SSRF Examples site? | `THM{SSRF_MASTER}` | ✅ منجز |
| **03** | [Task 03: Finding an SSRF](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-03.md) | استكشاف الميزات عالية الخطورة وتحديد المعاملات | Which of the following URLs is more likely vulnerable? | `3` | ✅ منجز |
| **04** | [Task 04: Defeating Common SSRF Defenses](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-04.md) | تخطي القوائم وعنوان السحابة الحساس والتوجيه المفتوح | Method used to bypass strict rules? | `Open Redirect` | ✅ منجز |
| | | | IP address containing sensitive data in cloud? | `169.254.169.254` | ✅ منجز |
| | | | Type of list used to permit only certain input? | `Allow List` | ✅ منجز |
| | | | Type of list used to stop certain input? | `Deny List` | ✅ منجز |
| **05** | [Task 05: SSRF Practical](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-05.md) | التطبيق العملي: كسر فلاتر البادئة واستخراج العلم | What is the flag from the /private directory? | `THM{YOU_WORKED_OUT_THE_SSRF}` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Introduction & Core Taxonomy](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-01.md)
2. [TASK-02.md: SSRF Examples & URL Syntax Neutralization](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-02.md)
3. [TASK-03.md: Finding an SSRF & High-Risk Surface Mapping](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-03.md)
4. [TASK-04.md: Defeating Common SSRF Defenses (Lists & Metadata)](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-04.md)
5. [TASK-05.md: SSRF Practical (Acme IT Support Traversal & Data URI)](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/04-INTRO-TO-SSRF/TASKS/TASK-05.md)
