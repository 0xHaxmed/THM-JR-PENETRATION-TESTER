# Room Tasks Overview: OWASP Top 10 2025 - IAAA Failures (فهرس وتفاصيل مهام غرفة إخفاقات الهوية والمصادقة والمساءلة)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تعتبر غرفة **OWASP Top 10: 2025 - IAAA Failures** (`owasptopten2025one`) الغرفة الأولى التأسيسية ضمن **الوحدة العاشرة: OWASP Top 10 2025** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز هذه الغرفة على تفكيك ودراسة إخفاقات التطبيق العملي لمنظومة الأمان الرباعية **IAAA** (Identity, Authentication, Authorisation, Accountability):
1. **استيعاب تسلسل نموذج IAAA:** الهوية تسبق المصادقة، والمصادقة تسبق التفويض، والمساءلة ترصد وتوثق كافة العمليات لمنع الإنكار.
2. **A01: Broken Access Control:** استغلال ثغرات الإسناد المباشر غير الآمن للكائنات (IDOR) لتنفيذ تصعيد صلاحيات أفقي (Horizontal Privilege Escalation) واختراق بيانات مستخدمين آخرين للحصول على راية المليونير.
3. **A07: Authentication Failures:** استغلال عيوب المطابقة غير الحساسة لحالة الأحرف (Case-Insensitive Account Collision) لتوليد تضارب في هوية الحسابات (Account Confusion) والاستيلاء على لوحة تحكم المدير `admin`.
4. **A09: Logging & Alerting Failures:** تنفيذ تحريات جنائية رقمية في سجلات الويب (Web Access Logs) لتحديد عنوان IP المهاجم المستخدم في هجوم التخمين العنيف، واسم المستخدم المخترق، والمسار الإداري السري المخترق.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف العملياتي (Operational Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Introduction](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-01.md) | التعرف على أهداف الغرفة وتصنيفات IAAA المشمولة | I am ready to learn about IAAA failures! | `No answer needed` | ✅ منجز |
| **02** | [Task 02: What does IAAA Stand For?](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-02.md) | فهم ركائز منظومة الأمان الرباعية وتسلسلها | What does IAAA stand for? | `Identity, Authentication, Authorisation, Accountability` | ✅ منجز |
| **03** | [Task 03: A01: Broken Access Control](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-03.md) | استغلال ثغرة IDOR وتصعيد الصلاحيات الأفقي | What type of privilege escalation describes viewing another user's data with the same permission level? | `Horizontal` | ✅ منجز |
| | | | What is the flag from the millionaire user's note? | `THM{Found.the.Millionare!}` | ✅ منجز |
| **04** | [Task 04: A07: Authentication Failures](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-04.md) | استغلال Account Confusion بالاسم `aDmiN` | What is the flag displayed on the admin user's dashboard? | `THM{Account.confusion.FTW!}` | ✅ منجز |
| **05** | [Task 05: A09: Logging & Alerting Failures](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-05.md) | التحقيق الجنائي الرقمي في سجلات خادم الويب | What is the IP address of the attacker performing the brute-force attack? | `203.0.113.45` | ✅ منجز |
| | | | Which username was compromised during the attack? | `admin` | ✅ منجز |
| | | | What sensitive admin endpoint was accessed by the attacker? | `/supersecretadminstuff` | ✅ منجز |
| **06** | [Task 06: Conclusion](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-06.md) | تلخيص ركائز الدفاع وخاتمة الغرفة | I understand the importance of a secure IAAA implementation in my application! | `No answer needed` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Introduction](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-01.md)
2. [TASK-02.md: What does IAAA Stand For?](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-02.md)
3. [TASK-03.md: A01: Broken Access Control (IDOR & Horizontal Escalation)](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-03.md)
4. [TASK-04.md: A07: Authentication Failures (Account Confusion)](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-04.md)
5. [TASK-05.md: A09: Logging & Alerting Failures (Forensic Web Log Analysis)](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-05.md)
6. [TASK-06.md: Conclusion & Defensive Architecture](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/01-OWASP-TOP-10-2025-IAAA-FAILURES/TASKS/TASK-06.md)
