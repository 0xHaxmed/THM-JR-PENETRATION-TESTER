# Room Tasks Overview: Intro to Cross-site Scripting (فهرس وتفاصيل مهام غرفة ثغرات XSS)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تُعد غرفة **Intro to Cross-site Scripting** من أكثر الغرف تأسيسية وأهمية في مسار مختبر الاختراق المبتدئ (Jr Penetration Tester) على منصة TryHackMe. تتألف الغرفة من 8 مهام تفاعلية تغطي النطاق الكامل لثغرات حقن الجافاسكريبت عبر المواقع، بدءاً من التعريف الأساسي وفهم بنية الحمولات (Payloads)، مروراً بالأنواع الرئيسية الثلاثة (Reflected, Stored, DOM-Based) والنوع المتقدم (Blind XSS)، وصولاً إلى مختبر تكييف وتجاوز الفلاتر عبر 6 مستويات، والسيناريو العملي الكامل لاختراق جلسة موظف دعم فني وسرقة الكوكيز.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف التعليمي (Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Room Brief](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-01.md) | مقدمة الغرفة ومكافآت الثغرات الواقعية | What does XSS stand for? | `Cross-Site Scripting` | ✅ منجز |
| **02** | [Task 02: XSS Payloads](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-02.md) | بنية الحمولات: النية والتعديل وسرقة الجلسات | Which document property could contain user's session token? | `document.cookie` | ✅ منجز |
| | | | Which JavaScript method is often used as a PoC? | `alert` | ✅ منجز |
| **03** | [Task 03: Reflected XSS](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-03.md) | البرمجة النصية المنعكسة ونقاط الدخول في الروابط | Where in an URL is a good place to test for reflected XSS? | `parameters` | ✅ منجز |
| **04** | [Task 04: Stored XSS](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-04.md) | البرمجة النصية المخزنة ومكان حفظ البيانات في الخادم | How are stored XSS payloads usually stored on a website? | `database` | ✅ منجز |
| **05** | [Task 05: DOM Based XSS](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-05.md) | نموذج كائن المستند ودوال التنفيذ غير الآمنة | What unsafe JavaScript method is good to look for in source code? | `eval()` | ✅ منجز |
| **06** | [Task 06: Blind XSS](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-06.md) | البرمجة النصية العمياء واستقبال الإشارات الخارجية | What tool can you use to test for Blind XSS? | `XSS Hunter Express` | ✅ منجز |
| | | | What type of XSS is very similar to Blind XSS? | `Stored XSS` | ✅ منجز |
| **07** | [Task 07: Perfecting your payload](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-07.md) | كسر السياقات وتخطي الفلاتر عبر 6 مستويات متصاعدة | What is the flag you received from level six? | `THM{XSS_MASTER}` | ✅ منجز |
| **08** | [Task 08: Practical Example (Blind XSS)](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-08.md) | سرقة كوكيز موظف الدعم الفني في Acme IT Support | What is the value of the staff-session cookie? | `4AB305E55955197693F01D6F8FD2D321` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Room Brief & Precedents](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-01.md)
2. [TASK-02.md: XSS Payloads & Intentions](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-02.md)
3. [TASK-03.md: Reflected XSS & URL Parameters](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-03.md)
4. [TASK-04.md: Stored XSS & Database Persistence](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-04.md)
5. [TASK-05.md: DOM Based XSS & Unsafe Sinks (eval)](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-05.md)
6. [TASK-06.md: Blind XSS & OOB Callback Tools](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-06.md)
7. [TASK-07.md: Perfecting your Payload (6 Levels & Master Flag)](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-07.md)
8. [TASK-08.md: Practical Example (Netcat Exfiltration & Staff Cookie)](file:///e:/THM-JR-PENETRATION-TESTER/07-WEB-APPLICATION-VULNERABILITIES-I/03-XSS-INTRO/TASKS/TASK-08.md)
