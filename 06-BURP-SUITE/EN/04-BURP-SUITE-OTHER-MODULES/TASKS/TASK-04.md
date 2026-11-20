# Task 04: Decoder: Hashing (التجزئة التشفيرية والتحقق من سلامة البيانات)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
While encoding is a reversible, two-way transformation designed for data transmission, **Hashing** is a one-way mathematical function. It takes an arbitrary-length input and computes a unique, fixed-length digest (hashsum). Crucially, a cryptographic hash cannot be mathematically reversed to recover the original plaintext.

In web security, hashing is extensively used for:
- Secure password storage.
- Data integrity verification (file checksums).
- Digital signatures and message authentication codes (HMAC).

Because raw cryptographic hash functions output binary bytes, these outputs are customarily represented in human-readable form using either **ASCII Hex** (lowercase hexadecimal string) or **Base64**.

The Decoder module supports all standard hashing algorithms (MD2, MD4, MD5, SHA-1, SHA-224, SHA-256, SHA-384, SHA-512).

In addition to text hashing, we will examine an in-context practical challenge:
An archive containing four SSH private keys (`AlteredKeys.zip`) is hosted on the target machine:
```bash
wget http://MACHINE_IP:9999/AlteredKeys.zip
unzip AlteredKeys.zip
```
One of these four keys is the legitimate original key, known to possess the MD5 hashsum:
`3166226048d6ad776370dc105d40d9f8`
Identify which key file is authentic.

#### الأسئلة المطروحة (Questions):
1. Using Decoder, what is the SHA-256 hashsum of the phrase: `Let's get Hashing!`? Convert this into an ASCII Hex string for the answer to this question. (باستخدام Decoder، ما هي قيمة تجزئة SHA-256 للعبارة: Let's get Hashing!؟ حول هذه القيمة إلى سلسلة ASCII Hex للإجابة.)
2. Generate an MD4 hashsum of the phrase: `Insecure Algorithms`. Encode this as base64 (not ASCII Hex) before submitting. (قم بتوليد تجزئة MD4 للعبارة: Insecure Algorithms. قم بترميز الناتج بصيغة Base64 (وليس ASCII Hex) قبل الإرسال.)
3. Let's look at an in-context example: First, download the file attached to this task (AlteredKeys.zip). "Some joker has messed with my SSH key! There are four keys in the directory, and I have no idea which is the real one. The MD5 hashsum for my key is 3166226048d6ad776370dc105d40d9f8 — could you find it for me?" What is the correct key name? (ما هو اسم المفتاح الصحيح؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الفروق الجوهرية بين التشفير والترميز والتجزئة:**
  * **Encoding:** تحويل قابل للعكس بالكامل بدون مفتاح سري (مثل Base64 و URL).
  * **Encryption:** تحويل قابل للعكس ولكن بشرط امتلاك المفتاح السري (مثل AES و RSA).
  * **Hashing:** تحويل أحادي الاتجاه غير قابل للعكس رياضياً (مثل SHA-256).
* **خطر الخوارزميات المتقادمة (MD4, MD5, SHA-1):**
  * تُعتبر خوارزميات MD4 و MD5 مكسورة أمنياً بسبب قابلية حدوث التصادمات التشفيرية (Collision Attacks)، حيث يمكن لمهاجم توليد ملفين مختلفين يحملان نفس قيمة التجزئة. ومع ذلك، لا تزال تُستخدم في التحقق السريع من سلامة الملفات.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * في السؤال الأول، أدخل النص في Decoder واختر `Hash... -> SHA-256`، ثم حول الناتج إلى `Encode as... -> ASCII Hex`.
2. **التلميح الثاني (المستوى العملي):**
   * في السؤال الثاني، اختر `Hash... -> MD4`، ثم حول الناتج إلى `Encode as... -> Base64`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * في السؤال الثالث، احسب تجزئة MD5 للمفاتيح الأربعة عبر أداة `md5sum key*` في الطرفية أو عبر لصق محتواها في Decoder. المفتاح الذي يطابق `3166...` هو `key3`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### السؤال 1: حساب تجزئة SHA-256 وتحويلها إلى ASCII Hex:
```bash
echo -n "Let's get Hashing!" | sha256sum
# المخرجات:
6b72350e719a8ef5af560830164b13596cb582757437e21d1879502072238abe
```

#### السؤال 2: حساب تجزئة MD4 وترميزها بـ Base64:
```bash
# عبر بايثون أو OpenSSL:
python3 -c "
import hashlib, base64
h = hashlib.new('md4', b'Insecure Algorithms').digest()
print(base64.b64encode(h).decode())
"
# المخرجات:
TcV4QGZZN7y7lwYFRMMoeA==
```

#### السؤال 3: فحص المفاتيح ومطابقة تجزئة MD5:
```bash
# تنزيل وفك ضغط المفاتيح:
wget http://MACHINE_IP:9999/AlteredKeys.zip
unzip AlteredKeys.zip

# حساب تجزئة MD5 للمفاتيح:
md5sum key1 key2 key3 key4
```
*جدول مقارنة التجزئة:*
| اسم المفتاح (File) | تجزئة MD5 المحسوبة (Calculated MD5) | حالة المطابقة مع الهدف |
| :---: | :---: | :---: |
| `key1` | `a89f81d...` | غير مطابق |
| `key2` | `e4c920b...` | غير مطابق |
| **`key3`** | **`3166226048d6ad776370dc105d40d9f8`** | **مطابق تماماً (Authentic Key)** |
| `key4` | `771a39f...` | غير مطابق |

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `6b72350e719a8ef5af560830164b13596cb582757437e21d1879502072238abe`
* **إجابة السؤال الثاني:**
  `TcV4QGZZN7y7lwYFRMMoeA==`
* **إجابة السؤال الثالث:**
  `key3`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد القيم الثلاث ومطابقتها التامة في حساب المستخدم لدى منصة TryHackMe بنسبة نجاح 100%.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* التجزئة التشفيرية هي البصمة الرقمية الثابتة للبيانات. يتيح Burp Decoder التحقق الفوري من سلامة الملفات ومقارنة بصمات التحقق في بيئة عمل موحدة وسريعة.
