# Task 06 · Where are IDORs located (مواقع وأشكال ظهور ثغرات IDOR في التطبيقات)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

A common mistake made by beginner penetration testers is restricting their search for IDOR vulnerabilities to the query parameters visibly displayed in the browser's address bar. In modern Single Page Applications (SPAs) and API-driven architectures, the vast majority of state-changing transactions and data retrieval flows operate in the background.

### The Comprehensive IDOR Attack Surface:

#### 1. Background Asynchronous Requests (AJAX / Fetch API):
When an authenticated user loads their dashboard, the address bar might simply display `https://portal.thm/dashboard`. Behind the scenes, the browser issues dozens of background requests visible only via developer tools or Burp Suite:
```http
GET /api/v1/customer?id=15 HTTP/1.1
Host: portal.thm
```
If the backend does not validate that `id=15` belongs to the requesting session, changing the parameter in an intercepted request unlocks unauthorized records.

#### 2. RESTful Path Segments:
Modern APIs structure object references as discrete path segments rather than query strings:
```
GET /api/v2/users/105/invoices/9924
DELETE /api/v2/workspaces/marketing/documents/741
PUT /api/v2/projects/482/members/31
```
Each numerical or textual path segment represents an object reference that must be tested for horizontal and vertical tampering.

#### 3. POST and PUT Request Bodies (JSON / Form Data):
Sensitive operations submit identifiers inside structured message bodies:
```json
{
  "order_id": 54210,
  "shipping_address_id": 883,
  "discount_coupon_code": "VIP2026"
}
```
Modifying `order_id` or `shipping_address_id` in the JSON payload often reveals IDOR flaws.

#### 4. Cookie Values & Custom Request Headers:
Applications sometimes store the active tenant ID, account ID, or role identifier inside custom headers or cookies:
```http
X-Target-User-ID: 105
Cookie: active_profile_id=105; session=...
```

#### 5. Parameter Mining (Hidden & Undocumented Parameters):
Developers frequently add parameters during development for debugging purposes (e.g., allowing developers to view another user's view via `?debug_user=admin` or `?user_id=123` on an endpoint that normally defaults to the current session). Tools like `Arjun` automate parameter discovery against such endpoints.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Passive vs Active Inspection:**
   - The golden rule of Web API auditing: **Always run an intercepting proxy (Burp Suite)** with passive logging active while manually clicking through every feature of the target web application.
   - Inspect the HTTP History tab for all endpoints containing numeric IDs, hashes, or UUIDs in paths, bodies, or query strings.
2. **REST API Mutation Matrix:**
   - Test every HTTP verb supported by the endpoint (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`). Often an application enforces authorization on `GET` (viewing), but forgets to enforce authorization on `DELETE` (deleting another user's record) or `PUT` (modifying another user's record)!

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Tier 1 (Conceptual):** Read the task narrative and review the diverse architectural locations where identifiers travel across HTTP requests.
- **Tier 2 (Practical):** Familiarize yourself with using Burp Suite or browser developer tools to inspect background requests before tackling the hands-on lab in Task 7.
- **Tier 3 (Direct):** This informational checkpoint requires confirming comprehension; click the "Complete" button.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### Comprehensive IDOR Testing Matrix:

| Request Location | Vulnerable Example | Attack Tampering Vector |
| :--- | :--- | :--- |
| **Query Parameter** | `/account.php?id=105` | Change to `/account.php?id=106` |
| **REST Path Segment** | `/api/users/105/settings` | Change to `/api/users/1/settings` |
| **JSON Request Body** | `{"account_id": 105, "status": "active"}` | Change to `{"account_id": 100, ...}` |
| **HTTP Request Header**| `X-User-Context: 105` | Change to `X-User-Context: 1` |
| **Cookie Value** | `Cookie: profile_view=105` | Change to `Cookie: profile_view=1` |
| **Hidden Form Input** | `<input type="hidden" name="user_id" value="105">` | Change value via Burp or DOM Inspector |

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *Read the above.*
  - **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Comprehension confirmed on TryHackMe platform.
- Systematic testing taxonomy cross-referenced with OWASP API Security Top 10 (API1:2023 - BOLA).

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** لا تنحصر ثغرات الـ IDOR في شريط عنوان المتصفح؛ بل تختبئ في الطلبات الخلفية (`AJAX / Background Requests`)، ومسارات واجهات البرمجة (`REST Path Segments`)، وهياكل رسائل الـ `POST / JSON`، وترويسات الطلب. اختبار الـ IDOR الشامل يتطلب مراقبة كافة حزم الشبكة واختبار جميع دوال البروتوكول (`GET, POST, PUT, DELETE`).
