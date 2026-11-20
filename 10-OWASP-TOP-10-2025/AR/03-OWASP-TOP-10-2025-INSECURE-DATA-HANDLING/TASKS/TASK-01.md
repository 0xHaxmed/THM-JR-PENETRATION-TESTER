# Task 01 · Introduction (مقدمة: معالجة وتدقيق البيانات غير الآمنة)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
This room introduces 3 categories from the OWASP Top 10 (2025) standard relating to application behavior, data processing, and user input validation:
1. A04: Cryptographic Failures
2. A05: Injection
3. A08: Software or Data Integrity Failures

Learning Objectives:
- Master the practical impact of rolling custom cryptography and using weak key derivations.
- Exploit Server-Side Template Injection (SSTI) in modern Python Jinja2 applications to execute arbitrary read operations.
- Understand how accepting untrusted serialized objects (such as Python pickle) leads to arbitrary code execution and software integrity collapse.
- Implement robust input validation, parameterized queries, and safe serialization patterns.

Question:
- I'm ready! (No answer needed)
```

### 2. Arabic Pedagogical Translation
```text
تستعرض هذه الغرفة ثلاثة تصنيفات رئيسية من معيار OWASP Top 10 لعام 2025 تتعلق بسلوك التطبيقات، ومعالجة البيانات، والتحقق من صحة مدخلات المستخدمين:
1. التصنيف A04: الإخفاقات التشفيرية (Cryptographic Failures).
2. التصنيف A05: ثغرات الحقن (Injection) بمختلف أشكالها (SQLi, SSTI, Command Injection).
3. التصنيف A08: إخفاقات سلامة البرمجيات والبيانات (Software or Data Integrity Failures).

الأهداف التعليمية:
- فهم الأثر الكارثي لابتكار خوارزميات تشفير مخصصة (Rolling your own crypto) والاعتماد على مفاتيح ضعيفة قابلة للتخمين.
- استغلال ثغرات حقن القوالب على خادم الويب (Server-Side Template Injection - SSTI) في محركات قوالب بايثون الحديثة (Jinja2) لقراءة الملفات الحساسة.
- استيعاب مخاطر قبول الكائنات المتسلسلة غير الموثوقة (مثل مكتبة Python pickle) وكيف تؤدي إلى تنفيذ الأوامر التعسفي وانهيار سلامة النظام.
- تطبيق أساليب دفاعية متينة تشمل تنقية المدخلات، والاستعلامات المجهزة، وصيغ التسلسل الآمنة مثل JSON.

السؤال:
- أنا جاهز! (لا توجد إجابة مطلوبة - اضغط Complete)
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `I'm ready!`
- **Pedagogical Goal:** Outline the three core data-handling categories from OWASP 2025 and initialize the lab targets across ports `8000`, `8001`, and `8002`.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. The Data Ingestion Lifecycle & Trust Boundaries
Modern applications continuously process incoming data streams:
$$\text{Untrusted Input} \xrightarrow{\text{Parsing}} \text{Serialization Layer} \xrightarrow{\text{Evaluation}} \text{Templating / Database Engine}$$
- When input crosses trust boundaries without strict semantic type-checking, injection occurs (A05).
- When binary data streams reconstruct active memory objects without signature verification, deserialization RCE occurs (A08).
- When developers build custom encryption wrappers to "protect" data without mathematical rigor, key brute-forcing occurs (A04).

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Initializing the Machine
> انقر على زر "Start Lab Machine" الأخضر لتشغيل الهدف المشترك الذي يستضيف التحديات الثلاثة على المنافذ 8000 و 8001 و 8002.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Setup & Lab Overview
1. Deploy the lab virtual machine via TryHackMe.
2. The VM exposes three practical targets:
   - **Port `8001`:** A04 - Weak Key Derivation & Note Decryption
   - **Port `8000`:** A05 - Jinja2 Server-Side Template Injection (SSTI)
   - **Port `8002`:** A08 - Insecure Python Pickle Deserialization
3. Ensure connectivity via OpenVPN or the AttackBox.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `I'm ready!`
- **Answer:**
  ```text
  No answer needed
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified on the user's completed account:
- Room: `OWASP Top 10 2025: Insecure Data Handling` (`owasptopten2025three`)
- Status: **100% Completed**

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Input is code until proven otherwise.** The moment an application hands untrusted input to an interpreter, templating engine, or deserializer, execution context is ceded to the user.
