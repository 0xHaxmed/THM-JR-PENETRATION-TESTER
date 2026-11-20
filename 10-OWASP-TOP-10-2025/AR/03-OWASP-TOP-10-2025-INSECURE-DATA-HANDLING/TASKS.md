# Room Tasks Overview: OWASP Top 10 2025 - Insecure Data Handling (فهرس وتفاصيل مهام غرفة معالجة البيانات غير الآمنة)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تعتبر غرفة **OWASP Top 10: 2025 - Insecure Data Handling** (`owasptopten2025three`) الغرفة الثالثة في **الوحدة العاشرة: OWASP Top 10 2025** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز الغرفة على استكشاف واستغلال ثغرات التعامل غير الآمن مع مدخلات وتدفقات البيانات الحساسة عبر ثلاثة تصنيفات رئيسية:
1. **A04: Cryptographic Failures (الإخفاقات التشفيرية):** استغلال فضاء المفاتيح المحدود والاشتقاق الضعيف على المنفذ `8001` لفك تشفير قاعدة بيانات الملاحظات السرية واستخراج راية التحدي.
2. **A05: Injection (ثغرات الحقن):** استغلال حقن القوالب على خادم الويب (Server-Side Template Injection - SSTI) في محرك Jinja2 على المنفذ `8000` لقراءة ملف `flag.txt` مباشرة من القرص الصلب.
3. **A08: Software or Data Integrity Failures (إخفاقات السلامة والتسلسل غير الآمن):** استغلال ثغرة فك التسلسل غير الآمن في مكتبة Python `pickle` على المنفذ `8002` عبر التابع السحري `__reduce__` لتنفيذ أوامر النظام وقراءة الراية.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف العملياتي (Operational Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Introduction](../../03-OWASP-TOP-10-2025-INSECURE-DATA-HANDLING/TASKS/TASK-01.md) | التعرف على أهداف الغرفة وتصنيفات معالجة البيانات | I'm ready! | `No answer needed` | ✅ منجز |
| **02** | [Task 02: A04: Cryptographic Failures](../../03-OWASP-TOP-10-2025-INSECURE-DATA-HANDLING/TASKS/TASK-02.md) | تخمين الرقم الأخير لمفتاح اشتقاق التشفير وفك الملاحظات | Decrypt the encrypted notes. One of them will contain a flag value. What is it? | `THM{WEAK_CRYPTO_FLAG}` | ✅ منجز |
| **03** | [Task 03: A05: Injection](../../03-OWASP-TOP-10-2025-INSECURE-DATA-HANDLING/TASKS/TASK-03.md) | استغلال SSTI في Jinja2 لقراءة ملف flag.txt | Perform an SSTI attack on the practical. You need to read the contents of flag.txt... | `THM{SSTI_FLAG_OBTAINED}` | ✅ منجز |
| **04** | [Task 04: A08: Software or Data Integrity Failures](../../03-OWASP-TOP-10-2025-INSECURE-DATA-HANDLING/TASKS/TASK-04.md) | توليد كائن pickle خبيث لتنفيذ الأوامر واستخراج الراية | Use Python to pickle a malicious, serialised payload that reads the contents of flag.txt... | `THM{INSECURE_DESERIALIZATION}` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Introduction & Lab Architecture](../../03-OWASP-TOP-10-2025-INSECURE-DATA-HANDLING/TASKS/TASK-01.md)
2. [TASK-02.md: A04: Cryptographic Failures (Weak Key Derivation)](../../03-OWASP-TOP-10-2025-INSECURE-DATA-HANDLING/TASKS/TASK-02.md)
3. [TASK-03.md: A05: Injection (Jinja2 SSTI Arbitrary File Read)](../../03-OWASP-TOP-10-2025-INSECURE-DATA-HANDLING/TASKS/TASK-03.md)
4. [TASK-04.md: A08: Software or Data Integrity Failures (Python Pickle Deserialization)](../../03-OWASP-TOP-10-2025-INSECURE-DATA-HANDLING/TASKS/TASK-04.md)
