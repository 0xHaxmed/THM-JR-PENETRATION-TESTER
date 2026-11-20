# Task 03 · Finding an SSRF (اكتشاف وتحديد نقاط ثغرات SSRF في التطبيقات)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

Identifying potential Server-Side Request Forgery vulnerabilities requires recognizing application features that take user-supplied input and use it to initiate outbound network or HTTP connections.

### High-Risk Functionality Patterns:
1. **File Download & Remote Import Features:** Endpoints that allow users to import profile avatars, documents, or data from remote URLs (e.g., `fetch-file.php?url=http://...`).
2. **Webhooks & Notification Integrations:** Features allowing users to specify custom callback endpoints to receive automated event notifications (e.g. GitHub/Stripe webhooks).
3. **URL Link Previews & Metadata Unfurling:** Social media and messaging platforms that scrape target web pages to display rich preview cards, title tags, and thumbnails (OpenGraph scraping).
4. **PDF, Image, and Document Generators:** Server-side utilities (e.g. `wkhtmltopdf`, headless Chromium) that render user-supplied HTML/CSS/URLs into downloadable PDF documents or screenshots.
5. **API Gateway & Microservice Proxies:** Features designed to query external data feeds, currency exchange rates, or weather updates.

### Techniques for Detecting Blind SSRF:
- **Out-of-Band (OOB) DNS/HTTP Beacons:** Supplying a domain under the tester's control (via Burp Collaborator, Interactsh, or RequestBin). If the server makes a DNS lookup or HTTP GET request to that domain, SSRF is confirmed.
- **Latency / Response Timing Analysis:** Comparing server response times when requesting an internal IP with an open port (fast TCP connect or immediate reset) versus an internal IP with a filtered port (slow TCP SYN timeout).
- **Differential Error Inspection:** Analyzing variations in error messages returned by the backend HTTP client library (e.g., `Connection refused` vs `404 Not Found` vs `Connection timed out`).

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Parameter Naming Heuristics:**
   - Security auditors should look for parameters named: `url`, `uri`, `target`, `dest`, `destination`, `redirect`, `feed`, `host`, `hostname`, `server`, `srv`, `port`, `load`, `fetch`, `proxy`, `document`, `file`, `path`.
2. **Protocol Smuggling (Beyond HTTP):**
   - In advanced assessments, SSRF is not restricted to HTTP. Depending on the underlying client library (e.g., `curl` in PHP), testers can test alternate URI schemes:
     - `file:///etc/passwd` (Local File Read)
     - `gopher://127.0.0.1:6379/_...` (Gopher protocol for arbitrary TCP byte stream injection, e.g., commanding unauthenticated Redis servers to achieve RCE)
     - `dict://127.0.0.1:11211/stat` (Dict protocol for Memcached probing)
     - `sftp://`, `ldap://`, `tftp://`.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** Compare the four URL candidates. Which URL explicitly contains parameters specifying an external file name, an external storage server (`srv=filestorage.cloud.thm`), and a target network port (`port=8001`)? That indicates the server is fetching network resources directly based on user parameters!

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Comparative Analysis of the Four URLs:

- **Option 1:** `https://website.thm/index.php`
  - *Analysis:* Static or root homepage request. No parameters present. Low likelihood of SSRF.
- **Option 2:** `https://website.thm/list-products.php?categoryId=5325`
  - *Analysis:* Standard database lookup parameter (`categoryId`). Primary candidate for SQL Injection or IDOR, not SSRF.
- **Option 3:** `https://website.thm/fetch-file.php?fname=242533.pdf&srv=filestorage.cloud.thm&port=8001`
  - *Analysis:* The script name `fetch-file.php` coupled with parameters `fname`, `srv`, and `port` indicates that the backend is programmatically establishing a network socket or HTTP connection to `http://filestorage.cloud.thm:8001/` to retrieve the PDF. By tampering with `srv` and `port` (e.g., `srv=127.0.0.1&port=22`), a tester can probe internal network services! This is a textbook SSRF candidate.
- **Option 4:** `https://website.thm/buy-item.php?itemId=213&price=100&q=2`
  - *Analysis:* E-commerce checkout parameters. Candidate for Business Logic Flaws or Parameter Tampering.

Therefore, URL 3 is by far the most likely candidate.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *Based on simple observation, which of the following URLs is more likely to be vulnerable to SSRF?*
  - **Answer:** `3`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Answer `3` validated by the TryHackMe grading system.
- Corresponds with standard penetration testing reconnaissance methodologies for URL parameter triage.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** عند تتبع مسارات الـ SSRF، ابحث دائماً عن المعاملات التي تطلب من الخادم جلب موارد خارجية (`srv`, `url`, `host`, `fetch`, `port`)؛ فالخوادم التي تجمع عناوين الاتصال من مدخلات المستخدم هي أكثر الأهداف عرضة للتحول إلى جسر استطلاع داخلي.
