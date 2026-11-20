# Room Tasks Overview: Hydra (فهرس وتفاصيل مهام غرفة هيدرا)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تعتبر غرفة **Hydra** (`hydra`) الغرفة التطبيقية الثانية في **الوحدة الحادية عشرة: هجمات كلمات المرور (Module 11: Password Attacks)** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز الغرفة على إتقان أداة التخمين الشبكي الأسرع عالمياً (THC-Hydra) لشن هجمات القوة الغاشمة والمعجمية المباشرة (Online Brute-Forcing) عبر بروتوكولات وتطبيقات متعددة:
1. **استيعاب معمارية Hydra (Task 01):** فهم آلية عمل الأداة وقدرتها على دعم أكثر من 50 بروتوكولاً مختلفاً (SSH, Web, FTP, RDP, Databases, Mail).
2. **التخمين على استمارات الويب (Task 02 - Web):** استهداف استمارة HTTP POST Form وتحديد مسار الصفحة ومعاملات الحقول وشرط الفشل (`F=incorrect`) لاقتناص الراية الأولى: `THM{2673a7dd116de68e85c48ec0b1f2612e}`.
3. **التخمين على بروتوكول SSH (Task 02 - SSH):** ضبط معاملات التوازي (`-t 4`) لتجاوز قيود خادم OpenSSH واكتشاف كلمة مرور المستخدمة `molly`، والاتصال بالنظام لاستخراج الراية الثانية: `THM{c8eeb0468febbadea859baeb33b2541b}`.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف العملياتي (Operational Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Hydra Introduction](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/02-HYDRA/TASKS/TASK-01.md) | التعرف على قدرات الأداة والبروتوكولات المدعومة | Read the above and have Hydra at the ready. | `No answer needed` | ✅ منجز |
| **02** | [Task 02: Using Hydra](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/02-HYDRA/TASKS/TASK-02.md) | التخمين على استمارة الويب وخدمة SSH لاستخراج الرايات | Use Hydra to brute-force molly's web password. What is the value of flag 1? | `THM{2673a7dd116de68e85c48ec0b1f2612e}` | ✅ منجز |
| | | | Use Hydra to brute-force molly's SSH password. What is the value of flag 2? | `THM{c8eeb0468febbadea859baeb33b2541b}` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Hydra Introduction & Architecture](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/02-HYDRA/TASKS/TASK-01.md)
2. [TASK-02.md: Using Hydra (Web Form & SSH Exploitation)](file:///e:/THM-JR-PENETRATION-TESTER/11-PASSWORD-ATTACKS/02-HYDRA/TASKS/TASK-02.md)
