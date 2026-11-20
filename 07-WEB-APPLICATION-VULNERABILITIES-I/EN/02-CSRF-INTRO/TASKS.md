# Room Tasks Overview: CSRF Introduction (فهرس وتفاصيل مهام غرفة تزوير الطلبات عبر المواقع)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تغطي هذه الغرفة العمليات الهجومية والدفاعية المتعلقة بثغرة **Cross-Site Request Forgery (CSRF)** على مسار Jr Penetration Tester في منصة TryHackMe. تتألف الغرفة من 8 مهام تفاعلية تنقل المتعلم من النظريات التأسيسية لبروتوكول HTTP وإدارة الجلسات في المتصفحات، إلى الاستغلال العملي للنماذج والرموز الضعيفة واستخراج الأعلام، ثم تطبيق أفضل الممارسات الهندسية لإغلاق هذه الثغرات في بيئات الإنتاج.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف التعليمي (Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Introduction](../../02-CSRF-INTRO/TASKS/TASK-01.md) | الاتصال ببيئة التدريب والتعرف على تطبيق StaffHub | I have successfully connected to the machine. | `No answer needed` | ✅ منجز |
| **02** | [Task 02: What is CSRF](../../02-CSRF-INTRO/TASKS/TASK-02.md) | استيعاب مفهوم الثقة وإرسال الكوكيز التلقائي | What relationship between the browser and the web app...? | `Trust` | ✅ منجز |
| | | | What does the browser automatically include...? | `Cookies` | ✅ منجز |
| **03** | [Task 03: Why CSRF Works](../../02-CSRF-INTRO/TASKS/TASK-03.md) | دراسة شروط الهجوم والإجراءات المحدثة للحالة | What type of action is usually required...? | `State-changing` | ✅ منجز |
| **04** | [Task 04: Finding CSRF Vulnerabilities](../../02-CSRF-INTRO/TASKS/TASK-04.md) | دحض مغالطة طريقة POST وفهم رموز الحماية | What HTTP request method do many developers incorrectly believe...? | `POST` | ✅ منجز |
| | | | What mechanism is commonly used to protect...? | `CSRF tokens` | ✅ منجز |
| **05** | [Task 05: Exploitation using HTML Form](../../02-CSRF-INTRO/TASKS/TASK-05.md) | بناء صفحة استغلال خارجية وتغيير البريد الإلكتروني | Flag value after updating email to attacker@evilmail.thm | `THM{Got_The_Evil_Email001}` | ✅ منجز |
| | | | Flag value after updating email to special@evilmail.thm | `THM{My_Special_Email007}` | ✅ منجز |
| **06** | [Task 06: Exploitation over Weak Tokens](../../02-CSRF-INTRO/TASKS/TASK-06.md) | كسر الرموز الضعيفة القابلة للتنبؤ وتخفيض الرتبة | Flag value after demoting user from admin to staff | `THM{Weak_CSRF_Role_001}` | ✅ منجز |
| | | | Name of the encoding scheme used by developer | `base64` | ✅ منجز |
| **07** | [Task 07: Best Practices](../../02-CSRF-INTRO/TASKS/TASK-07.md) | منهجية الفحص والتدقيق الدفاعي وسمات الكوكيز | I have understood the best practices. | `No answer needed` | ✅ منجز |
| **08** | [Task 08: Conclusion](../../02-CSRF-INTRO/TASKS/TASK-08.md) | المراجعة الشاملة والانتقال للموضوع التالي | I have successfully completed the room. | `No answer needed` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Introduction & Lab Connectivity](../../02-CSRF-INTRO/TASKS/TASK-01.md)
2. [TASK-02.md: What is CSRF (Trust & Ambient Credentials)](../../02-CSRF-INTRO/TASKS/TASK-02.md)
3. [TASK-03.md: Why CSRF Works (State-Changing Invariants)](../../02-CSRF-INTRO/TASKS/TASK-03.md)
4. [TASK-04.md: Finding CSRF Vulnerabilities (POST Fallacy & Tokens)](../../02-CSRF-INTRO/TASKS/TASK-04.md)
5. [TASK-05.md: Exploitation using HTML Form (Auto-Submit Exploitation)](../../02-CSRF-INTRO/TASKS/TASK-05.md)
6. [TASK-06.md: Exploitation over Weak Tokens (Base64 Reversal & GET CSRF)](../../02-CSRF-INTRO/TASKS/TASK-06.md)
7. [TASK-07.md: Best Practices & Pentesting Methodology](../../02-CSRF-INTRO/TASKS/TASK-07.md)
8. [TASK-08.md: Conclusion & Learning Path Continuation](../../02-CSRF-INTRO/TASKS/TASK-08.md)
