# Task 04 · Defeating Common SSRF Defenses (تخطي آليات الدفاع الشائعة ضد SSRF)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

As awareness of SSRF vulnerabilities has grown, developers have deployed various defensive filtering mechanisms. However, poorly implemented filters are frequently vulnerable to sophisticated evasion techniques.

### 1. Deny Lists (Blacklisting) & IP Obfuscation:
A **Deny List** is an approach that attempts to block known dangerous keywords, hostnames, or IP addresses (e.g., rejecting `localhost`, `127.0.0.1`, or `169.254.169.254`).

Because IPv4 addresses can be represented in multiple valid mathematical formats, string-based deny lists that only check for the exact string `"127.0.0.1"` can be bypassed using alternate encodings:
- **Dotted Decimal Shorthand:** `127.1` (Resolves to `127.0.0.1`)
- **Single Integer / Decimal Encoding:** `2130706433` (Calculated as $127 \times 256^3 + 0 \times 256^2 + 0 \times 256^1 + 1$)
- **Octal Encoding:** `017700000001` or `0177.0.0.1`
- **Hexadecimal Encoding:** `0x7f000001` or `0x7f.0.0.1`
- **Zero-Address Shorthand:** `0` or `0.0.0.0` (Binds to all local interfaces on Linux)
- **IPv6 Loopback:** `[::1]` or `[0:0:0:0:0:0:0:1]`
- **DNS Wildcard Services:** Using services like `nip.io` or `sslip.io` where `127.0.0.1.nip.io` resolves to `127.0.0.1` via public DNS, bypassing string filters looking for numeric IPs.

### 2. Allow Lists (Whitelisting):
An **Allow List** is a much more secure defense that permits connections strictly to explicitly approved hostnames or domains (e.g., allowing requests *only* to `api.partner.com`).

Common bypasses against flawed allow-list validation regexes:
- **Subdomain Confusion:** If the regex merely checks if the URL *contains* `partner.com`, an attacker registers `partner.com.evil.thm` or `attacker.thm?partner.com`.
- **URL Credential Formatting (The `@` Character):** In URL specification (RFC 3986), everything before `@` is treated as user credentials:
  `https://partner.com@attacker.thm/`
  The parser may validate that `partner.com` is present, while the underlying HTTP client connects to `attacker.thm`!

### 3. Open Redirect Chaining:
When an application enforces a strict allow list that validates the exact hostname, an attacker searches for an **Open Redirect** vulnerability on the whitelisted domain.

The attacker submits a request to the whitelisted domain, passing a redirect parameter pointing to an internal target or cloud metadata:
`https://website.thm/api/fetch?url=https://allowed-domain.thm/redirect?to=http://169.254.169.254/latest/meta-data/`
1. The server validates that `https://allowed-domain.thm` is on the Allow List (PASSED).
2. The server sends an HTTP GET request to `allowed-domain.thm`.
3. The whitelisted server responds with an HTTP `302 Found` redirecting to `http://169.254.169.254/latest/meta-data/`.
4. The requesting server's HTTP client automatically follows the redirect into the cloud metadata endpoint!

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **The Critical Significance of `169.254.169.254`:**
   - In IPv4 RFC 3927, `169.254.0.0/16` is designated as the Link-Local address block.
   - Cloud providers (AWS, GCP, Azure, DigitalOcean) universally host their **Instance Metadata Service (IMDS)** on `169.254.169.254`. An attacker who reaches this IP via SSRF can query:
     - AWS: `http://169.254.169.254/latest/meta-data/iam/security-credentials/<ROLE_NAME>` (Returns AWS Access Key ID, Secret Access Key, and Session Token).
     - GCP: `http://169.254.169.254/computeMetadata/v1/instance/service-accounts/default/token` (Requires `Metadata-Flavor: Google` header).
     - Azure: `http://169.254.169.254/metadata/instance?api-version=2021-02-01` (Requires `Metadata: true` header).
2. **DNS Rebinding Attacks (TOCTOU):**
   - If an application resolves the IP address first to verify it is not private, and then passes the URL to `curl` to fetch the content, an attacker can configure a custom DNS server that returns a public IP for the first check (TTL=0) and `127.0.0.1` for the second fetch. This Time-of-Check to Time-of-Use (TOCTOU) flaw bypasses IP-level validation.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Hint for Question 1:** What common web vulnerability allows an application to send a 302 redirect header to an arbitrary external URL, which can be chained to bypass strict domain allow lists? (Two words: O___ R_______).
- **Hint for Question 2:** What is the famous 169.254.X.X link-local IP address used across AWS, GCP, and Azure to host instance metadata?
- **Hint for Question 3:** What defensive list pattern permits *only* specifically approved entries and blocks everything else? (Two words: A____ L___).
- **Hint for Question 4:** What defensive list pattern attempts to blacklist or forbid specific malicious values while allowing everything else? (Two words: D___ L___).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### 1. Analysis of the Four Questions:

#### Question 1:
- *Question:* What method can be used to bypass strict rules?
- *Explanation:* An **Open Redirect** vulnerability on a trusted domain allows attackers to satisfy the application's strict allow-list check on the initial request, while the subsequent HTTP 301/302 redirect instructs the client library to fetch internal or prohibited destinations.
- *Answer:* `Open Redirect`

#### Question 2:
- *Question:* What IP address may contain sensitive data in a cloud environment?
- *Explanation:* The link-local IP **`169.254.169.254`** is the standard metadata service address for virtual machines running in AWS, Azure, GCP, and other cloud hypervisors.
- *Answer:* `169.254.169.254`

#### Question 3:
- *Question:* What type of list is used to permit only certain input?
- *Explanation:* An **Allow List** (whitelisting) defines an explicit inventory of authorized values. Anything not explicitly listed is rejected.
- *Answer:* `Allow List`

#### Question 4:
- *Question:* What type of list is used to stop certain input?
- *Explanation:* A **Deny List** (blacklisting) defines an inventory of prohibited values. Anything not on the list is accepted.
- *Answer:* `Deny List`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What method can be used to bypass strict rules?*
  - **Answer:** `Open Redirect`
- **Question 2:** *What IP address may contain sensitive data in a cloud environment?*
  - **Answer:** `169.254.169.254`
- **Question 3:** *What type of list is used to permit only certain input?*
  - **Answer:** `Allow List`
- **Question 4:** *What type of list is used to stop certain input?*
  - **Answer:** `Deny List`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- All four answers verified against TryHackMe platform validation engine.
- Technical mechanisms cross-referenced with PortSwigger Web Security Academy SSRF evasion techniques.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** القوائم السوداء (`Deny List`) تفشل حتماً أمام الـ SSRF لتعدد الصيغ الرياضية للآي بي (`127.0.0.1` vs `2130706433` vs `0x7f.1`)؛ وحتى القوائم البيضاء الصارمة (`Allow List`) يمكن الالتفاف عليها عند دمجها مع ثغرات التوجيه المفتوح (`Open Redirect`) للوصول لخدمات السحابة الحساسة (`169.254.169.254`).
