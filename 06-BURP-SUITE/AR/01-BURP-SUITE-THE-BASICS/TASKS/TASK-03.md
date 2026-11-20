# Task 03: Features of Burp Community (أدوات ووحدات إصدار Burp Community)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Even in its free Community Edition, Burp Suite provides a formidable arsenal of manual testing tools that cover every phase of web application analysis:

- **Proxy:** The central intercepting proxy that captures, holds, inspects, and modifies HTTP/HTTPS requests and responses in real time.
- **Repeater:** Captures a single request from the Proxy or Target map and allows the tester to modify parameters, headers, and payloads, then resend it repeatedly while observing the direct response without having to interact with the browser.
- **Intruder:** An automation engine used for fuzzing parameters, directory and file enumeration, and brute-forcing authentication forms (rate-limited in Community Edition).
- **Decoder:** A multi-format translation tool capable of decoding and encoding data across URL encoding, Base64, HTML entities, Hex, ASCII, and calculating cryptographic hashes (MD5, SHA-256).
- **Comparer:** Performs visual word-by-word or byte-by-byte diffs between two requests or responses (e.g., comparing responses to identify subtle Blind SQLi differences).
- **Sequencer:** A cryptographic entropy analyzer that inspects the pseudo-randomness of tokens, session cookies, and reset IDs to detect predictable pattern generation.
- **Extensions / Extender:** Allows the integration of community and third-party plugins from the BApp Store written in Java, Python (Jython), or Ruby (JRuby).

#### الأسئلة المطروحة (Questions):
1. Which Burp Suite feature allows us to intercept requests between ourselves and the target? (Answer Format: FeatureName)
2. Which Burp tool would we use to brute-force a login form? (Answer Format: ToolName)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المكاملة التكتيكية بين الأدوات (Tool Orchestration):**
  * في سيناريو اختبار اختراق واقعي، لا تعمل هذه الأدوات بمعزل عن بعضها:
    1. يلتقط المختبر طلب تسجيل الدخول عبر **Proxy**.
    2. يرسله إلى **Repeater** (عبر `Ctrl + R`) لتجربة حمولة حقن SQL أولية يدوياً ودراسة سلوك الخادم.
    3. إذا تبين أن الحقل يقبل التخمين العنيف، يرسله إلى **Intruder** (عبر `Ctrl + I`) لضبط مواضع الحمولات وتخمين أسماء المستخدمين وكلمات المرور آلياً.
    4. إذا وجد كعكة جلسة جديدة، يرسلها إلى **Sequencer** لفحص مدى عشوائيتها وتوقع قيمها المستقبلية.
* **الفرق التشغيلي بين Repeater و Intruder:**
  * Repeater: مخصص للاختبار اليدوي الفردي الدقيق (واحد لواحد).
  * Intruder: مخصص للاختبار الآلي المكثف والمتكرر عبر القواميس (واحد لآلاف).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * المكون المسؤول عن اعتراض الطلبات يسمى الوكيل: `Proxy`.
2. **التلميح الثاني (المستوى العملي):**
   * الأداة المخصصة للأتمتة والتخمين العنيف على النماذج وقوائم الكلمات هي: `Intruder`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * إجابة السؤال الأول: `Proxy`، وإجابة السؤال الثاني: `Intruder`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### مصفوفة أدوات Burp Suite Community ووظائفها الميدانية:
```text
┌─────────────┬────────────────────────────────────────────────────────┐
│ الأداة      │ الوظيفة التكتيكية في اختبار الاختراق الميداني          │
├─────────────┼────────────────────────────────────────────────────────┤
│ Proxy       │ اعتراض وتعديل حزم الـ HTTP/HTTPS لحظياً بين العميل والهدف │
│ Repeater    │ تكرار وتعديل الطلبات يدوياً لملاحظة استجابة الخادم     │
│ Intruder    │ التخمين الآلي وحقن القواميس وفحص المعاملات             │
│ Decoder     │ تحويل وفك وتشفير صيغ البيانات (Base64, URL, Hex, Hash) │
│ Comparer    │ المقارنة المرئية بين استجابتين لتحديد الفروقات الدقيقة │
│ Sequencer   │ قياس عشوائية وتشتت (Entropy) رموز الجلسات والتوكنات    │
│ Extensions  │ تثبيت إضافات متجر BApp Store وتوسيع وظائف المنصة       │
└─────────────┴────────────────────────────────────────────────────────┘
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Intercepting Feature):** `Proxy`
* **إجابة السؤال الثاني (Brute-forcing Tool):** `Intruder`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تأكيد أداة `Proxy` المسؤولة عن اعتراض حركة المرور.
* تم تأكيد أداة `Intruder` المسؤولة عن هجمات التخمين الآلي.
* تم اعتماد الإجابات رسمياً على TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
تكامل وحدات Burp Suite يمنح مختبر الاختراق مسار عمل انسيابي فائق المرونة؛ فالقدرة على نقل الطلب الواحد بين Proxy و Repeater و Intruder بنقرة زر واحدة تختصر ساعات من كتابة السكربتات المخصصة وتتيح التركيز على تحليل استجابات التطبيق.
