# Room Tasks Overview: IDOR (فهرس وتفاصيل مهام غرفة ثغرات المرجع المباشر غير الآمن للكائنات)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تُعد غرفة **IDOR (Insecure Direct Object Reference)** في مسار Jr Penetration Tester على منصة TryHackMe من أهم الغرف المتخصصة في كسر آليات التحكم في الوصول (Broken Access Control - OWASP A01 / API1). تتألف الغرفة من 7 مهام تفاعلية تنقل الدارس من فهم المفاهيم الأساسية، وأنماط المعرفات المختلفة (الصريحة، المرمزة بـ Base64، المبتورة بـ MD5، والعشوائية كـ UUID)، ومنهجية الفحص بالحسابين (Two-Account Technique)، وصولاً إلى فحص حركة الشبكة الخلفية واستغلال ثغرة IDOR في واجهات البرمجة (REST API) واستخراج بيانات المستخدمين الآخرين.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف التعليمي (Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: What is an IDOR?](../../05-IDOR/TASKS/TASK-01.md) | التعريف النظري وتصنيف الثغرة ضمن OWASP A01 / BOLA | What does IDOR stand for? | `Insecure Direct Object Reference` | ✅ منجز |
| **02** | [Task 02: An IDOR Example](../../05-IDOR/TASKS/TASK-02.md) | استغلال المعرفات الصريحة وتعديل بيانات الملف الشخصي | What is the Flag from the IDOR example website? | `THM{IDOR-VULN-FOUND}` | ✅ منجز |
| **03** | [Task 03: Finding IDORs in Encoded IDs](../../05-IDOR/TASKS/TASK-03.md) | التعامل مع المعرفات المرمزة وفك وإعادة تشفير القيم | What is a common type of encoding used by websites? | `base64` | ✅ منجز |
| **04** | [Task 04: Finding IDORs in Hashed IDs](../../05-IDOR/TASKS/TASK-04.md) | المعرفات المعالجة بدوال الهاش وهجمات الجداول المسبقة | What is a common algorithm used for hashing IDs? | `md5` | ✅ منجز |
| **05** | [Task 05: Finding IDORs in Unpredictable IDs](../../05-IDOR/TASKS/TASK-05.md) | المعرفات العشوائية UUID ومنهجية الفحص بحسابين | Minimum number of accounts needed to check for IDORs? | `2` | ✅ منجز |
| **06** | [Task 06: Where are IDORs located](../../05-IDOR/TASKS/TASK-06.md) | مواقع ظهور الثغرة في الطلبات الخلفية وتعدين المعاملات | Read the above. | `No answer needed` | ✅ منجز |
| **07** | [Task 07: A Practical IDOR Example](../../05-IDOR/TASKS/TASK-07.md) | التطبيق العملي الكامل وسحب بيانات المستخدمين 1 و 3 | What is the username for user id 1? | `adam84` | ✅ منجز |
| | | | What is the email address for user id 3? | `j@fakemail.thm` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: What is an IDOR? (Core Theory & BOLA Classification)](../../05-IDOR/TASKS/TASK-01.md)
2. [TASK-02.md: An IDOR Example (Plaintext Parameter Tampering)](../../05-IDOR/TASKS/TASK-02.md)
3. [TASK-03.md: Finding IDORs in Encoded IDs (Base64 Transformation)](../../05-IDOR/TASKS/TASK-03.md)
4. [TASK-04.md: Finding IDORs in Hashed IDs (MD5 & Predictable Inputs)](../../05-IDOR/TASKS/TASK-04.md)
5. [TASK-05.md: Finding IDORs in Unpredictable IDs (UUIDs & Two-Account Method)](../../05-IDOR/TASKS/TASK-05.md)
6. [TASK-06.md: Where are IDORs located (Background APIs & Mining)](../../05-IDOR/TASKS/TASK-06.md)
7. [TASK-07.md: A Practical IDOR Example (API Exploitation & Data Extraction)](../../05-IDOR/TASKS/TASK-07.md)
