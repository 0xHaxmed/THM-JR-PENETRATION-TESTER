# Task 03 · Reflected XSS (البرمجة النصية المنعكسة عبر المواقع)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

**Reflected Cross-Site Scripting (Reflected XSS)** happens when user-supplied input delivered in an HTTP request is immediately included in the server's immediate HTTP response without proper validation, filtering, or output sanitization.

### Vulnerability Dynamics:
- **Non-Persistent Nature:** The payload is never stored in the database. It exists only for the duration of that single HTTP transaction.
- **Delivery Vector:** Because the server does not store the payload, an attacker must craft a malicious URL containing the script payload in its query parameters and trick the victim into clicking that specific link (or load it within an external `<iframe>`).
- **Common Scenarios:**
  - Search query echo: *"Results for: `<script>...`"*
  - Error messages: `https://vulnerable.thm/login.php?error=<script>...`
  - Feedback / Confirmation banners: `https://vulnerable.thm/welcome.php?name=<script>...`

### Primary Testing Entry Points:
1. **URL Query String Parameters:** The most frequent location where user input is reflected into the page DOM.
2. **URL File Paths:** In RESTful routing patterns where URLs map directly into headings or breadcrumbs.
3. **HTTP Request Headers:** Such as `User-Agent` or `Referer` (though much harder to exploit in real victims unless chained with other browser bugs).

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Social Engineering Dependency:**
   - Unlike Stored XSS which triggers automatically for anyone browsing the affected page, Reflected XSS strictly requires an **active delivery mechanism** (phishing email, Discord/Slack direct message, malicious forum link, or watering-hole website embedding an iframe).
2. **URL Encoding & Defense Evasion:**
   - Browsers automatically URL-encode special characters in the address bar (e.g., `<` becomes `%3C`). Modern web applications often decode these values server-side before reflecting them into the template. Security testers must analyze whether the server decodes values before reflection, and if so, how it handles double-encoding (`%253C`).

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** Look at the standard components of a URL (`protocol://hostname:port/path?query#hash`). Where in the URL does the user pass key-value pairs (like `?q=test&page=1`) that are commonly reflected on search results or error pages? (Plural word starting with P).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Reflected XSS Transaction Flow:

```
[ Attacker crafts malicious link: ]
https://vulnerable.thm/search?q=<script>alert('ReflectedXSS')</script>
         │
         │ (1. Social engineering: Attacker sends link to Victim)
         ▼
[ Victim Browser clicks link ]
         │
         │ (2. Sends GET request with payload in query string)
         ▼
[ Vulnerable Web Server ]
Processes template:
<h1>Search Results for: <?php echo $_GET['q']; ?></h1>
         │
         │ (3. Server responds with raw payload embedded in HTML body)
         ▼
[ Victim Browser ]
Renders: <h1>Search Results for: <script>alert('ReflectedXSS')</script></h1>
Parser encounters <script> tag and EXECUTES JavaScript in Victim session!
```

### 2. Analysis of Question 1:
- *Question:* Where in an URL is a good place to test for reflected XSS?
- *Explanation:* The query string in a URL is composed of **parameters** (e.g. `?search=...`, `?id=...`, `?msg=...`). Web applications continuously read these parameter values and display them back to users in search summaries, error alerts, pagination trackers, and form input values. Therefore, parameters are the primary testing ground for Reflected XSS.
- *Answer:* `parameters`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *Where in an URL is a good place to test for reflected XSS?*
  - **Answer:** `parameters`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Answer `parameters` confirmed on the TryHackMe platform engine.
- Validated against OWASP testing guide (WSTG-INPV-01: Testing for Reflected Cross Site Scripting).

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** يحدث هجوم الـ Reflected XSS عندما يعكس خادم الويب مدخلات المستخدم القادمة عبر معاملات الرابط (`parameters`) مباشرة في صفحة الرد دون تشفير أو تعقيم (Sanitization). وتتطلب هذه الثغرة خداع الضحية للضغط على رابط مخصص ليتم تنفيذ الكود داخل متصفحه حصرياً.
