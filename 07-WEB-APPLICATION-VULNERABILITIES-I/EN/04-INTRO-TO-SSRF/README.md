# Comprehensive Technical Room Guide: Intro to SSRF

---

## Section 01: Room Overview & Metadata

| Metadata Field | Technical Specification |
| :--- | :--- |
| **Room Title** | Intro to SSRF (Server-Side Request Forgery) |
| **Learning Path** | Jr Penetration Tester (PT1 Aligned Curriculum) |
| **Module** | Web Application Vulnerabilities I (Module 07) |
| **Room Slug** | `ssrfqi` (Direct URL: `https://tryhackme.com/room/ssrfqi`) |
| **Difficulty Level** | Easy |
| **Room Type** | Walkthrough & Practical Laboratory |
| **Estimated Completion Time** | 30 Minutes |
| **Total Tasks** | 5 Interactive Tasks |
| **Target Application** | Parameter Manipulation Sandbox & Acme IT Support Customer Portal |
| **User Completion Status** | 100% Completed (Verified live on TryHackMe platform) |

---

## Section 02: Educational & Operational Objectives

1. **Understand Server-Side Request Forgery:** Master how SSRF coerces backend application servers into initiating unauthorized network connections to internal infrastructure, cloud endpoints, or external third parties.
2. **Distinguish Regular vs. Blind SSRF:** Contrast visible/in-band SSRF (where response bodies are rendered directly in front-end pages) with Blind SSRF (requiring Out-of-Band DNS/HTTP listeners, latency metrics, or error differential analysis).
3. **Master Parameter Structure Manipulation:** Manipulate full and partial URL parameters, neutralizing forced trailing path fragments using query delimiters (`&x=`, `?`, `#`).
4. **Circumvent Deny Lists and Allow Lists:** Exploit multiple IPv4 mathematical representations (`127.1`, `2130706433`, `0x7f.1`, `nip.io`) and chain Allow Lists with Open Redirect vulnerabilities.
5. **Analyze Cloud Metadata Attack Surfaces:** Understand the architecture of the link-local metadata address `169.254.169.254` across AWS, GCP, and Azure to extract sensitive IAM role credentials.
6. **Execute Path Normalization Traversal:** Bypass naive string prefix validation (`/private`) using directory traversal sequences (`x/../private`) and extract Base64 Data URI streams.

---

## Section 03: Target Architecture & Network Topology

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    NETWORK TOPOLOGY DIAGRAM                             |
|                                                                                         |
|      [ Public Internet / Untrusted ]        [ DMZ Perimeter ]     [ Internal Secure Zone ]
|                                                                                         |
|    +─────────────────────────────────+   1. HTTP Request    +────────────────────────+  |
|    |      Attacker / Penetration     | ───────────────────► |  Vulnerable Web Server |  |
|    |             Platform            |   avatar=x/../private|  (Acme IT Support)     |  |
|    +─────────────────────────────────+                      +────────────────────────+  |
|                     ▲                                                   │               |
|                     │                                                   │ 2. Internal   |
|                     │                                                   │    Localhost  |
|                     │                                                   │    Request    |
|                     │                                                   ▼               |
|                     │ 3. Fetches /private & Base64 encodes     +─────────────────────+  |
|                     │    into <img src="data:image/png;base64">|  Protected Internal  |  |
|                     │                                          |  Endpoint (/private)|  |
|                     └──────────────────────────────────────────|  (Localhost Only)   |  |
|                                                                +─────────────────────+  |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Section 04: Prerequisites, Tooling & Environment Setup

### 1. Prerequisite Knowledge:
- Standard URL grammar specifications under RFC 3986 (Scheme, Authority, Path, Query, Fragment).
- IP addressing standards under RFC 1918 (Private Networks) and RFC 3927 (Link-Local `169.254.0.0/16`).
- Familiarity with diverse network schemes (`http://`, `https://`, `file://`, `gopher://`, `dict://`).

### 2. Environment Tools & Diagnostics:
- Intercepting HTTP Proxy (Burp Suite Community / Professional).
- Browser Developer Tools (DOM element inspection and manipulation).
- Linux command-line data processing tools (`base64`, `curl`).

---

## Section 05: Theoretical Deep Dive

### 1. The Core Nature of SSRF:
Server-Side Request Forgery (SSRF) abuses the ambient network trust of an application server. Because the server resides inside a corporate network or DMZ, internal firewalls allow it to communicate with internal microservices, databases, and admin consoles. SSRF tricks the server into serving as a malicious proxy, allowing an external attacker to access these protected resources.

### 2. Visible vs. Blind SSRF:
- **Visible / Regular SSRF:** The response generated by the target resource is echoed back to the client interface (e.g., PDF generation, image reflection, direct API responses).
- **Blind SSRF:** The request is dispatched, but the application does not return the response body. Attackers must verify execution using Out-of-Band (OOB) DNS lookups, HTTP webhooks, or side-channel timing analysis.

### 3. The Cloud Metadata Goldmine:
Cloud service providers provide virtual instances with an internal HTTP service hosted at `169.254.169.254`. Under AWS IMDSv1, an attacker reaching this endpoint can harvest temporary AWS STS credentials from `http://169.254.169.254/latest/meta-data/iam/security-credentials/<ROLE>`, escalating from an unprivileged web flaw to full cloud infrastructure compromise.

---

## Section 06: Reconnaissance & Attack Surface Mapping

When mapping attack surfaces for SSRF vulnerabilities:

1. **High-Risk Functionalities:**
   - File import / avatar upload features accepting external URLs.
   - Webhook callback configuration dialogs.
   - URL unfurling / link preview generation services.
   - Document conversion (HTML-to-PDF / screenshot generation).
2. **High-Risk Parameter Patterns:**
   - `url=`, `uri=`, `target=`, `dest=`, `destination=`, `redirect=`, `feed=`, `host=`, `server=`, `srv=`, `fetch=`, `path=`.

---

## Section 07: Vulnerability Analysis & Root Cause

### 1. Parameter Concatenation (Task 02):
- **Root Cause:** Insecure string concatenation:
  ```php
  $request_url = "https://" . $_GET['server'] . "/api/item?id=" . $_GET['id'];
  ```
  The developer failed to parse the URL using standard URI parsing libraries, enabling attackers to inject complete query strings (`flag?id=9&x=`) to relegate the hardcoded path to an ignored query value.

### 2. Order-of-Operations Flaw (Task 05):
- **Root Cause:** String prefix validation performed *before* path canonicalization.
  The server checked if the raw input started with `/private`. Supplying `x/../private` bypassed the prefix validation, while the underlying web server collapsed `../` to fetch the protected `/private` handler.

---

## Section 08: Step-by-Step Exploitation & Flag Capture

### Scenario 1: URL Syntax Manipulation (Task 02)
1. Examine normal parameter request:
   `https://website.thm/item/2?server=api`
2. Target destination: `server.website.thm/flag?id=9`.
3. Craft payload with query neutralizer `&x=`:
   ```
   server=server.website.thm/flag?id=9&x=
   ```
4. The server requests `https://server.website.thm/flag?id=9&x=/api/item?id=2` and returns:
   > **Flag 1:** `THM{SSRF_MASTER}`

---

### Scenario 2: Path Traversal Bypass on Acme IT Support (Task 05)
1. Navigate to `/customers/new-account-page`.
2. Inspect the avatar radio button in Developer Tools:
   ```html
   <input type="radio" name="avatar" value="images/avatar1.png">
   ```
3. Change the value attribute to leverage path traversal:
   ```html
   <input type="radio" name="avatar" value="x/../private" checked>
   ```
4. Submit the registration form. The server fetches `/private` on localhost and renders:
   ```html
   <img src="data:image/png;base64,VEhNe1lPVV9XT1JLRURfT1VUX1RIRV9TU1JGfQ==">
   ```
5. Decode the Base64 data string in terminal:
   ```bash
   echo "VEhNe1lPVV9XT1JLRURfT1VUX1RIRV9TU1JGfQ==" | base64 -d
   ```
> **Flag 2:** `THM{YOU_WORKED_OUT_THE_SSRF}`

---

## Section 09: Defense-in-Depth & Hardening Strategies

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           DEFENSE-IN-DEPTH REMEDIATION MODEL                            |
|                                                                                         |
|  [ Layer 1: Egress Network Segmentation & Firewalls ]                                   |
|  - Block outbound traffic from application hosts to RFC 1918 private subnets.          |
|  - Drop traffic destined for 169.254.169.254 at the host firewall level.                |
|                                                                                         |
|  [ Layer 2: Strict Allow-Listing & Protocol Restriction ]                                |
|  - Restrict outbound requests strictly to pre-approved fully qualified domain names.    |
|  - Disable non-HTTP schemes (gopher://, file://, dict://, ftp://).                     |
|  - Disable automatic redirect following (CURLOPT_FOLLOWLOCATION = false).             |
|                                                                                         |
|  [ Layer 3: Cloud Metadata Hardening (IMDSv2) ]                                         |
|  - Require IMDSv2 session tokens across all cloud instances.                            |
|  - Set hop-limit = 1 to prevent containerized workloads from querying host metadata.   |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Section 10: Detection, Threat Hunting & SIEM Rules

### 1. Web Access & Proxy Log Monitoring:
```bash
# Grep access logs for internal IP representations and metadata endpoints:
grep -E "(127\.0\.0\.1|localhost|169\.254\.169\.254|0\.0\.0\.0|2130706433)" /var/log/nginx/access.log
```

### 2. Sigma Detection Rule:
```yaml
title: Potential SSRF Querying Internal or Cloud Metadata
id: 5a7e9b2c-3d4f-4a6e-b1c8-29d5e8f01b2a
status: stable
description: Detects outbound request parameters targeting private subnets or cloud metadata endpoints.
logsource:
  category: webserver
detection:
  selection:
    cs-uri-query|contains:
      - '169.254.169.254'
      - '127.0.0.1'
      - 'localhost'
      - 'metadata.google'
      - 'latest/meta-data'
  condition: selection
fields:
  - c-ip
  - cs-method
  - cs-uri-stem
  - cs-uri-query
level: critical
```

---

## Section 11: Common Pitfalls & Troubleshooting

1. **Relying on String Deny Lists:**
   - *Pitfall:* Filtering literal `"127.0.0.1"`.
   - *Reality:* Attackers use alternative numeric representations (`2130706433`), octal strings (`0177.1`), or DNS wildcards (`127.0.0.1.nip.io`).
2. **Validating Raw Input Before Canonicalization:**
   - *Pitfall:* Checking for forbidden path prefixes before normalizing dot-dot-slash (`../`) sequences.
   - *Reality:* Attackers bypass prefix checks using `x/../private`.
3. **Allowing Blind HTTP Redirects:**
   - *Pitfall:* Validating that the initial host is whitelisted, but following 302 redirects to internal IP addresses.
   - *Reality:* Always disable redirect following in backend HTTP clients.

---

## Section 12: Real-World Case Studies & CVE Mapping

1. **Capital One Cloud Breach (2019):**
   - SSRF against an open-source WAF allowed an attacker to query AWS IMDSv1, stealing temporary IAM role credentials and exfiltrating over 100 million customer records.
2. **Microsoft Exchange Server (CVE-2021-26855 - ProxyLogon):**
   - SSRF flaw in the frontend proxy allowed unauthenticated attackers to forge backend requests with arbitrary Kerberos authentication.
3. **Standard Classifications:**
   - **CWE-918:** Server-Side Request Forgery (SSRF).
   - **OWASP Top 10:2021:** A10:2021 - Server-Side Request Forgery.

---

## Section 13: Summary & Answers Matrix

| Task Number | Focus Concept | Platform Question | Final Accepted Answer |
| :--- | :--- | :--- | :--- |
| **Task 01** | Core Definition | What does SSRF stand for? | `Server-Side Request Forgery` |
| | Subtype Classification | As opposed to a regular SSRF, what is the other type? | `Blind` |
| **Task 02** | Parameter Tampering | What is the flag from the SSRF Examples site? | `THM{SSRF_MASTER}` |
| **Task 03** | Vulnerability Triaging | Which URL is more likely to be vulnerable to SSRF? | `3` |
| **Task 04** | Defense Evasion | What method can be used to bypass strict rules? | `Open Redirect` |
| | Cloud Metadata | What IP address may contain sensitive data in a cloud environment? | `169.254.169.254` |
| | Allow Lists | What type of list is used to permit only certain input? | `Allow List` |
| | Deny Lists | What type of list is used to stop certain input? | `Deny List` |
| **Task 05** | Practical Challenge | What is the flag from the /private directory? | `THM{YOU_WORKED_OUT_THE_SSRF}` |

---

## Section 14: Practical Exercises & Challenge Extensions

1. **Exercise 1: Gopher to Redis Exploitation:**
   - Set up an unauthenticated Redis instance on port 6379 in a local lab, craft a `gopher://` payload using `gopherus`, and write a test file to disk.
2. **Exercise 2: DNS Rebinding Testing:**
   - Use an online DNS rebinding service (such as `rbndr.us`) to simulate a TOCTOU race condition against an IP-filtering endpoint.
3. **Exercise 3: IMDSv2 Hardening Verification:**
   - Deploy an AWS EC2 instance, enforce `HttpTokens=required`, verify that unauthenticated `GET` requests to `169.254.169.254` return HTTP 401 Unauthorized, and automate token acquisition.
