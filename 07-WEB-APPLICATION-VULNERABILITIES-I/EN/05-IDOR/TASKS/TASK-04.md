# Task 04 · Finding IDORs in Hashed IDs (اكتشاف ثغرات IDOR في المعرفات المبتورة بدوال الهاش)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

When developers recognize that Base64 encoding provides no obfuscation, they sometimes turn to cryptographic hash functions (such as MD5, SHA-1, or SHA-256) to disguise user and object identifiers.

A hashed identifier presents itself as a fixed-length string of hexadecimal characters:
- **MD5:** 32 hexadecimal characters (128 bits)
- **SHA-1:** 40 hexadecimal characters (160 bits)
- **SHA-256:** 64 hexadecimal characters (256 bits)

### The Predictability Flaw:
While a cryptographic hash function is mathematically one-way (irreversible), it is strictly **deterministic**: the same input string always produces the exact same hash output.

If the application hashes predictable or sequential values (such as database integers `1`, `2`, `3`, or sequential user IDs), the hash provides almost zero protection:
1. Integer `1` hashed with MD5 is always: `c4ca4238a0b923820dcc509a6f75849b`
2. Integer `2` hashed with MD5 is always: `c81e728d9d4c2f636f067f89cc14862c`
3. Integer `123` hashed with MD5 is always: `202cb962ac59075b964b07152d234b70`

An attacker who observes a 32-character hexadecimal string in a request can simply query online precomputed lookup tables (such as **CrackStation**) or generate a local rainbow table of MD5 hashes for integers `1` through `100000`. By replacing the hash in the request, the attacker accesses unauthorized records just as easily as with plaintext IDs.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Hash Identification Techniques:**
   - On Linux / Kali, penetration testers can quickly identify unknown hash signatures using automated tools:
     ```bash
     hash-identifier "202cb962ac59075b964b07152d234b70"
     # Or using python hashid:
     hashid 202cb962ac59075b964b07152d234b70
     ```
2. **Salted vs Unsalted Hashes:**
   - If a developer salts the hash (`md5($id . $secret_salt)`), rainbow table lookups fail. However, if the salt is static or predictable, or if the server still fails to verify authorization upon receiving a valid hash obtained elsewhere, the vulnerability remains.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** What is the ubiquitous, legacy 128-bit hash algorithm that generates a 32-character hexadecimal digest, frequently misused by legacy applications to hash identifiers? (3 letters: m__).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Analysis of Question 1:
- *Question:* What is a common algorithm used for hashing IDs?
- *Explanation:* **MD5** (Message Digest Algorithm 5) is one of the most common legacy hashing algorithms historically implemented by web developers to obscure IDs. Due to its fixed 32-character output and speed, precomputed lookup tables can reverse predictable integer inputs in milliseconds.
- *Answer:* `md5`

### 2. Practical Bash / Python Exploitation Pipeline:

Suppose a target URL uses an MD5 hash parameter:
```
http://online-service.thm/view_order?ref=c4ca4238a0b923820dcc509a6f75849b
```

To test if sequential order numbers are hashed, a tester can generate MD5 hashes on the command line:
```bash
# Calculate MD5 hash for Order 1:
echo -n "1" | md5sum | awk '{print $1}'
# Output: c4ca4238a0b923820dcc509a6f75849b (MATCH!)

# Calculate MD5 hash for Order 2:
echo -n "2" | md5sum | awk '{print $1}'
# Output: c81e728d9d4c2f636f067f89cc14862c
```

Navigating to `http://online-service.thm/view_order?ref=c81e728d9d4c2f636f067f89cc14862c` confirms access to Order 2!

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is a common algorithm used for hashing IDs?*
  - **Answer:** `md5`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Answer `md5` verified and confirmed on TryHackMe platform engine.
- Hash generation and length matching (32 hex characters) validated.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** تجزئة المعرفات باستخدام دوال الهاش مثل `md5` لا تغني عن فحص التفويض؛ فدوال الهاش قطعية (Deterministic)، وعند تطبيقها على مدخلات صغيرة ومتوقعة كالأرقام التسلسلية، يستطيع المهاجم توليدها أو فكها فورياً باستخدام جداول البحث المسبقة (Lookup Tables) وتجاوز محاولة الإخفاء السطحية بالكامل.
