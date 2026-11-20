# Task 02: What is Intruder (ما هي أداة الدخيل وهيكلية تبويباتها)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Burp Suite Intruder is an automated fuzzing and testing suite designed to issue customized HTTP requests at scale. Testers familiar with command-line tooling often compare Intruder to utilities like **Wfuzz**, **ffuf**, or **Hydra**, with the added benefit of being fully integrated into Burp Suite's ecosystem (sharing cookie jars, upstream proxies, and scope rules).

However, it is crucial to note that **Burp Community Edition** deliberately throttles the speed of Intruder attacks. While the Professional edition can send thousands of requests per minute across multi-threaded resource pools, the Community edition introduces intentional artificial delays, making massive dictionary brute-forcing unfeasible without specialized tools or Burp Pro. Despite this limitation, Community Intruder remains exceptionally useful for smaller-scale fuzzing, parameter verification, and CTF challenges.

The Intruder interface is divided into four major configuration tabs:
1. **Positions:**
   - Used to view and edit the raw HTTP request template.
   - Used to mark the exact insertion points where payloads will be injected using section markers (`§`).
   - Used to select the **Attack type** (Sniper, Battering Ram, Pitchfork, or Cluster Bomb).
2. **Payloads:**
   - Used to configure payload sets (simple lists, numbers, brute-force character sets).
   - Used to configure payload processing rules (prefix, suffix, regex transformations) and URL-encoding options.
3. **Resource Pool:**
   - Controls concurrent request threads and throttling delays (principally utilized in Burp Professional).
4. **Settings (formerly Options):**
   - Configures operational parameters, request headers, error handling, redirect tracking (3xx), and Grep matching rules (Grep - Match, Grep - Extract) to automatically highlight flags or error states in responses.

#### الأسئلة المطروحة (Questions):
1. In which Intruder tab can we define the "Attack type" for our planned attack? (في أي تبويب من تبويبات أداة Intruder يمكننا تحديد "نوع الهجوم Attack type" لهجومنا المخطط له؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المقارنة المعمارية بين أدوات الفحص الموجهة:**
  * أدوات سطر الأوامر مثل `ffuf` تمتاز بالسرعة الخارقة في مسح الأدلة وعناوين URL لأنها مبرمجة بلغة Go ومحسنة للأداء العالي (High Throughput).
  * في المقابل، تكمن قوة **Intruder** في الفهم العميق لبروتوكول HTTP وإمكانية دمج جلسات معقدة وقواعد إدارة الجلسات (Session Handling Rules / Macros) لتجاوز قيود CSRF تلقائياً كما سنرى في التحديات المتقدمة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * راجع التبويبات الأربعة الرئيسية لأداة Intruder المذكورة في نص المهمة.
2. **التلميح الثاني (المستوى العملي):**
   * نوع الهجوم (Attack type) يرتبط مباشرة بكيفية توزيع الحمولات على المواقع (Positions) المحددة في الطلب.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * التبويب هو الذي تبدأ به لتحديد علامات `§` واختيار أحد الأنماط الأربعة: `Positions`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشريح التبويبات الأربعة في Burp Intruder:
```text
┌───────────────────────────────────────────────────────────────────────────────┐
│ BURP SUITE INTRUDER                                                           │
├───────────────┬───────────────┬───────────────┬───────────────────────────────┤
│ [POSITIONS]   │ [PAYLOADS]    │ [RESOURCE POOL│ [SETTINGS]                    │
│               │               │               │                               │
│ Attack type:  │ Payload set:  │ Concurrent: 1 │ Grep - Match                  │
│ [ Sniper ▼ ]  │ [Simple list] │ Delay: 0ms    │ Grep - Extract                │
│               │               │               │ Redirections: Follow          │
│ Request Text: │ Payload Items:│               │                               │
│ GET /api/§1§  │ 1, 2, 3...    │               │                               │
└───────────────┴───────────────┴───────────────┴───────────────────────────────┘
```

#### التحليل الوظيفي لتبويب Positions:
- يحتوي القائمة المنسدلة لاختيار نوع الهجوم (`Attack type`).
- يتيح تحديد النصوص المستهدفة وإحاطتها برمز المقطع `§` عبر زر `Add §`.
- يتيح تنظيف العلامات عبر زر `Clear §` أو الاعتماد على التخمين التلقائي لبيرب عبر `Auto §`.

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `Positions`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
1. افتح Burp Suite وانتقل إلى تبويب **Intruder**.
2. ستجد التبويب الأول بعنوان **Positions**.
3. في الجزء العلوي، ستجد بوضوح خيار القائمة المنسدلة: `Attack type: [Sniper | Battering ram | Pitchfork | Cluster bomb]`.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* تبويب **Positions** هو نقطة الانطلاق التكتيكية لكل هجوم في Intruder، حيث يحدد المختبر من خلاله جغرافية مواضع الحقن ويختار الاستراتيجية الهجومية المثلى التي تحكم كيفية توزيع الحمولات.
