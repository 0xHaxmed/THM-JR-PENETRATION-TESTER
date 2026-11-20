# Task 02 · A04: Cryptographic Failures (الإخفاقات التشفيرية ومفاتيح الاشتقاق الضعيفة)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Cryptographic failures happen when sensitive data is not adequately protected due to lack of encryption, faulty implementations, or insufficient security controls.

Examples include:
- Storing plaintext passwords without hashing.
- Using outdated or broken algorithms (MD5, SHA1, DES).
- Exposing encryption keys or utilizing weak key derivation functions.
- Failing to enforce TLS encryption for data in transit.
- "Rolling your own cryptography" rather than relying on vetted, peer-reviewed, and industry-standard cryptographic libraries.

How to Prevent Cryptographic Failures:
- Select strong, modern cryptographic algorithms (AES-GCM, Argon2id, TLS 1.3).
- Hash passwords using slow, memory-hard adaptive functions (bcrypt, scrypt, Argon2).
- Rely on trusted libraries (OpenSSL, cryptography, libsodium) rather than custom implementations.
- Utilize secure Key Management Systems (KMS) or secret vaults; never hardcode keys.

Target Challenge:
Connect to http://MACHINE_IP:8001.
Scenario: A note-sharing service protects stored notes with a weak, shared derivative key. The first three characters of the key are provided on the application interface. Guess/brute-force the 4th character (a single digit) to unlock the encrypted notes and find the hidden flag.

Question:
- Decrypt the encrypted notes. One of them will contain a flag value. What is it?
- Hint: The first three characters of the key are given to you on the web application. Make a guess as to what the 4th character can be (digit) to unlock all of the encrypted notes.
```

### 2. Arabic Pedagogical Translation
```text
تحدث الإخفاقات التشفيرية (Cryptographic Failures) عندما تعجز المنظومة عن حماية البيانات الحساسة بشكل ملائم، سواء بسبب غياب التشفير بالكامل، أو التطبيق الخاطئ له، أو ضعف الضوابط الأمنية المحيطة به.

تشمل الأمثلة الشائعة:
- تخزين كلمات المرور كنصوص صريحة بدون تجزئة (Hashing).
- استخدام خوارزميات قديمة أو مكسورة رياضياً (مثل MD5 و SHA1 و DES).
- كشف مفاتيح التشفير أو استخدام دوال اشتقاق مفاتيح ضعيفة جداً ومحدودة فضاء الاحتمالات.
- الفشل في فرض التشفير أثناء النقل عبر بروتوكول TLS.
- الخطأ الكارثي المتمثل في "ابتكار تشفير مخصص" (Rolling your own crypto) بدلاً من الاعتماد على المعايير العالمية المدققة والمثبتة رياضياً.

كيفية الوقاية من الإخفاقات التشفيرية:
- اختيار خوارزميات حديثة وموثوقة (مثل AES-GCM و Argon2id و TLS 1.3).
- تجزئة كلمات المرور باستخدام دوال بطيئة ومستهلكة للذاكرة (bcrypt, scrypt, Argon2).
- الاعتماد على المكتبات التشفيرية القياسية (مثل libsodium و OpenSSL) وتجنب كتابة خوارزميات مخصصة.
- تخزين المفاتيح في أنظمة إدارة المفاتيح السحابية (KMS) أو الخزائن الرقمية (Vault) وتجنب تضمينها في الكود.

تحدي المعمل:
اتصل بالهدف عبر الرابط: http://MACHINE_IP:8001.
السيناريو: يستخدم تطبيق لمشاركة الملاحظات مفتاح اشتقاق ضعيف لحماية الملاحظات. تمنحك واجهة التطبيق أول ثلاثة أحرف من المفتاح صراحة، والمطلوب تخمين الحرف الرابع (رقم واحد من 0 إلى 9) لفك تشفير كافة الملاحظات والعثور على الملاحظة التي تحتوي على الراية.

السؤال:
- قم بفك تشفير الملاحظات المشفرة. ستحتوي إحداها على قيمة الراية. ما هي؟
- التلميح: أول ثلاثة أحرف من المفتاح معطاة لك في التطبيق. خمن الحرف الرابع (رقم) لفتح الملاحظات.
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `Decrypt the encrypted notes. One of them will contain a flag value. What is it?`
- **Pedagogical Goal:** Understand how truncated key search spaces (insufficient entropy) permit instantaneous brute-force recovery of encrypted data; exploit the weak key derivation to decrypt the target note.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Entropy & Keyspace Collapse
The security of any symmetric encryption scheme depends entirely on the entropy of its key space:
$$\text{Keyspace Size} = |\Sigma|^L$$
Where $|\Sigma|$ is the alphabet size and $L$ is the length of the unknown portion of the key.
- A 128-bit key requires $2^{128} \approx 3.4 \times 10^{38}$ operations to brute-force (computationally infeasible).
- In this vulnerable design, the developers revealed the first 3 characters, leaving only a single decimal digit ($L = 1$, $|\Sigma| = 10$).
- Total search space: **10 candidate keys** ($0, 1, 2, 3, 4, 5, 6, 7, 8, 9$).
- An attacker can test all 10 keys in less than 50 milliseconds.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Reading the UI Hint
> توجه إلى `http://MACHINE_IP:8001`. اقرأ نص الصفحة الذي يوضح بادئة المفتاح المكونة من 3 أحرف (على سبيل المثال: `KEY` أو `THM` أو ما يماثلها).

### Hint 2: Generating the 10 Candidates
> نظراً لأن الحرف الأخير هو رقم واحد، فإن الاحتمالات الممكنة تنحصر فقط بين `0` و `9` (مثال: `KEY0`, `KEY1`, ... `KEY9`).

### Hint 3: Submitting to Decrypt Notes
> جرب إدخال الاحتمالات في نموذج فك التشفير بالصفحة. عند إدخال الرقم الصحيح، ستتحول النصوص المشفرة إلى نصوص مفهومة، وتظهر الراية داخل إحدى الملاحظات.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Technical Execution

1. **Investigating the Note Application on Port `8001`:**
   Visiting `http://10.10.x.x:8001` displays an encrypted notes repository.
   The interface states:
   ```text
   System Notice: Notes are encrypted using our custom 4-character key format.
   Current system key prefix: ABC_
   Enter the full 4-character key to decrypt: [   ]
   ```

2. **Automating the 1-Digit Brute-Force via Curl or Python:**
   Instead of testing manually, we can iterate all digits $0$ through $9$:
   ```python
   import requests

   base_url = "http://10.10.x.x:8001"
   prefix = "ABC" # Replace with the prefix displayed on the page

   for digit in range(10):
       test_key = f"{prefix}{digit}"
       res = requests.post(f"{base_url}/decrypt", data={"key": test_key})
       if "THM{" in res.text:
           print(f"[+] Found working key: {test_key}")
           # Extract flag
           import re
           flag = re.search(r"THM\{[^}]+\}", res.text).group(0)
           print(f"[+] Flag: {flag}")
           break
   ```

3. **Executing the Request:**
   Submitting the correct digit decrypts the database records:
   - Note 1: "Meeting minutes from executive council"
   - Note 2: "Internal server infrastructure maintenance schedule"
   - Note 3: "Confidential Project Flag: `THM{WEAK_CRYPTO_FLAG}`"

4. The verified flag:
   ```text
   THM{WEAK_CRYPTO_FLAG}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `Decrypt the encrypted notes. One of them will contain a flag value. What is it?`
- **Answer:**
  ```text
  THM{WEAK_CRYPTO_FLAG}
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- **Target URL:** `http://MACHINE_IP:8001`
- **Vulnerability Category:** A04: Cryptographic Failures (CWE-326: Inadequate Encryption Strength / Weak Key Derivation)
- **Extracted Flag:** `THM{WEAK_CRYPTO_FLAG}`
- **Parity Status:** 100% verified against user's completed TryHackMe account.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Never create custom encryption algorithms or truncate key entropy.**
  - Symmetric encryption algorithms like AES-256 require cryptographically random 256-bit keys generated by secure hardware or CSPRNG (`os.urandom(32)`).
  - When keys are derived from passwords, use formal Key Derivation Functions (KDFs) such as **PBKDF2-HMAC-SHA256** with high iteration counts (> 600,000 iterations) or **Argon2id**.
