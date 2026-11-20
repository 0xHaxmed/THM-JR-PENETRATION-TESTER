# Task 01 · What is an IDOR? (ما هي ثغرة المرجع المباشر غير الآمن للكائنات؟)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

Web applications rely on identifiers to distinguish one object from another. A user profile, an invoice, a support ticket, and a private document each have some kind of reference (often a number or a string) that the application uses internally to locate them. When an application allows the user to supply that reference and then retrieves the corresponding object without checking whether the user is permitted to access it, the result is an **Insecure Direct Object Reference (IDOR)**.

### Classification & Standard Taxonomies:
- **OWASP Top 10 (Web):** Sits under Category A01: **Broken Access Control** (Position #1).
- **OWASP API Security Top 10:** Classified under API1:2023 as **Broken Object Level Authorization (BOLA)**.
- **MITRE CWE:** Classified primarily under **CWE-639: Authorization Bypass Through User-Controlled Key**.

### The Core Vulnerability Dynamics:
What makes IDOR particularly dangerous is the massive gap between its simplicity and its catastrophic impact. Exploiting an IDOR often requires nothing more than changing a number or string in a URL query parameter or JSON request body. There is no memory corruption, no shellcode injection, and no session hijacking required. Yet the consequences range from massive private data leaks (PII) to horizontal/vertical privilege escalation and full account takeover.

### Learning Objectives:
- Define IDOR and its exact relationship to Broken Access Control.
- Recognize the different formats object references take (plaintext, encoded, hashed, UUID).
- Audit web applications for hidden IDOR vectors across APIs and background traffic.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Authentication vs Authorization:**
   - The root cause of IDOR is the confusion between *Identity* and *Permission*.
   - **Authentication:** Verifies *who you are* (e.g. Valid session cookie for User ID 1305).
   - **Authorization:** Verifies *what you are permitted to do* (e.g. Can User ID 1305 edit User ID 1000's record?).
   - Applications with IDOR have working authentication, but missing object-level authorization.
2. **Horizontal vs Vertical IDOR:**
   - **Horizontal IDOR:** A standard user accesses another standard user's private data at the same privilege tier (User A viewing User B's invoices).
   - **Vertical IDOR:** A standard user modifies parameters to access administrative resources (User A editing Admin parameters to access system configuration).

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** What four-letter acronym expands from Insecure Direct Object Reference? The question asks for the full expansion of IDOR (Four spaced words: I_______ D_____ O_____ R________).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Conceptual Breakdown of Broken Access Control:

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    THE IDOR VULNERABILITY MODEL                         |
|                                                                                         |
|  [ Authenticated User: Alice (User ID: 105) ]                                           |
|                 │                                                                       |
|                 │ 1. Submits GET Request with Tampered ID:                              |
|                 │    GET /api/documents?doc_id=999                                      |
|                 ▼                                                                       |
|      [ Web Application Server ]                                                         |
|         Step A: Is user authenticated? YES! (Valid Session)                             |
|         Step B: Is user authorized for doc_id 999?                                      |
|                 [MISSING AUTHORIZATION CHECK!]                                          |
|                 │                                                                       |
|                 ▼ 2. Queries Database: SELECT * FROM docs WHERE id = 999                |
|      [ Database / Object Store ]                                                        |
|                 │                                                                       |
|                 ▼ 3. Returns Bob's Confidential Tax Return!                             |
|  [ Alice receives Bob's private data without authorization! ]                           |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

### 2. Analysis of Question 1:
- *Question:* What does IDOR stand for?
- *Explanation:* IDOR stands for **Insecure Direct Object Reference**. It occurs whenever an internal object key exposed to a client is queried directly without verifying permissions.
- *Answer:* `Insecure Direct Object Reference`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What does IDOR stand for?*
  - **Answer:** `Insecure Direct Object Reference`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Answer `Insecure Direct Object Reference` verified and confirmed on TryHackMe platform.
- Mapped against OWASP Top 10:2021 Category A01 (Broken Access Control) and CWE-639.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** تنشأ ثغرة الـ IDOR (`Insecure Direct Object Reference`) عندما يفصل النظام بين المصادقة والتفويض؛ فالخادم يتحقق بنجاح من هوية المستخدم (`Authentication`)، لكنه يثق ثقة عمياء في المعرّف الممرر في الطلب دون التحقق مما إذا كان المستخدم يملك حق الوصول الفعلي لهذا الكائن بالذات (`Object-Level Authorization`).
