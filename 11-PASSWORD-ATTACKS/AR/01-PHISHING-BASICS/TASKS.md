# Room Tasks Overview: Phishing Basics (فهرس وتفاصيل مهام غرفة أساسيات التصيد الاحتيالي)

---

## 📌 Executive Summary (الملخص التنفيذي للمهام)

تعتبر غرفة **Phishing Basics** (`phishingbasics`) الغرفة التأسيسية الأولى في **الوحدة الحادية عشرة: هجمات كلمات المرور (Module 11: Password Attacks)** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز الغرفة على استكشاف واستغلال ثغرات العنصر البشري من خلال الهندسة الاجتماعية والتصيد الاحتيالي، وتغطي المحاور الخمسة التالية:
1. **المفاهيم والتصنيفات (Phishing 101):** التمييز بين قنوات الهجوم المختلفة (SMS لـ Smishing، الصوت لـ Vishing) وفهم استهداف القيادات العليا (Whaling).
2. **سيكولوجية التصيد (Psychology):** دراسة الركائز النفسية الأساسية (الندرة، الإلحاح، السلطة، الخوف، الفضول، الثقة) والانحيازات المعرفية.
3. **التقنيات الفنية (Technical Tradecraft):** دراسة انتحال النطاقات بالأخطاء الإملائية (Typosquatting)، وهجمات التجانس البصري (Homograph)، وبروتوكولات حماية البريد التشفيرية الثلاثية (`DKIM, DMARC, SPF`).
4. **مقاييس وإدارة الحملات (Campaign Lifecycle):** تحليل معدلات النقر وإدخال البيانات (مثل تصنيف 6% كـ High risk)، وتحديد التوصيات الأمنية المؤسسية.
5. **المختبر التطبيقي العملي (SET Lab):** محاكاة هجوم تصيد موجه ضد رئيس الحسابات (Bob Wilkinson) باستخدام The Social Engineering Toolkit وعميل البريد Rainloop، واقتناص راية كلمة المرور.

---

## 📋 Task Index & Solutions Matrix (جدول فهرس المهام والحلول)

| رقم المهمة (Task #) | عنوان المهمة (Task Title) | الهدف العملياتي (Operational Objective) | نوع السؤال / السؤال المطلوب | الإجابة المعتمدة (Accepted Answer) | حالة الإنجاز |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **01** | [Task 01: Introduction](../../01-PHISHING-BASICS/TASKS/TASK-01.md) | التعرف على أهداف الغرفة ودور التصيد في اختبار الاختراق | Are you ready? | `No answer needed` | ✅ منجز |
| **02** | [Task 02: Phishing 101](../../01-PHISHING-BASICS/TASKS/TASK-02.md) | التمييز بين قنوات التصيد واستهداف الرؤساء التنفيذيين | What is the primary channel used during a smishing attack? | `sms` | ✅ منجز |
| | | | You are a CEO and have just received a phishing email... | `Whaling` | ✅ منجز |
| **03** | [Task 03: Psychology of Phishing](../../01-PHISHING-BASICS/TASKS/TASK-03.md) | ربط السيناريوهات بالمبادئ النفسية الستة | ...offer for the new iPhone will expire in 24 hours... | `Urgency` | ✅ منجز |
| | | | An executive requests sensitive data via email... | `Authority` | ✅ منجز |
| | | | ...promising exclusive access to a new product... | `Curiosity` | ✅ منجز |
| | | | ...account credentials were found in a recent data breach... | `Fear` | ✅ منجز |
| **04** | [Task 04: Phishing Techniques](../../01-PHISHING-BASICS/TASKS/TASK-04.md) | تقنيات النطاقات المنحولة وحماية ترويسات البريد | Which technique relies on users making a typo? | `Typosquatting` | ✅ منجز |
| | | | Which three security measures help organisations defend against email spoofing? | `DKIM, DMARC, SPF` | ✅ منجز |
| **05** | [Task 05: Anatomy of a Phishing Campaign](../../01-PHISHING-BASICS/TASKS/TASK-05.md) | تقييم مقاييس الأداء وتحديد التوصيات الأمنية | Your campaign shows a credential entry rate of 6%... | `High risk` | ✅ منجز |
| | | | Which metric measures the percentage of users who open an attachment? | `Attachment Detonation Rate` | ✅ منجز |
| | | | A client has a click rate of 10%. Which single recommendation... | `Focused security awareness training` | ✅ منجز |
| **06** | [Task 06: The Social Engineering Toolkit](../../01-PHISHING-BASICS/TASKS/TASK-06.md) | استنساخ البوابة وانتحال الدعم الفني لاصطياد كلمة مرور بوب | What is the password flag? | `THM{you_just_got_phished!}` | ✅ منجز |
| **07** | [Task 07: Conclusion](../../01-PHISHING-BASICS/TASKS/TASK-07.md) | تلخيص المفاهيم والانتقال لغرفة Hydra | Well done on completing this room!... | `No answer needed` | ✅ منجز |

---

## 🎯 Quick Navigation to Detailed Task Files (روابط الوصول المباشر لملفات المهام)

1. [TASK-01.md: Introduction & Educational Scope](../../01-PHISHING-BASICS/TASKS/TASK-01.md)
2. [TASK-02.md: Phishing 101 (Mediums & Whaling Taxonomy)](../../01-PHISHING-BASICS/TASKS/TASK-02.md)
3. [TASK-03.md: Psychology of Phishing (Social Engineering Levers)](../../01-PHISHING-BASICS/TASKS/TASK-03.md)
4. [TASK-04.md: Phishing Techniques (Typosquatting, SPF, DKIM, DMARC)](../../01-PHISHING-BASICS/TASKS/TASK-04.md)
5. [TASK-05.md: Anatomy of a Phishing Campaign (Metrics & RoE)](../../01-PHISHING-BASICS/TASKS/TASK-05.md)
6. [TASK-06.md: The Social Engineering Toolkit (SET Credential Harvesting Lab)](../../01-PHISHING-BASICS/TASKS/TASK-06.md)
7. [TASK-07.md: Conclusion & Defensive Architecture](../../01-PHISHING-BASICS/TASKS/TASK-07.md)
