# Room Tasks Overview: Introduction to Wordlists (فهرس وتفاصيل مهام غرفة مقدمة في القوائم المعجمية)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تعتبر غرفة **Introduction to Wordlists** (`introductiontowordlists`) الغرفة التطبيقية الثالثة في **الوحدة الحادية عشرة: هجمات كلمات المرور (Module 11: Password Attacks)** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز الغرفة على استكشاف وتطبيق دورة العمليات الكاملة لهندسة وتخصيص وتطهير القوائم المعجمية لاختراق الأنظمة:
1. **استيعاب دور القوائم وأداة Crunch (Task 02):** فهم استخدامات القوائم عبر كافة أطوار اختبار الاختراق واستخدام Crunch لتوليد قواميس تعتمد على الأنماط.
2. **استطلاع وجمع بيانات الهدف المخصصة (Task 03):** استخدام CeWL لتجريف المواقع، و Wget لسحب ملفات الـ PDF، واستخراج العناوين البريدية وصيغ أسماء المستخدمين عبر Regex و Awk.
3. **تطهير وتنقية القوائم وتوليد الأقنعة (Task 04):** تصفية القوائم بحذف التكرارات (`sort -u`) وتوحيد حالة الأحرف لتصل إلى 161 كلمة، واستخدام Crunch لتوليد نمط `Helios20%%!` (الكلمة العاشرة: `Helios2009!`).
4. **التطبيق الهجومي الميداني (Task 05):** استكشاف مسار الإدارة الداخلي `helios/` (رمز الحالة 200) عبر ffuf، والتخمين على بوابته عبر Hydra لاختراق حساب `alex.johnson` واستخراج الراية: `THM{w0rdlists_win_rooms}`.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف العملياتي (Operational Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Introduction](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-01.md) | التعرف على أهداف الغرفة ودورة هندسة القوائم | I’m ready to learn about wordlists! | `No answer needed` | ✅ منجز |
| **02** | [Task 02: Wordlists](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-02.md) | تحديد أداة توليد القوائم المعجمية بالأقنعة | What tool can generate a custom wordlist based on known patterns? | `crunch` | ✅ منجز |
| **03** | [Task 03: Gathering Information for Custom Wordlists](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-03.md) | تجريف الويب وسحب المستندات واستخراج العناوين | ...how many unique email addresses did you scrape? | `4` | ✅ منجز |
| | | | How many PDF files were you able to find? | `1` | ✅ منجز |
| | | | How many email addresses... from the PDF files? | `4` | ✅ منجز |
| | | | What is the last username in the users_from_emails.txt file? | `security` | ✅ منجز |
| **04** | [Task 04: Creating and Cleaning Wordlists](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-04.md) | تطهير القواميس وتوليد كلمات المرور بالأقنعة | How many words are in words_clean.txt after cleaning up...? | `161` | ✅ منجز |
| | | | What is the 10th password in the pass_helios.txt file? | `Helios2009!` | ✅ منجز |
| | | | What flag does sort use to remove duplicate lines? | `-u` | ✅ منجز |
| | | | What crunch template character represents a digit? | `%` | ✅ منجز |
| **05** | [Task 05: Using Your Wordlist](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-05.md) | استكشاف المسارات عبر ffuf واختراق حساب أليكس عبر Hydra | What HTTP status code does ffuf report for helios/ ? | `200` | ✅ منجز |
| | | | What is the username that Hydra discovers? | `alex.johnson` | ✅ منجز |
| | | | What is the flag? | `THM{w0rdlists_win_rooms}` | ✅ منجز |
| **06** | [Task 06: Conclusion](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-06.md) | تلخيص المهارات والانتقال لكسر التجزئات | I got all my wordlists sharp and ready. | `No answer needed` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Introduction & Educational Scope](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-01.md)
2. [TASK-02.md: Wordlists (Applications & Crunch Tool)](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-02.md)
3. [TASK-03.md: Gathering Information (CeWL, PDF Scraping & Regex)](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-03.md)
4. [TASK-04.md: Creating & Cleaning (Deduplication & Crunch Masks)](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-04.md)
5. [TASK-05.md: Using Your Wordlist (Ffuf Fuzzing & Hydra Exploitation)](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-05.md)
6. [TASK-06.md: Conclusion & Synthesis](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/03-INTRODUCTION-TO-WORDLISTS/TASKS/TASK-06.md)
