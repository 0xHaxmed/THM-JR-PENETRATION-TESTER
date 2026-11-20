# Room Tasks Overview: OWASP Top 10 2025 - Application Design Flaws (فهرس وتفاصيل مهام غرفة عيوب التصميم المعماري)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تعتبر غرفة **OWASP Top 10: 2025 - Application Design Flaws** (`owasptopten2025two`) الغرفة الثانية التأسيسية ضمن **الوحدة العاشرة: OWASP Top 10 2025** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز الغرفة على استكشاف واستغلال الثغرات الناتجة عن القصور في التصميم المعماري للأنظمة والتهيئة الأمنية الخاطئة، وتغطي أربعة تصنيفات رئيسية:
1. **AS02: Security Misconfigurations (التهيئة الأمنية الخاطئة):** استغلال رسائل الخطأ التفصيلية (Verbose Error Stack Traces) على المنفذ `5002` لاستخراج المتغيرات الداخلية وتسريب راية التحدي.
2. **AS03: Software Supply Chain Failures (إخفاقات سلاسل التوريد):** فحص مكتبة خارجية قديمة ومستوردة (`lib/vulnerable_utils.py`) واستغلال معامل التصحيح الخفي عبر مسار `/api/process` على المنفذ `5003` لاسترجاع الراية.
3. **AS04: Cryptographic Failures (الإخفاقات التشفيرية):** استخراج مفتاح تشفير رئيسي صلب (Hard-coded Master Key) تم تضمينه في كود التطبيق على المنفذ `5004`، واستخدامه لفك تشفير البيانات المحمية.
4. **AS06: Insecure Design (التصميم غير الآمن):** تفكيك الافتراض المعماري الخاطئ الذي يثق في ترويسات العميل، وتزييف ترويسة `User-Agent` لمحاكاة هاتف محمول على المنفذ `5005` لتجاوز قيود الوصول.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف العملياتي (Operational Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Introduction](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-01.md) | التعرف على أهداف الغرفة وتصنيفات التصميم المعماري | I am ready to learn about design flaw vulnerabilities! | `No answer needed` | ✅ منجز |
| **02** | [Task 02: AS02: Security Misconfigurations](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-02.md) | استغلال رسائل الخطأ التفصيلية في User API | What's the flag? | `THM{V3RB0S3_3RR0R_L34K}` | ✅ منجز |
| **03** | [Task 03: AS03: Software Supply Chain Failures](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-03.md) | استغلال ميزة التصحيح في مكتبة قديمة مستوردة | What's the flag? | `THM{SUPPLY_CH41N_VULN3R4B1L1TY}` | ✅ منجز |
| **04** | [Task 04: AS04: Cryptographic Failures](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-04.md) | استخراج مفتاح التشفير الصلب وفك تشفير البيانات | What's the flag? | `THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}` | ✅ منجز |
| **05** | [Task 05: AS06: Insecure Design](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-05.md) | تزييف ترويسة User-Agent لتجاوز قيود الهواتف المحمولة | What's the flag? | `THM{1NS3CUR3_D35IGN_4SSUMPT10N}` | ✅ منجز |
| **06** | [Task 06: Conclusion](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-06.md) | تلخيص مبادئ التصميم الآمن والانتقال للغرفة 03 | I'm ready for the next room! | `No answer needed` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Introduction](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-01.md)
2. [TASK-02.md: AS02: Security Misconfigurations (Verbose Error Disclosure)](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-02.md)
3. [TASK-03.md: AS03: Software Supply Chain Failures (Vulnerable Dependency)](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-03.md)
4. [TASK-04.md: AS04: Cryptographic Failures (Hard-coded Keys)](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-04.md)
5. [TASK-05.md: AS06: Insecure Design (Untrusted Client Assumptions)](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-05.md)
6. [TASK-06.md: Conclusion & Architectural Remediation](file:///e:/THM-JR-PENETRATION-TESTER/10-OWASP-TOP-10-2025/02-OWASP-TOP-10-2025-APPLICATION-DESIGN-FLAWS/TASKS/TASK-06.md)
