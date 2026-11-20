# Task 01 · Introduction (مقدمة: عيوب التصميم المعماري للأنظمة والتطبيقات)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
This room breaks down vulnerabilities related to failures in architecture and system design from the OWASP Top 10 2025 standard.

Categories Covered:
1. AS02: Security Misconfigurations
2. AS03: Software Supply Chain Failures
3. AS04: Cryptographic Failures
4. AS06: Insecure Design

Learning Objectives:
- Identify and exploit verbose error disclosure leading to internal data leakage.
- Dissect vulnerable third-party components and software supply chain risks.
- Audit cryptographic implementations, locate hard-coded secrets, and understand key lifecycle risks.
- Analyze flawed architectural assumptions (e.g., client identity trust) that lead to insecure design compromises.
- Understand modern threats introduced by AI/ML integrations into application architectures.

Question:
- I am ready to learn about design flaw vulnerabilities! (No answer needed)
```

### 2. Arabic Pedagogical Translation
```text
تستعرض هذه الغرفة مجموعة الثغرات الأمنية المرتبطة بالقصور في البنية المعمارية وتصميم الأنظمة استناداً إلى معيار OWASP Top 10 لعام 2025 المحدث.

التصنيفات المشمولة في الغرفة:
1. التصنيف AS02: التهيئة الأمنية الخاطئة (Security Misconfigurations).
2. التصنيف AS03: إخفاقات سلسلة توريد البرمجيات (Software Supply Chain Failures).
3. التصنيف AS04: الإخفاقات التشفيرية (Cryptographic Failures).
4. التصنيف AS06: التصميم غير الآمن والافتراضات الخاطئة (Insecure Design).

الأهداف التعليمية:
- كشف واستغلال رسائل الخطأ التفصيلية (Verbose Errors) التي تؤدي لتسريب البيانات الحساسة وهياكل الأنظمة.
- تفكيك المكونات والمكتبات البرمجية القديمة أو الملغومة وتحليل مخاطر سلاسل التوريد.
- فحص آليات التشفير واستخراج المفاتيح الصلبة (Hard-coded Keys) وفهم إدارة دورة حياة المفاتيح.
- تحليل الافتراضات المعمارية الخاطئة (مثل الثقة العمياء في نوع المتصفح أو العميل) واستغلال ثغرات التصميم.
- استيعاب التهديدات الحديثة المصاحبة لدمج الذكاء الاصطناعي (AI/ML) في تصاميم التطبيقات.

السؤال:
- أنا جاهز لتعلم ثغرات عيوب التصميم المعماري! (لا توجد إجابة مطلوبة - اضغط Complete)
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `I am ready to learn about design flaw vulnerabilities!`
- **Pedagogical Goal:** Establish the architectural foundation separating implementation bugs (code flaws) from design and configuration flaws; initialize the lab environment.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Bugs vs Design Flaws: The Fundamental Distinction
$$\text{Vulnerabilities} = \begin{cases} \text{Implementation Bugs} & \text{(Buffer overflows, SQLi, XSS) } \longrightarrow \text{Fixed by patching code} \\ \text{Design / Configuration Flaws} & \text{(Weak trust models, bad crypto, open S3) } \longrightarrow \text{Requires redesign} \end{cases}$$

- A bug is an unintentional error in code execution. The design was intended to be secure, but the implementation deviated.
- A design flaw is an intentional architectural decision that inherently lacks defensibility (e.g., designing an API that trusts client-provided headers without server-side verification). No amount of code sanitization can fix a fundamentally broken architectural design.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Completing the Task
> هذه المهمة تمثل المدخل التمهيدي للمعمل. انقر على زر "Start Lab Machine" لتشغيل المعمل ثم اضغط على زر Complete لتسجيل إتمام المهمة الأولى.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Setup & Lab Initialization
1. Deploy the lab virtual machine via the TryHackMe interface.
2. The VM exposes multiple target challenge services across dedicated HTTP ports:
   - Port `5002`: AS02 - Security Misconfigurations (User Management API)
   - Port `5003`: AS03 - Software Supply Chain Failures (`/api/process`)
   - Port `5004`: AS04 - Cryptographic Failures (Hard-coded Key Decryption)
   - Port `5005`: AS06 - Insecure Design (Mobile Client Assumption)
3. Ensure network connectivity via TryHackMe OpenVPN or the internal AttackBox.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `I am ready to learn about design flaw vulnerabilities!`
- **Answer:**
  ```text
  No answer needed
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified on the user's completed account:
- Room: `OWASP Top 10 2025: Application Design Flaws` (`owasptopten2025two`)
- Status: **100% Completed**

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Architecture sets the ceiling for software security.** Writing memory-safe code in Rust or Go cannot protect an application if the fundamental design assumes client headers are tamper-proof or if production error handlers output raw database connection strings.
