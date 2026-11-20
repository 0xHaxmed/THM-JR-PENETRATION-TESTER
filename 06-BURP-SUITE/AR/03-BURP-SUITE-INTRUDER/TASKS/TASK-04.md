# Task 04: Payloads (إدارة مجموعات الحمولات وقواعد المعالجة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **Payloads** tab is where you define the values and dictionaries that Burp Intruder will inject into the positions marked in the Positions tab.

The Payloads tab is structured into four sequential configuration sections:
1. **Payload Sets:**
   - **Payload set:** In attacks like Sniper and Battering Ram, only 1 payload set is allowed. In multi-position attacks like Pitchfork and Cluster Bomb, you can configure up to 20 numbered payload sets. The numbering corresponds to positions ordered from top to bottom, left to right.
   - **Payload type:** Determines how payloads are generated (e.g., *Simple list*, *Numbers*, *Brute forcer*, *Null payloads*, *Character blocks*, *Dates*).
2. **Payload Settings:**
   - Configures the options for the selected payload type. For example, in a *Simple list*, this section contains controls to load a dictionary from a text file (`Load...`), paste entries (`Paste`), or add individual items. For *Numbers*, it defines min, max, and step increments.
3. **Payload Processing:**
   - Allows testers to define transformation rules applied to each payload item on-the-fly before transmission.
   - Available rules include:
     - `Add prefix`: Prepend a string to each payload.
     - `Add suffix`: Append characters to the end of each payload.
     - `Match / replace`: Apply regex transformations.
     - `Substring`: Extract specific slices.
     - `Reverse`: Reverse characters.
     - `Encode / Decode`: Base64, URL, Hex encoding.
     - `Hash`: MD5, SHA-1, SHA-256 generation.
     - `Skip if matches regex`: Filter out specific items.
4. **Payload Encoding:**
   - Controls automatic URL-encoding for reserved characters to prevent malformed HTTP requests.

#### الأسئلة المطروحة (Questions):
1. Which Payload processing rule could we use to add characters at the end of each payload in the set? (أي قاعدة معالجة للحمولات يمكننا استخدامها لإضافة محارف في نهاية كل حمولة داخل المجموعة؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **القوة التكتيكية لمعالجة الحمولات (Payload Processing):**
  * بدلاً من تعديل ملف القاموس يدوياً على القرص الصلب (وهو ما قد يستغرق وقتاً طويلاً ويستهلك مساحة التخزين)، تتيح قواعد المعالجة تعديل الحمولات ديناميكياً أثناء تدفقها في الذاكرة.
  * **أمثلة عملية:**
    * إذا كنت تخمن أسماء مستخدمين وتريد اختبار نطاق بريد إلكتروني، يمكنك استخدام قاعدة `Add suffix` لإضافة `@company.thm` تلقائياً لكل اسم في القائمة.
    * إذا كان التطبيق يتطلب إرسال تجزئة MD5 لكلمة المرور بدلاً من النص الصريح، يمكنك تطبيق قاعدة `Hash -> MD5` على كل كلمة قبل إرسالها.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * راجع قائمة قواعد المعالجة في قسم Payload Processing.
2. **التلميح الثاني (المستوى العملي):**
   * كلمة "إضافة في البداية" تسمى Add prefix، فماذا تسمى "إضافة في النهاية كلائحة لاحقة"؟
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * القاعدة المطلوبة تتكون من كلمتين: `Add suffix`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشريح قواعد معالجة الحمولات (Payload Processing Rules Pipeline):
```text
[ Raw Payload from List ] (e.g., "admin")
            │
            ▼  Rule 1: Add prefix ("usr_")
     ("usr_admin")
            │
            ▼  Rule 2: Add suffix ("_2026")
     ("usr_admin_2026")
            │
            ▼  Rule 3: Hash (MD5)
     ("c1b918a5e45...")
            │
            ▼
[ Injected into HTTP Request Template (§...§) ]
```

#### جدول مقارنة قواعد المعالجة الشائعة:
| القاعدة (Rule) | الوظيفة (Function) | مثال تطبيقي (Example) |
| :--- | :--- | :--- |
| **Add prefix** | إضافة بادئة في بداية النص | تحويل `123` إلى `USER_123` |
| **Add suffix** | إضافة لاحقة في نهاية النص | تحويل `john` إلى `john@target.thm` |
| **Match / replace** | استبدال محارف عبر Regex | استبدال المسافات بـ `+` |
| **Encode** | تشفير الحمولة بصيغة محددة | Base64 Encoding للتوثيق الأساسي |
| **Hash** | توليد التجزئة الرياضية | تشفير MD5 أو SHA-256 لكلمات المرور |

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `Add suffix`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
1. في تبويب **Payloads**، انتقل إلى أسفل الصفحة إلى قسم **Payload Processing**.
2. انقر على زر **Add**.
3. في القائمة المنسدلة للقواعد، اختر `Add suffix`، ستجد حقل إدخال يطلب منك تحديد المحارف المراد إلحاقها بنهاية كل عنصر، مما يؤكد صحة الإجابة.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* توفر قواعد معالجة الحمولات، وعلى رأسها `Add suffix` و `Add prefix` و `Hash`، مرونة برمجية فائقة لتكييف القواميس الجاهزة وتوليد حمولات معقدة ديناميكياً في الذاكرة دون الحاجة لكتابة سكربتات خارجية.
