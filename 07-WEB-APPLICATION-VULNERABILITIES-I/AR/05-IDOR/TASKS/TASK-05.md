# Task 05 · Finding IDORs in Unpredictable IDs (اكتشاف ثغرات IDOR في المعرفات العشوائية وغير القابلة للتخمين)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

Modern enterprise architectures frequently avoid sequential integers by adopting globally unique, high-entropy identifiers such as **UUIDs (Universally Unique Identifiers)** or GUIDs:
```
d3b07384-d9a0-4e9b-8b3c-2f1a6c7e4a90
```
Because a standard version 4 UUID contains 122 bits of pseudo-random entropy, an attacker cannot guess, brute-force, or enumerate sequential IDs across other accounts.

### The Critical Architectural Truth:
Using unpredictable IDs **does NOT fix an IDOR vulnerability**.
It merely eliminates one specific attack vector: *sequential enumeration*. If the backend endpoint still blindly accepts an identifier without validating whether the authenticated user owns that resource, the endpoint remains vulnerable to Insecure Direct Object Reference. The attacker simply needs to obtain a legitimate victim identifier through other channels.

### Real-World UUID / Identifier Leakage Channels:
- **Shared URLs & Collaboration Links:** When users share project links, invite codes, or public dashboard URLs.
- **Leaked API Responses:** Endpoints that return metadata arrays of other users containing their UUIDs.
- **Client-Side HTML / JavaScript Source:** Debug data embedded in DOM data attributes (`data-uuid="..."`).
- **Transactional Emails & Notifications:** "User X commented on your post" links containing User X's internal UUID.
- **Exported CSV / PDF Reports:** Internal reports displaying record UUIDs.

### The Two-Account Testing Methodology:
To prove an IDOR exists when identifiers cannot be guessed, penetration testers apply the **Two-Account Technique**:
1. Provision two independent user accounts: **Account A** (Victim) and **Account B** (Attacker).
2. Authenticate as Account A, navigate to the target feature (e.g. view private document), and capture the resource UUID.
3. Authenticate as Account B, make the same request, but substitute Account A's captured UUID into Account B's request.
4. If the server delivers Account A's confidential data to Account B, the access control layer is broken!

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Separation of Concerns (Entropy vs Permission):**
   - Security engineers must clearly separate two questions:
     - Question 1: *Can an attacker predict the ID?* (Entropy / Identifier Design)
     - Question 2: *Does the server verify ownership before returning the record?* (Authorization)
   - An application is only secure if the answer to Question 2 is an unconditional **YES**. Relying on UUIDs as the sole defense is a classic violation of the principle of Complete Mediation.
2. **Autorize (Burp Suite Extension):**
   - In commercial penetration tests, performing the two-account technique across hundreds of endpoints is automated using the **Autorize** BApp plugin. Autorize intercepts the tester's traffic as User A, automatically replays every request using User B's session cookies (and unauthenticated), and flags endpoints that return matching HTTP status codes and response lengths.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** To test whether User B can access User A's private data, what is the absolute minimum number of test accounts a security tester must register? (A single digit number).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Analysis of Question 1:
- *Question:* What is the minimum number of accounts you need to create to check for IDORs between accounts?
- *Explanation:* Testing for horizontal privilege escalation or unauthorized data access between peers requires at least **two** accounts: one to establish the legitimate baseline resource and one to attempt the unauthorized access.
- *Answer:* `2`

### 2. Conceptual Diagram of the Two-Account Verification Protocol:

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                  THE TWO-ACCOUNT IDOR AUDIT PROTOCOL                    |
|                                                                                         |
|  [ Account A (Victim Session) ]                                                         |
|  Authenticates ➔ Fetches /api/invoices ➔ Receives: UUID "a1b2-c3d4-e5f6"                |
|                                                                                         |
|  [ Account B (Attacker Session) ]                                                       |
|  Authenticates ➔ Submits Request:                                                       |
|  GET /api/invoice/download?id=a1b2-c3d4-e5f6                                            |
|  Cookie: session_B=xyz789                                                               |
|                                                                                         |
|  [ Server-Side Evaluation ]                                                             |
|  Check 1: Is session_B valid? YES                                                       |
|  Check 2: Does session_B own invoice "a1b2-c3d4-e5f6"?                                  |
|           - IF Server checks ownership ➔ Returns 403 Forbidden (SECURE)                |
|           - IF Server ignores ownership ➔ Returns Account A's Invoice (IDOR VULNERABLE!)|
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the minimum number of accounts you need to create to check for IDORs between accounts?*
  - **Answer:** `2`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Answer `2` submitted and accepted on TryHackMe platform.
- Validated against standard OWASP Access Control Testing Methodology (WSTG-ATHZ-02).

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** استخدام معرّفات عشوائية صعبة التخمين كـ `UUID` يمنع هجمات التعداد التسلسلي (`Sequential Enumeration`) فقط، لكنه لا يحمي من ثغرات الـ IDOR؛ فالمعرّفات تتسرب بسهولة عبر الروابط والتقارير وواجهات الـ API. والسبيل الوحيد لاختبار هذه الثغرات هو "منهجية الحسابين" (`Two-Account Technique`) بحد أدنى لحسابين (`2`) للتحقق من فحص ملكية السجل.
