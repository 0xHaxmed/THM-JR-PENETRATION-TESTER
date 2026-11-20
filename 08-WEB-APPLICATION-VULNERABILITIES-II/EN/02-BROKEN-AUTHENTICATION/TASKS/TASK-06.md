# Task 06 - Cookie Manipulation (التلاعب بملفات تعريف الارتباط)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Authentication and session management mechanisms frequently rely on cookies to preserve state across HTTP requests. However, when developers fail to implement cryptographic signatures or server-side session stores, cookies become vulnerable to client-side manipulation.

In this task, we explore three common cookie formats:
1. Plain Text Cookies:
   The application stores authentication flags directly as readable text (e.g., logged_in=false; admin=false).
   Navigate to http://MACHINE_IP/cookie-test in your browser or Burp Suite. Modify the cookie values to logged_in=true; admin=true and refresh the page to obtain the flag.

2. Hashed Cookies:
   Some applications store hashed representations of user IDs or session numbers (e.g. MD5 or SHA-1 hashes). Because standard hash functions are one-way but deterministic, unsalted hashes of common or sequential values can be reversed using lookup tables, rainbow tables, or services like crackstation.net.
   Reverse the MD5 hash: 3b2a1053e3270077456a79192070aa78.

3. Base64 Encoded Cookies:
   Developers often mistake Base64 encoding for encryption. Base64 simply transforms binary data into an ASCII string format and can be decoded instantly by anyone.
   - Decode the Base64 string: VEhNe0JBU0U2NF9FTkNPRElOR30=
   - Encode the JSON payload: {"id":1,"admin":true}

Questions:
- What is the flag from changing the plain text cookie values?
- What is the value of the md5 hash 3b2a1053e3270077456a79192070aa78 ?
- What is the base64 decoded value of VEhNe0JBU0U2NF9FTkNPRElOR30= ?
- Encode the following value using base64 {"id":1,"admin":true}
```

### 2. Arabic Pedagogical Translation
```text
تعتمد آليات المصادقة وإدارة الجلسات بشكل كبير على ملفات تعريف الارتباط (Cookies) للحفاظ على حالة المستخدم عبر طلبات HTTP المتتالية. ومع ذلك، عندما يفشل المطورون في تطبيق التوقيعات التشفيرية الرقمية أو الاعتماد على مخازن الجلسات المركزية في الخادم، تصبح الكوكيز عرضة للتلاعب المباشر من جانب العميل (Client-Side Manipulation).

في هذه المهمة، نستكشف ثلاث صيغ شائعة لملفات الارتباط الضعيفة:
1. ملفات الارتباط النصية الصريحة (Plain Text Cookies):
   يخزن التطبيق مؤشرات المصادقة مباشرة كنص عادي مقروء (مثل: logged_in=false; admin=false).
   انتقل إلى الرابط http://MACHINE_IP/cookie-test في متصفحك أو عبر Burp Suite. قم بتعديل قيم الكوكي لتصبح logged_in=true; admin=true ثم حدث الصفحة للحصول على العلم.

2. ملفات الارتباط المجزأة (Hashed Cookies):
   تخزن بعض التطبيقات قيماً مجزأة لمعرفات المستخدمين أو أرقام الجلسات (مثل تجزئات MD5 أو SHA-1). نظراً لأن دوال التجزئة حتمية (Deterministic) وغير مملحة (Unsalted)، يمكن عكس التجزئات للقيم الشائعة أو المتسلسلة باستخدام الجداول المحددة مسبقاً (Rainbow Tables) أو مواقع مثل crackstation.net.
   قم بفك وعكس تجزئة MD5 التالية: 3b2a1053e3270077456a79192070aa78.

3. ملفات الارتباط المرمزة بـ Base64:
   يخلط الكثير من المطورين المبتدئين بين الترميز (Encoding) والتشفير (Encryption). ترميز Base64 هو مجرد تحويل للبيانات إلى صيغة نصوص ASCII، ويمكن فكه وعكسه فورياً بواسطة أي شخص دون الحاجة لأي مفتاح سري.
   - فك ترميز نص Base64 التالي: VEhNe0JBU0U2NF9FTkNPRElOR30=
   - قم بترميز كائن JSON التالي إلى Base64: {"id":1,"admin":true}

الأسئلة:
- ما هو العلم الناتج عن تغيير قيم الكوكي النصية الصريحة؟
- ما هي القيمة الأصلية لتجزئة md5 التالية: 3b2a1053e3270077456a79192070aa78 ؟
- ما هي القيمة بعد فك ترميز base64 للنص التالي: VEhNe0JBU0U2NF9FTkNPRElOR30= ؟
- قم بترميز القيمة التالية باستخدام base64: {"id":1,"admin":true}
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Cryptographic Taxonomy & Common Fallacies
```
+-----------------------------------------------------------------------------------+
|                        ENCODING vs HASHING vs ENCRYPTION                          |
+-------------------+---------------------------------------------------------------+
| Encoding (Base64) | - Data representation transformation.                         |
|                   | - NO key required. Completely reversible by anyone.           |
+-------------------+---------------------------------------------------------------+
| Hashing (MD5)     | - One-way cryptographic digest.                               |
|                   | - Deterministic (same input = same hash). Vulnerable to       |
|                   |   Rainbow tables if unsalted and low-entropy.                 |
+-------------------+---------------------------------------------------------------+
| Encryption (AES)  | - Confidentiality transformation using a SECRET KEY.          |
|                   | - Impossible to reverse without the secret key.               |
+-------------------+---------------------------------------------------------------+
```

### 2. Operational Security (OpSec) Considerations
* **الاعتماد على الكوكيز من جانب العميل:**
  - أي تطبيق يقرر ما إذا كان المستخدم مديراً بناءً على قيمة قابلة للتعديل في المتصفح (`Cookie: admin=true`) يعاني من انهيار أمني كامل لمبدأ "عدم الثقة في مدخلات العميل" (Zero Client Trust).
* **الحذر من أسطر النهاية الفارغة (Newlines):**
  - عند استخدام أدوات سطر الأوامر لتوليد ترميز Base64، يجب دائماً استخدام العلم `-n` مع أمر `echo` (`echo -n`), لأن تضمين حرف السطر الجديد `\n` يؤدي إلى تغيير قيمة الـ Base64 وقيمة التجزئة بالكامل!

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - استخدم أدوات المطورين في المتصفح (تبويب Application ثم Cookies) أو Burp Suite لتعديل قيم الكوكي في صفحة `/cookie-test`.
> - لفك تجزئة MD5، توجه إلى موقع CrackStation أو استخدم أداة `hashcat` / `john`.
> - لترميز وفك ترميز Base64، استخدم سطر الأوامر `base64` أو Burp Decoder.
>
> **المستوى الثاني (توجيهي):**
> - عند تعيين الكوكي إلى `logged_in=true; admin=true`، سيظهر العلم مباشرة في الصفحة.
> - تجزئة MD5 تعود لرقم من 6 خانات.
> - فك نص الـ Base64 سيعطيك علماً يبدأ بـ `THM{...}`.
> - تأكد من عدم وجود مسافات عند ترميز كائن JSON `{"id":1,"admin":true}`.
>
> **المستوى الثالث (الحل المباشر):**
> 1. علم التلاعب بالكوكي: `THM{COOKIE_TAMPERING}`
> 2. قيمة تجزئة MD5: `463729`
> 3. فك ترميز Base64: `THM{BASE64_ENCODING}`
> 4. ناتج ترميز Base64: `eyJpZCI6MSwiYWRtaW4iOnRydWV9`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Plain Text Cookie Exploitation
نرسل طلباً باستخدام `curl` إلى صفحة `/cookie-test` متضمناً الكوكيز المعدلة:
```bash
curl -s http://MACHINE_IP/cookie-test -H "Cookie: logged_in=true; admin=true"
```
مخرجات الاستجابة في المتصفح / الطرفية:
```html
<div class="alert alert-success">
    <h4>Administrative Access Granted!</h4>
    <p>Congratulations, you tampered with the cookie values successfully.</p>
    <p>Flag: <strong>THM{COOKIE_TAMPERING}</strong></p>
</div>
```
العلم المستخرج هو: **`THM{COOKIE_TAMPERING}`**.

---

### 2. MD5 Hash Reversal
التجزئة المطلوبة: `3b2a1053e3270077456a79192070aa78`
باستخدام استعلام CrackStation أو محرك محلي:
```bash
# Using python hashlib verification
python3 -c "import hashlib; print(hashlib.md5(b'463729').hexdigest())"
# Output: 3b2a1053e3270077456a79192070aa78
```
القيمة الأصلية المحلولة هي: **`463729`**.

---

### 3. Base64 Decoding
النص المطلوب: `VEhNe0JBU0U2NF9FTkNPRElOR30=`
ننفذ أمر فك الترميز في الطرفية:
```bash
echo -n "VEhNe0JBU0U2NF9FTkNPRElOR30=" | base64 -d
```
المخرجات:
```text
THM{BASE64_ENCODING}
```
القيمة هي: **`THM{BASE64_ENCODING}`**.

---

### 4. Base64 Encoding
الكائن المطلوب: `{"id":1,"admin":true}`
ننفذ أمر الترميز مع الحفاظ التام على انعدام المسافات وبدون سطر جديد:
```bash
echo -n '{"id":1,"admin":true}' | base64
```
المخرجات:
```text
eyJpZCI6MSwiYWRtaW4iOnRydWV9
```
القيمة هي: **`eyJpZCI6MSwiYWRtaW4iOnRydWV9`**.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | الأداة والمنهجية |
| :--- | :--- | :--- |
| **What is the flag from changing the plain text cookie values?** | `THM{COOKIE_TAMPERING}` | تعديل الكوكي إلى `logged_in=true; admin=true` عبر curl/DevTools. |
| **What is the value of the md5 hash 3b2a1053e3270077456a79192070aa78 ?** | `463729` | فك التجزئة بواسطة جداول البحث العكسية (CrackStation). |
| **What is the base64 decoded value of VEhNe0JBU0U2NF9FTkNPRElOR30= ?** | `THM{BASE64_ENCODING}` | فك الترميز القياسي بواسطة `base64 -d`. |
| **Encode the following value using base64 {"id":1,"admin":true}** | `eyJpZCI6MSwiYWRtaW4iOnRydWV9` | الترميز الدقيق باستخدام `echo -n ... \| base64`. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من جميع الإجابات الأربع ومطابقتها حرفياً مع قاعدة بيانات TryHackMe واجتيازها بنسبة 100%.
* تم اختبار دقة ترميز Base64 بدون أي أحرف زائدة لضمان صحة الإجابة.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **الترميز ليس تشفيراً (Encoding is NOT Encryption):** لا يجوز أبداً الاعتماد على Base64 لحماية أو إخفاء بيانات حساسة أو أدوار مستخدمين، لأن أي شخص يستطيع قراءتها وتعديلها في أجزاء من الثانية.
2. **التجزئة السريعة غير كافية لملفات الجلسات:** استخدام MD5 أو SHA-1 بدون تمليح سري (Secret Salt) أو توقيع رقمي (HMAC) يجعل الجلسات قابلة للعكس والتزوير الفوري. يجب دائماً توقيع كوكيز العميل رقمياً أو استخدام جلسات مخزنة على الخادم بالكامل.
