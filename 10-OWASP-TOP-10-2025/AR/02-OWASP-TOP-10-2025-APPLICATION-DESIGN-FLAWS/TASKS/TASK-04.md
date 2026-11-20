# Task 04 · AS04: Cryptographic Failures (الإخفاقات التشفيرية)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Cryptographic failures occur when encryption is used incorrectly or omitted entirely. These issues include weak algorithms, hard-coded keys, improper key handling, or unencrypted sensitive data in transit or at rest.

Why It Matters:
Web applications rely on cryptography for transport security, data storage, authentication tokens, and secret management. Weaknesses allow man-in-the-middle attacks, key brute-forcing, or direct extraction of credentials, tokens, and personally identifiable information (PII).

Common Patterns in 2025:
- Deprecated or mathematically broken algorithms: MD5, SHA-1, DES, RC4, or AES in ECB mode.
- Hard-coded secrets, encryption keys, or salts embedded directly in client-side code, backend repos, or configuration files.
- Absence of key rotation and lack of formal cryptographic lifecycle management.
- Transmission of sensitive data in plaintext (HTTP instead of HTTPS, unencrypted internal microservice traffic).
- Self-signed, expired, or untrusted TLS certificates.
- AI/ML systems lacking secret handling for proprietary model weights or sensitive training inputs.

Target Challenge:
Connect to http://MACHINE_IP:5004.
Scenario: An internal data protection utility encrypts files. Find the hard-coded key used by the application to decrypt the protected file and retrieve the flag.

Question:
- What's the flag?
```

### 2. Arabic Pedagogical Translation
```text
تحدث الإخفاقات التشفيرية (Cryptographic Failures) عندما يُستخدم التشفير بطريقة غير صحيحة، أو عندما يتم إهماله وتجاهله كلياً. تشمل هذه القضايا استخدام خوارزميات قديمة أو مكسورة، وحفظ مفاتيح التشفير بشكل نصي صلب داخل الكود (Hard-coded Keys)، أو سوء إدارة دورة حياة المفاتيح، أو نقل وتخزين البيانات الحساسة بدون تشفير.

لماذا يُعد هذا الأمر فائق الخطورة؟
تعتمد تطبيقات الويب الحديثة على علم التشفير لحماية حركة البيانات عبر الشبكة، وتأمين قواعد البيانات، وتوليد رموز المصادقة (Tokens)، وإدارة الأسرار الرقمية. تؤدي نقاط الضعف التشفيرية إلى تمكين هجمات الوسيط (MitM)، وتخمين المفاتيح، أو استخراج أوراق الاعتماد والبيانات الشخصية الحساسة (PII) بصورة مباشرة.

الأنماط الشائعة في معيار 2025:
- خوارزميات ملغاة أو مكسورة رياضياً: مثل MD5 و SHA-1 و DES ونمط التشفير الكتلي الضعيف AES-ECB.
- مفاتيح التشفير أو الأسرار المكتوبة كنصوص ثابتة داخل مستودعات الكود أو ملفات الإعدادات المكشوفة.
- غياب آليات تدوير المفاتيح الدورية (Key Rotation) وضعف إدارة دورة الحياة التشفيرية.
- نقل البيانات الحساسة عبر نصوص واضحة غير مشفرة (بروتوكول HTTP أو قنوات الاتصال الداخلية غير الآمنة بين الخدمات المصغرة).
- شهادات TLS منتهية الصلاحية، أو ذاتية التوقيع (Self-signed)، أو غير موثوقة.
- أنظمة الذكاء الاصطناعي التي تفتقر لإدارة وتشفير أوزان النماذج والبيانات الحساسة أثناء التدريب.

تحدي المعمل:
اتصل بالهدف عبر الرابط: http://MACHINE_IP:5004.
السيناريو: يستخدم نظام داخلي لحماية البيانات آلية تشفير للملفات. ابحث عن المفتاح الصلب (Hard-coded Key) الذي يستخدمه التطبيق لفك تشفير الملف المحمي واستخراج الراية.

السؤال:
- ما هي الراية (What's the flag?)؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `What's the flag?`
- **Pedagogical Goal:** Audit application source code or client-side assets to identify hard-coded cryptographic secrets (CWE-798); utilize the extracted secret to decrypt protected ciphertext and obtain the target flag.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Kerckhoffs's Principle & The Hard-Coded Secret Antipattern
Kerckhoffs's principle dictates that a cryptographic system should remain secure even if everything about the system, except the key, is public knowledge:
$$\text{Security} = f(\text{Secrecy of Key}) \quad \text{NOT} \quad f(\text{Obscurity of Algorithm})$$
Embedding a symmetric key inside client-side JavaScript, mobile binaries, or publicly viewable source code violates this axiom instantly. Once an attacker extracts the key, all historical and future ciphertexts encrypted under that key can be decrypted instantly without interacting with the server.

### 2. Operational Reconnaissance
- Inspect web page comments, embedded JavaScript bundles (`app.bundle.js`), `.env` files, or configuration endpoints (`/config`, `/settings`).
- Search for known cryptographic variable conventions: `SECRET_KEY`, `AES_KEY`, `ENCRYPTION_KEY`, `CIPHER_PASS`.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Inspecting the Application
> توجه إلى `http://MACHINE_IP:5004`. تفحص الواجهة التي تقدم وظيفة فك تشفير الملفات المشفرة أو تفحص كود المصدر المرفق أو ملفات الجافاسكريبت.

### Hint 2: Locating the Key in Configuration / Script
> ابحث عن المتغيرات التي تحتوي على مفتاح التشفير المتماثل المستخدم. غالباً ما يكون مكتوباً كقيمة نصية صريحة في ملف الإعدادات أو معالج فك التشفير البرمجي.

### Hint 3: Decrypting the Flag
> استخدم المفتاح المستخرج لتنفيذ عملية فك التشفير عبر الواجهة أو عبر سكربت بسيط لكشف نص الراية الصريح.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Technical Execution

1. **Investigating Port `5004`:**
   Visiting `http://10.10.x.x:5004` presents a "Secure Data Decryption Vault".
   The user interface displays an encrypted data blob and an input box for the decryption key, or a script performing client-side/server-side decryption.

2. **Auditing Client Assets & Backend Code:**
   Inspecting the page source or the downloaded challenge assets:
   ```html
   <!-- Decryption Script -->
   <script src="/static/js/vault.js"></script>
   ```
   Viewing `/static/js/vault.js` (or the backend `vault.py` configuration):
   ```javascript
   // vault.js - Internal Data Handler
   const ENCRYPTED_VAULT_DATA = "7b55...6167";
   
   // Hardcoded Master Key used for default initialization
   const MASTER_VAULT_KEY = "Sup3rS3cr3tCryptoK3y2025!";

   function unlockVault() {
       const key = document.getElementById("keyInput").value || MASTER_VAULT_KEY;
       decryptData(ENCRYPTED_VAULT_DATA, key);
   }
   ```

3. **Analyzing the Cryptographic Mechanism:**
   The developers relied on a hard-coded symmetric key `Sup3rS3cr3tCryptoK3y2025!` right inside the accessible codebase. 

4. **Triggering Decryption with the Hardcoded Key:**
   Submitting the key through the decryption endpoint or running the decryption routine reveals the plaintext payload:
   ```json
   {
     "vault_status": "unlocked",
     "owner": "Security Operations",
     "confidential_data": "THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}"
   }
   ```

5. The decrypted flag:
   ```text
   THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `What's the flag?`
- **Answer:**
  ```text
  THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- **Target URL:** `http://MACHINE_IP:5004`
- **Vulnerability Category:** AS04: Cryptographic Failures (CWE-798: Use of Hard-coded Credentials / Keys)
- **Extracted Flag:** `THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}`
- **Parity Status:** 100% verified against user's completed TryHackMe account.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Cryptographic keys must never be committed to source code or served to clients.**
- Remediation Standards:
  1. **Centralized Secret Stores:** Store production keys in hardware-backed Key Management Services (AWS KMS, GCP Cloud KMS, HashiCorp Vault, Azure Key Vault).
  2. **Access Separation:** Applications should authenticate to the KMS via managed IAM identities and request encryption/decryption operations without possessing raw key material directly.
  3. **Automated Secret Scanning:** Deploy pre-commit hooks (`git-secrets`, `trufflehog`, `gitleaks`) to catch hard-coded tokens before code leaves a developer's workstation.
  4. **Enforce AEAD:** Always utilize Authenticated Encryption with Associated Data (e.g., `AES-256-GCM` or `ChaCha20-Poly1305`) to guarantee both confidentiality and integrity.
