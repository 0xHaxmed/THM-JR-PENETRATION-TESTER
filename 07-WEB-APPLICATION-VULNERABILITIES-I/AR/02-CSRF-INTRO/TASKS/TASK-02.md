# Task 02 · What is CSRF (ما هو هجوم تزوير الطلبات عبر المواقع؟)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

**Cross-Site Request Forgery (CSRF)** is an attack that forces an authenticated end user to execute unwanted actions on a web application in which they are currently authenticated. With a little help of social engineering (such as sending a link via email or chat), an attacker may trick the users of a web application into executing actions of the attacker's choosing.

A successful CSRF attack targets functionality that causes a state change on the server, such as changing the victim's email address or password, or purchasing an item. If the victim is an ordinary user, a successful CSRF attack can force the user to perform state-changing requests like transferring funds, changing their email address, and so forth. If the victim is an administrative account, CSRF can compromise the entire web application.

### The Attack Flow:
1. **Victim logs in** to the legitimate application (`http://staffhub.thm:8080`). The browser stores the session cookie.
2. **Attacker lures the victim** into visiting a malicious webpage (`http://attacker.thm/evil.html`) hosted by the attacker.
3. **Victim's browser automatically submits** an unauthorized request to the target application while including the victim's valid session cookie.
4. **Target web application receives and processes** the request, believing it was deliberately authorized by the authenticated victim.

```
+-----------------------------------------------------------------------------------------+
|                                    CSRF ATTACK FLOW                                     |
|                                                                                         |
|  1. Login & Auth           +-------------------------+                                  |
|  ======================>   |   Victim Web Browser    | <==============================+ |
|                            | (Holds Valid Session)   |                                | |
|                            +-------------------------+                                | |
|                                |                 ^                                    | |
|  3. Forged Request             |                 | 2. Lure to Malicious Page          | |
|     (With Session Cookies!)    |                 |    (Phishing / Social Eng)         | |
|                                v                 |                                    | |
|                    +--------------------+   +-----------------------+                 | |
|                    | Target Application |   |  Attacker Web Server  |                 | |
|                    | (staffhub.thm:8080)|   | (evil.thm / Exploit)  |                 | |
|                    +--------------------+   +-----------------------+                 | |
|                             |                                                         | |
|  4. State Change Processed  v                                                         | |
|     (e.g., Email Updated to Attacker)                                                 | |
+-----------------------------------------------------------------------------------------+
```

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Ambient Authority Dilemma:**
   - Computer science describes this vulnerability pattern as the *Confused Deputy Problem*. The web browser acts as a "deputy" on behalf of the user, possessing the authority (cookies) to make changes. The attacker tricks the deputy into using its authority for malicious intent.
2. **Scope of Exploitation vs Data Exfiltration:**
   - Note the critical difference: **CSRF is fundamentally a state-changing attack, NOT an information-stealing attack**. Because of the Same-Origin Policy (SOP), an external malicious site cannot read the response body returned by the victim server (unless CORS is misconfigured). The attacker cannot *view* data via pure CSRF; they can only force the server to *execute* actions.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** Look at the fundamental dynamic being compromised. The web application assumes that because the request contains valid credentials, it can automatically rely on or believe the sender. What is that psychological or architectural bond called? (A 5-letter word starting with T).
- **Hint for Question 2:** When you log into any website, what specific data structure does the browser store and automatically append to every subsequent HTTP header destined for that domain? (Starts with C).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Conceptual Breakdown of Questions:

#### Question 1 Analysis:
- *Question:* What relationship between the browser and the web application does a CSRF attack abuse?
- *Technical Reality:* Web applications are stateless over HTTP. To maintain a user session, applications issue credentials and establish a relationship where the server **trusts** requests coming from the browser containing those credentials. CSRF exploits the fact that the web application cannot distinguish whether the user actively clicked the button or an external JavaScript payload triggered it.
- *Answer:* `Trust`

#### Question 2 Analysis:
- *Question:* What does the browser automatically include with requests after login?
- *Technical Reality:* When a web application sets an authentication token using `Set-Cookie: session_id=XYZ`, the browser saves it in its internal cookie store. According to standard HTTP cookie specifications (RFC 6265), unless restricted by explicit attributes like `SameSite=Strict`, the browser automatically transmits these **Cookies** in the `Cookie:` header of any HTTP request sent to that target host.
- *Answer:* `Cookies`

### 2. HTTP Protocol Demonstration:

When an attacker's website initiates a background request:
```http
POST /update_email.php HTTP/1.1
Host: staffhub.thm:8080
User-Agent: Mozilla/5.0 (X11; Linux x86_64) ...
Accept: text/html,application/xhtml+xml...
Referer: http://attacker.thm/malicious_payload.html
Cookie: PHPSESSID=d3b07384d113edec49eaa6238ad5ff00
Content-Type: application/x-www-form-urlencoded
Content-Length: 30

email=attacker@evilmail.thm
```

Because the browser automatically attaches `Cookie: PHPSESSID=...`, the backend PHP script executes:
```php
<?php
session_start();
if (isset($_SESSION['user_id'])) {
    $email = $_POST['email'];
    update_user_email($_SESSION['user_id'], $email);
}
?>
```
The server checks `$_SESSION['user_id']`, sees a valid session, and commits the database update without realizing the request originated from `http://attacker.thm/`.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What relationship between the browser and the web application does a CSRF attack abuse?*
  - **Answer:** `Trust`
- **Question 2:** *What does the browser automatically include with requests after login?*
  - **Answer:** `Cookies`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Both answers verified directly against the TryHackMe platform engine and accepted with 100% accuracy.
- Demonstrated matching behavior in standard browser developer tools network log (Network tab -> Request Headers -> Cookie).

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** لا تستطيع خوادم الويب وحدها معرفة ما إذا كان الطلب نابعاً من إرادة حقيقية للمستخدم أو مفروضاً عليه عبر كود خبيث؛ فالخادم يعتمد على علاقة الثقة (`Trust`) بملفات تعريف الارتباط (`Cookies`). لذلك، أي إجراء يغير حالة الحساب (State-changing) يجب أن يقترن بدليل إضافي يثبت أن المستخدم هو من أنشأ الطلب عمداً من داخل واجهة التطبيق الشرعية.
