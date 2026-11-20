# Task 05 · DOM Based XSS (البرمجة النصية المستندة إلى نموذج كائن المستند)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

**DOM-Based Cross-Site Scripting (DOM XSS)** occurs when the vulnerability exists entirely on the client side within the browser's Document Object Model (DOM).

In a traditional Reflected or Stored XSS attack, the server receives the malicious payload and reflects or serves it back inside the HTTP response body. In **DOM XSS**, the server might never see or process the malicious script! The entire attack chain—from source to sink—takes place inside the client-side JavaScript executing in the user's browser.

### The Mechanics of DOM XSS:
DOM XSS requires two fundamental components:
1. **The Source:** A JavaScript property or API through which untrusted user input enters the application. Examples:
   - `location.search` (URL query parameters)
   - `location.hash` (the URL fragment following `#`, which is never transmitted to the web server!)
   - `document.referrer`
   - `window.name`
2. **The Sink:** A dangerous JavaScript function or DOM object that can execute code or render raw HTML when supplied with untrusted input. Examples:
   - `eval()`
   - `document.write()` / `document.writeln()`
   - `element.innerHTML` / `element.outerHTML`
   - `setTimeout()` / `setInterval()` when receiving string arguments

### Attack Scenario:
Consider a client-side JavaScript script on a page:
```javascript
// Reading from the Source:
var hashValue = window.location.hash.substring(1);

// Writing directly into an Execution Sink:
document.getElementById('content').innerHTML = "Welcome " + hashValue;
```
If an attacker sends a victim to:
`https://target.thm/page.html#<img src=x onerror=alert('DOM_XSS')>`
The text after `#` is processed entirely inside the client's browser without ever hitting the web server access logs. The browser evaluates `innerHTML`, injects the `<img>` tag, fails to load image `x`, and immediately executes `alert('DOM_XSS')`.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **WAF Blindness (The `#` Fragment Advantage):**
   - HTTP protocol specifications dictate that the URL fragment identifier (anything after `#`) is **strictly client-side**. Browsers never send the fragment to the server in the HTTP GET request line.
   - Consequently, traditional network Web Application Firewalls (WAFs), reverse proxies, and server-side log analyzers **cannot detect** or block DOM XSS payloads residing in `location.hash`!
2. **Auditing Client-Side Source Code:**
   - Security auditors must search frontend JavaScript bundles for dangerous sinks. Modern static analysis tools search for patterns where user-controlled sources reach `eval()` or `innerHTML`.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** What is the notorious built-in JavaScript function that takes any string passed to it and evaluates/executes it directly as JavaScript code? (4 characters: e___).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Architectural Source-to-Sink Data Flow:

```
[ User Browser Address Bar ]
URL: https://target.thm/app#user_input
                  │
                  ▼
         [ SOURCE (JavaScript) ]
         window.location.hash
                  │
                  │ (Flows unsanitized through script variables)
                  ▼
          [ SINK (Execution) ]
         eval(code)  OR  element.innerHTML = input
                  │
                  ▼
         [ Arbitrary Code Executed ]
         Browser executes injected JavaScript in current origin!
```

### 2. Analysis of Question 1:
- *Question:* What unsafe JavaScript method is good to look for in source code?
- *Explanation:* The `eval()` function evaluates JavaScript code represented as a string. It is widely regarded as one of the most dangerous functions in programming because any string passed to it executes with the full privileges of the caller. When auditing JavaScript source code, searching for `eval()` is a standard technique to identify potential remote code execution or DOM XSS sinks.
- *Answer:* `eval()`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What unsafe JavaScript method is good to look for in source code?*
  - **Answer:** `eval()`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Answer `eval()` validated by the TryHackMe platform engine.
- Verified in developer tools: `eval("alert('DOM XSS Verified')")` triggers immediate alert box execution.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** هجمات الـ DOM-Based XSS تحدث بالكامل داخل متصفح الضحية عندما تقرأ أكواد الجافاسكريبت بيانات من مصدر (`Source`) مثل `location.hash` وتمررها إلى دالة تنفيذ خطيرة (`Sink`) مثل `eval()` أو `innerHTML` دون تعقيم. وتتميز هذه الثغرة بقدرتها على تجاوز جدران الحماية الخادمة (WAFs) لأن محتوى الـ Fragment (`#`) لا يُرسل أصلاً إلى الخادم.
