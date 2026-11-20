# Task 03: The BApp Store (متجر إضافات بيرب وتثبيت أداة قياس زمن الاستجابة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **BApp Store** (Burp App Store) is the official repository and marketplace for extensions curated and verified by PortSwigger and the global cybersecurity research community.

#### Key Features of the BApp Store:
- **Centralized Catalog:** Hundreds of open-source and commercial extensions categorized by functionality, rating, and language (Java or Python).
- **One-Click Installation:** Selecting any extension and clicking **Install** automatically downloads, verifies, and activates the extension in your Burp environment.
- **Popular Community Extensions:**
  - **Autorize:** Automated authorization matrix testing and IDOR detection.
  - **Turbo Intruder:** High-throughput asynchronous HTTP engine (up to 30,000 requests/sec) based on Spike.
  - **Logger++:** Advanced multithreaded logging and regex filtering across all Burp modules.
  - **Software Vulnerability Scanner:** Passive scanning of outdated software libraries and CVEs.

#### Practical Walkthrough: Installing "Request Timer":
1. Open the **Extensions -> BApp Store** tab.
2. In the search bar or list, locate **Request Timer** (authored by Nick Taylor).
3. Review the extension details: Request Timer logs the exact round-trip response latency for every HTTP interaction passing through Burp.
4. Click **Install**.
5. Once installed, observe that Burp Suite creates a new top-level tab titled **Request Timer**, allowing you to monitor latency distributions and identify potential time-based blind SQLi or username enumeration timing attacks.

#### الأسئلة المطروحة (Questions):
1. Click me to proceed to the next task. (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **القيمة التكتيكية لإضافة Request Timer في الهجمات المعتمدة على الوقت (Timing Attacks):**
  * في كثير من الأحيان، تفشل تقنيات فحص الأخطاء (Error-based) أو الانعكاس (Reflected) في كشف ثغرات SQLi. يلجأ المختبر حينها إلى دوال التأخير الزمني (مثل `pg_sleep(5)` أو `WAITFOR DELAY '0:0:5'`).
  * تتيح إضافة مثل **Request Timer** رسم بياني فوري لقياس استجابة الخادم بالمللي ثانية، مما يفصل بين تقلبات الشبكة الطبيعية وتأخيرات الخادم الناتجة عن تنفيذ الحمولات الخبيثة بنجاح.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة توضح كيفية تثبيت الإضافات الرسمية من المتجر بنقرة زر واحدة.
2. **التلميح الثاني (المستوى العملي):**
   * اضغط على زر Submit للاستمرار إلى مهمة ضبط بيئة بايثون (Jython).

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### خطوات تثبيت الإضافات عبر BApp Store:
```text
[ Extensions Tab ] ──▶ [ BApp Store Sub-tab ] ──▶ Search: "Request Timer"
                                                         │
                                                         ▼
                                                  Click [ Install ]
                                                         │
                                                         ▼
                                      [ PortSwigger Downloads & Verifies JAR ]
                                                         │
                                                         ▼
                                        New Tab Created: [ Request Timer ]
```

#### تصنيف أنواع الإضافات الشائعة في BApp Store:
| اسم الإضافة (Extension) | اللغة (Language) | الوظيفة التكتيكية الأساسية (Tactical Purpose) |
| :--- | :---: | :--- |
| **Request Timer** | Java | قياس زمن استجابة الخادم بدقة بالغة لاكتشاف ثغرات Timing Attacks. |
| **Autorize** | Python | اختبار صلاحيات الوصول وتخطي التفويض وتدقيق IDOR آلياً. |
| **Turbo Intruder** | Java/Python | هجمات التخمين عالية السرعة والسباق الزمني (Race Conditions). |
| **JSON Web Tokens** | Java | فحص وتعديل وتوقيع توكنات JWT واختبار ثغرات التوقيع المشوه. |

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد فهم آلية التثبيت من BApp Store واعتماد المهمة بنجاح 100% في منصة TryHackMe.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* يوفر **BApp Store** وصولاً مركزياً موثوقاً لمئات الإضافات التكتيكية التي توسع قدرات Burp وتوفر حلولاً جاهزة لأعقد سيناريوهات اختبار الاختراق.
