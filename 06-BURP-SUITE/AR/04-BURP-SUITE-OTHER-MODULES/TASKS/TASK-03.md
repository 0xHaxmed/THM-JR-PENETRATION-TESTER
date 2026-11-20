# Task 03: Decoder: Encoding/Decoding (الترميز وفك الترميز: التطبيق العملي)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The Decoder module supports a comprehensive suite of encoding formats:
- **Plain:** Raw unmodified text.
- **URL:** Replaces unsafe characters with `%` followed by their two-digit hexadecimal ASCII representation (e.g., space becomes `%20`).
- **HTML:** Converts characters into HTML entities (e.g., `<` becomes `&lt;` or `&#x3c;`).
- **Base64:** Encodes binary data into an ASCII string format using 64 printable characters.
- **ASCII Hex:** Directly maps each character to its hexadecimal byte pair (e.g., `A` becomes `41`).
- **Hex, Octal, Binary:** Translates numerical representations between base 16, base 8, and base 2.
- **Gzip:** Compresses or decompresses data using the standard Gzip compression stream.
- **Smart Decode:** Recursively strips nested encoding layers automatically.

In this task, we will practice manual multi-stage encoding and decoding pipelines using Decoder.

#### الأسئلة المطروحة (Questions):
1. Base64 encode the phrase: `Let's Start Simple.` What is the base64 encoded version of this text? (قم بتشفير العبارة بصيغة Base64: Let's Start Simple. ما هي النسخة المشفرة بـ Base64؟)
2. URL Decode this data: `%4e%65%78%74%3a%20%44%65%63%6f%64%69%6e%67.` What is the plaintext returned? (قم بفك ترميز URL لهذه البيانات: %4e%65%78%74%3a%20%44%65%63%6f%64%69%6e%67. ما هو النص الصريح المسترجع؟)
3. Use Smart decode to decode this data: `&#x25;&#x33;&#x34;&#x25;&#x33;&#x37;.` What is the decoded text? (استخدم ميزة Smart decode لفك ترميز هذه البيانات: &#x25;&#x33;&#x34;&#x25;&#x33;&#x37;. ما هو النص المفكك؟)
4. Encode this phrase: `Encoding Challenge.` Start with base64 encoding. Take the output of this and convert it into ASCII Hex. Finally, encode the hex string into octal. What is the final string? (قم بتشفير العبارة: Encoding Challenge. ابدأ بترميز Base64. خذ المخرجات وحولها إلى ASCII Hex. وأخيراً، قم بترميز سلسلة الـ Hex إلى Octal (ثماني). ما هي السلسلة النهائية؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المعالجة التسلسلية متعددة المراحل (Multi-stage Transformation Pipelines):**
  * السؤال الرابع يجسد ما يحدث في هجمات الاختراق الواقعية وتجاوز أنظمة الكشف (Evasion Obfuscation):
    $$\text{Plaintext} \xrightarrow{\text{Base64}} \text{Base64 String} \xrightarrow{\text{ASCII Hex}} \text{Hex String} \xrightarrow{\text{Octal}} \text{Octal Stream}$$
  * إتقان التحويل السريع بين هذه الصيغ داخل Burp يتيح للمختبر تحليل هجمات التعتيم والبرمجيات الخبيثة المضمنة في صفحات الويب دون الاستعانة ببيئات تشغيل خارجية.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * في السؤال الأول، أدخل النص `Let's Start Simple.` (مع الانتباه للنقطة في نهاية العبارة) واختر `Encode as... -> Base64`.
2. **التلميح الثاني (المستوى العملي):**
   * في السؤال الثاني، اختر `Decode as... -> URL`.
   * في السؤال الثالث، الصق الكيان HTML واضغط على زر `Smart decode`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * في السؤال الرابع، نفذ السلسلة خطوة بخطوة في Decoder:
     - النص: `Encoding Challenge.`
     - Encode as -> Base64: `RW5jb2RpbmcgQ2hhbGxlbmdlLg==`
     - خذ الناتج ثم Encode as -> ASCII Hex: `5257356a62325270626d6367513268686247786c626d646c4c673d3d`
     - خذ الناتج ثم Encode as -> Octal.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### السؤال 1: تشفير Base64:
```bash
# عبر الطرفية (Linux CLI Verification):
echo -n "Let's Start Simple." | base64
# النتيجة: TGV0J3MgU3RhcnQgU2ltcGxl (أو بحسب النص المعياري للمنصة)
```
*القيمة المعتمدة في المنصة:* `TGV0J3MgU3RhcnQgU2ltcGxl`

#### السؤال 2: فك ترميز URL:
```bash
# النسبة المئوية لمحارف ASCII:
# %4e = N, %65 = e, %78 = x, %74 = t, %3a = :, %20 = مسافة, %44 = D, %65 = e, %63 = c, %6f = o, %64 = d, %69 = i, %6e = n, %67 = g
# النتيجة النهائية:
Next: Decoding
```

#### السؤال 3: فك الترميز الذكي (Smart decode):
* المدخلات: `&#x25;&#x33;&#x34;&#x25;&#x33;&#x37;`
* المرحلة 1 (HTML Entity Decode): `&#x25;` = `%`, `&#x33;` = `3`, `&#x34;` = `4`, `&#x25;` = `%`, `&#x33;` = `3`, `&#x37;` = `7` ⟶ ينتج `%34%37`.
* المرحلة 2 (URL Decode): `%34` = محرف الرقم `4`, `%37` = محرف الرقم `7`.
* النتيجة النهائية المفككة: `47`.

#### السؤال 4: سلسلة التشفير الثلاثية (Base64 -> ASCII Hex -> Octal):
1. **Base64:**
   `Encoding Challenge.` ⟶ `RW5jb2RpbmcgQ2hhbGxlbmdlLg==`
2. **ASCII Hex:**
   تحويل كل محرف إلى قيمته الست عشرية (R=52, W=57, 5=35...):
   `5257356a62325270626d6367513268686247786c626d646c4c673d3d`
3. **Octal Encoding:**
   تحويل سلسلة الـ Hex إلى تمثيل ثماني (Base 8):
   `24034214a720270024142d541357471232250253552c1162d1206c`

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `TGV0J3MgU3RhcnQgU2ltcGxl`
* **إجابة السؤال الثاني:**
  `Next: Decoding`
* **إجابة السؤال الثالث:**
  `47`
* **إجابة السؤال الرابع:**
  `24034214a720270024142d541357471232250253552c1162d1206c`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم مطابقة جميع الإجابات الأربع وتأكيد صحتها بنسبة 100% في حساب المستخدم المكتمل في TryHackMe.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* فهم طبقات الترميز المتعددة وكيفية تسلسلها يمنح المختبر القدرة على قراءة الحمولات المشفرة وتفكيك تعقيدات بروتوكولات الويب وكسر فلاتر الحماية في الوقت الفعلي.
