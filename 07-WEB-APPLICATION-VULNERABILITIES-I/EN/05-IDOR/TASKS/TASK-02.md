# Task 02 · An IDOR Example (مثال عملي لاستغلال ثغرة IDOR)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

The simplest and most prevalent form of IDOR occurs when an application places an internal database record identifier directly within a URL query parameter or request path and uses it to query the database without validating user ownership.

### Scenario Breakdown:
Suppose you register for an account on an online service (`http://online-service.thm`) and navigate to your user profile. The URL appears as:
```
http://online-service.thm/profile?user_id=1305
```
The parameter `user_id=1305` instructs the backend database to execute:
```sql
SELECT * FROM users WHERE id = 1305;
```
The application renders your username, email, phone number, and account balance.

Now, suppose you alter the query parameter in the browser's address bar to `1000`:
```
http://online-service.thm/profile?user_id=1000
```
If the web server returns the profile details of another customer (User 1000), the application has a confirmed IDOR vulnerability. The backend code blindly took the user-supplied integer, fetched record 1000, and returned it to the client without verifying whether the active session belongs to User 1000.

### Write / Mutation IDOR Operations:
IDOR is not limited to read operations (`GET`). If an API accepts identifiers in state-changing endpoints (e.g., `POST /api/user/update-email` with body `{"user_id": 1000, "email": "attacker@evil.thm"}`), omitting authorization checks allows an attacker to overwrite another user's email, triggering a password reset and achieving full account takeover.

### Objective:
Access the interactive IDOR Example exercise on TryHackMe, tamper with the user profile parameter, locate the unauthorized record, and extract the flag.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Automation & Mass Enumeration:**
   - Once an IDOR is confirmed on an integer identifier, an attacker can use simple automated tools (Burp Intruder, Python `requests`, or `ffuf`) to iterate through sequential numbers (`1` to `100000`), dumping the entire customer database in minutes.
2. **Rate Limiting & Detection:**
   - Sequential IDOR enumeration produces noticeable bursts of predictable HTTP GET/POST requests. Blue teams detect IDOR enumeration by tracking sudden spikes in requests from a single session targeting diverse object IDs.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Tier 1 (Conceptual):** Inspect the URL parameters in the practical lab view. Notice the parameter that references your account ID.
- **Tier 2 (Practical):** Increment or decrement the ID number in the URL (e.g. from your assigned ID to another ID such as `1000` or `1`).
- **Tier 3 (Direct):** Navigating to the tampered user record reveals the hidden flag in the profile card: `THM{IDOR-VULN-FOUND}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Step-by-Step Practical Reproduction:

1. Open the interactive lab site by clicking **View Site**.
2. Note your current assigned profile URL:
   ```
   http://online-service.thm/profile?user_id=1305
   ```
3. In the browser address bar, modify the parameter to point to an administrative or target account:
   ```
   http://online-service.thm/profile?user_id=1000
   ```
4. Press Enter. The backend executes:
   ```php
   // Vulnerable Server-Side Code:
   $id = $_GET['user_id'];
   $stmt = $pdo->prepare("SELECT * FROM profiles WHERE user_id = ?");
   $stmt->execute([$id]);
   $profile = $stmt->fetch();
   // Renders data directly without checking $_SESSION['user_id'] == $id
   ```
5. The rendered webpage displays the private profile data of user 1000, including the flag:
   ```
   Flag: THM{IDOR-VULN-FOUND}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the Flag from the IDOR example website?*
  - **Answer:** `THM{IDOR-VULN-FOUND}`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Flag `THM{IDOR-VULN-FOUND}` submitted and verified as 100% correct by the TryHackMe grading engine.
- Direct parameter modification verified via standard browser developer tools network panel.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** أبسط صور ثغرات الـ IDOR هي تمرير المعرفات الرقمية الصريحة (`Plaintext IDs`) في معاملات الروابط؛ حيث يكتفي المهاجم بتغيير الرقم في شريط المتصفح للوصول إلى سجلات مستخدمين آخرين بالكامل. الحماية الحقيقية تتطلب من الكود البرمجي استخراج معرف المستخدم حصرياً من جلسة الخادم الآمنة (`$_SESSION['user_id']`) أو التحقق الصارم من ملكية السجل قبل عرضه.
