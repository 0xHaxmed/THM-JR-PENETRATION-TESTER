# Task 01 · Introduction (مقدمة في ثغرة تزوير الطلب من جانب الخادم)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

**Server-Side Request Forgery (SSRF)** is a critical web security vulnerability that allows an attacker to induce the server-side application to make HTTP or network requests to an arbitrary domain or IP address of the attacker's choosing.

In a typical SSRF attack, the attacker might cause the server to establish a connection to internal-only services within the organization's infrastructure. In other cases, they may force the server to connect to external systems under the attacker's control, potentially leaking sensitive data such as internal authentication credentials, API tokens, or session identifiers.

### The Two Primary Types of SSRF:
1. **Regular SSRF (In-Band / Visible SSRF):**
   - The server processes the forged request and returns the resulting HTTP response body directly back to the attacker's browser (e.g., displaying the fetched internal web page, JSON payload, or file content in the user interface).
2. **Blind SSRF (Out-of-Band / Semi-Blind SSRF):**
   - The server makes the backend request, but does not display the response body in the application interface. To detect and exploit Blind SSRF, the tester must observe secondary side-effects such as out-of-band network callbacks (using Burp Collaborator or custom HTTP/DNS listeners), significant latency differences (Time-based SSRF), or detailed server error messages (Error-based SSRF).

### Impact Spectrum of SSRF:
- **Perimeter Firewall Bypass:** Gaining access to internal subnets, microservices, and management consoles (e.g., ElasticSearch, Redis, Kubernetes API, Spring Boot Actuators) that are inaccessible from the public internet.
- **Sensitive Data & File Exposure:** Querying internal services without authentication, since internal networks frequently implement an implicit "perimeter trust" model.
- **Cloud Metadata Harvesting:** Querying the link-local metadata address (`169.254.169.254`) on cloud platforms (AWS EC2, Google Cloud Platform, Microsoft Azure) to extract IAM instance profiles, temporary STS security tokens, SSH keys, and provisioning scripts.
- **Internal Reconnaissance:** Performing automated port scanning across `127.0.0.1` and internal RFC 1918 subnets (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) by observing response times and status codes.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **The Architecture of Perimeter Trust:**
   - Why is SSRF so catastrophic in modern infrastructure? Organizations spend millions building firewalls to block external ingress traffic, but internal services often lack mutual TLS (mTLS) or robust authentication because they trust the "internal network." SSRF converts the vulnerable frontend server into an insider pivot point.
2. **OpSec in Cloud Environments:**
   - Querying AWS Instance Metadata Service version 1 (IMDSv1) via SSRF is a primary vector for total cloud account compromise (e.g., the 2019 Capital One breach). Penetration testers must know that IMDSv2 requires a `PUT` request with a custom token header (`X-aws-ec2-metadata-token-ttl-seconds`), which stops basic `GET`-based SSRF unless the attacker can forge headers or exploit an HTTP verb-tampering flaw.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** What four-word term expands from the acronym SSRF? (Starts with S_____-S___ R______ F______).
- **Hint for Question 2:** When an attack triggers execution on the backend without returning visible data directly in the response, what descriptive word is universally used across web security (e.g., Blind SQLi, Blind XSS)?

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Conceptual Anatomy of SSRF:

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                  THE SSRF PIVOT MODEL                                   |
|                                                                                         |
|  [ Public Internet ]                 [ Demilitarized Zone (DMZ) ]   [ Internal Zone ]   |
|                                                                                         |
|  +──────────────────+  1. HTTP Request  +───────────────────────+  2. Internal GET      |
|  |     Attacker     | ────────────────► | Vulnerable Web Server | ────────────────────┐ |
|  | (198.51.100.4)   |   url=http://...  |  (e.g., Apache/Nginx) |                       │ |
|  +──────────────────+                   +───────────────────────+                       │ |
|           ▲                                         │                                   │ |
|           │                                         ▼ Direct Access BLOCKED             ▼ |
|           │                                    [ Perimeter Firewall ]          +──────────────+
|           │                                                                    | Admin Portal |
|           │ 3. Returns Internal Admin Page Contents!                           | (127.0.0.1   |
|           └────────────────────────────────────────────────────────────────────|  or 10.0.0.5)|
|                                                                                +──────────────+
+─────────────────────────────────────────────────────────────────────────────────────────+
```

### 2. Analysis of the Questions:

#### Question 1:
- *Question:* What does SSRF stand for?
- *Explanation:* SSRF stands for **Server-Side Request Forgery**. The term accurately depicts the mechanism: the web server itself is tricked into forging an outbound network request on behalf of the attacker.
- *Answer:* `Server-Side Request Forgery`

#### Question 2:
- *Question:* As opposed to a regular SSRF, what is the other type?
- *Explanation:* In contrast to regular/in-band SSRF where data is reflected in the HTTP response, **Blind** SSRF occurs when the backend initiates the network call without displaying the resulting data to the user.
- *Answer:* `Blind`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What does SSRF stand for?*
  - **Answer:** `Server-Side Request Forgery`
- **Question 2:** *As opposed to a regular SSRF, what is the other type?*
  - **Answer:** `Blind`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Both answers verified and confirmed against the TryHackMe platform engine.
- Aligned with OWASP Top 10:2021 Category A10: Server-Side Request Forgery.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** تنشأ ثغرة الـ SSRF (`Server-Side Request Forgery`) عندما يفوض تطبيق الويب صلاحياته الشبكية للمستخدم ويقوم بإجراء طلبات نيابة عنه دون تحقق صارم. وتكمن خطورتها في تحويل خادم الويب إلى "وكيل داخلي" (Internal Proxy) يخترق الجدران النارية ويصل للخدمات السرية وبيانات السحابة الحساسة (`Metadata`).
