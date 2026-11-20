# Module 07: Topic Transition Recap - Questions & Comprehensive Analysis

---

## 📌 Overview & Scoring Metadata

- **Module:** Web Application Vulnerabilities I (Module 07 on Jr Penetration Tester Path)
- **Recap ID:** `6afe01d5f306fc3f65927f8f`
- **Assessment Scope:** Synthesis of SQL Injection, CSRF, XSS, SSRF, IDOR, and the Recruit Challenge
- **Total Questions:** 6 Assessment Questions (5 Single-Choice + 1 Four-Way Match-Pair)
- **Score Achieved:** **48 / 48 Points (100% Perfect Score)**
- **User Verification Status:** Verified live on TryHackMe Level 64 account.

---

## 📋 Comprehensive Question-by-Question Technical Audit

### Question 1: Form Action Resolution in Cross-Site Request Forgery (CSRF)
- **Difficulty Rating:** 2
- **Question Text:**
  > *"Why must the form action on an attacker-hosted CSRF page use an absolute URL to the target?"*
- **Options Evaluated:**
  - `[ ]` Absolute URLs bypass the target's CSRF token check
    - *Technical Refutation:* The syntactic structure of the URL attribute has zero influence on backend server-side token validation logic.
  - `[x]` **A relative path would resolve to the attacker's own server**
    - *Verified Technical Rationale:* Under W3C HTML specifications, relative URI references (e.g., `action="/update_email.php"`) are resolved against the Base URI of the current document (the attacker's origin `http://attacker.thm/`). If a relative path were used, the victim's browser would send the forged POST request back to the attacker's server, never reaching the target application. Therefore, an absolute URL (`http://staffhub.thm:8080/update_email.php`) is mandatory to route the forged request to the target origin.
  - `[ ]` Absolute URLs stop the browser from sending cookies
    - *Technical Refutation:* The exact opposite is required: the browser attaches target cookies specifically because the request destination matches the target origin.
  - `[ ]` Relative paths are blocked by all modern browsers
    - *Technical Refutation:* Relative paths are fully standard in HTML; they simply resolve against the hosting page's origin.
  - `[ ]` Only absolute URLs can be used with POST
    - *Technical Refutation:* HTTP methods and URI forms are completely decoupled; POST functions identically with relative and absolute paths.
- **Verified Correct Answer:** `A relative path would resolve to the attacker's own server`
- **Platform Explanation:**
  > *"Relative paths resolve against the hosting page's origin, so the request would never reach the target application."*

---

### Question 2: Primary Attack Target for CSRF
- **Difficulty Rating:** 0
- **Question Text:**
  > *"Which kind of application request is the primary target for CSRF?"*
- **Options Evaluated:**
  - `[ ]` Requests for static images and stylesheets
    - *Technical Refutation:* Static assets do not modify server state.
  - `[ ]` DNS lookups made by the browser
    - *Technical Refutation:* DNS resolution is an infrastructure lookup, not an application-level session action.
  - `[x]` **State-changing requests such as updating account settings**
    - *Verified Technical Rationale:* CSRF is fundamentally a "blind execution" attack. Because the browser's Same-Origin Policy (SOP) prevents the attacker's page from reading the response of cross-origin requests, forging a read-only request yields no benefit. The attacker's objective is to induce state-changing actions (modifying emails, resetting passwords, transferring funds, changing roles) where the execution itself achieves the attack objective.
  - `[ ]` Requests that fail authentication
    - *Technical Refutation:* Unauthenticated requests are rejected by the target server.
  - `[ ]` Requests that only read and display public content
    - *Technical Refutation:* Read-only actions cause no state mutation, and response data is blocked by SOP.
- **Verified Correct Answer:** `State-changing requests such as updating account settings`
- **Platform Explanation:**
  > *"Forging a request is only useful if it modifies data or performs an action on the victim's behalf."*

---

### Question 3: IDOR Classification in OWASP API Security
- **Difficulty Rating:** 1
- **Question Text:**
  > *"What name does the OWASP API Security Top 10 give to the same flaw known as IDOR?"*
- **Options Evaluated:**
  - `[ ]` Server Side Parameter Pollution
    - *Technical Refutation:* SSPP involves injecting unexpected query parameters into backend service calls.
  - `[x]` **Broken Object Level Authorisation**
    - *Verified Technical Rationale:* In the OWASP API Security Top 10, IDOR is cataloged under **API1:2023 - Broken Object Level Authorization (BOLA)**. The API taxonomy emphasizes that the root failure is an absence of object-level permission verification for the requesting identity.
  - `[ ]` Broken User Authentication
    - *Technical Refutation:* Relates to credential verification (API2), whereas IDOR involves authenticated users accessing unauthorized objects.
  - `[ ]` Unrestricted Resource Consumption
    - *Technical Refutation:* Concerns rate limiting and DoS (API4).
  - `[ ]` Broken Function Level Authorisation
    - *Technical Refutation:* Concerns administrative function access (BFLA / API5), rather than accessing objects of peers at the same role tier.
- **Verified Correct Answer:** `Broken Object Level Authorisation`
- **Platform Explanation:**
  > *"The API-focused term describes the same root cause: no per-object permission check for the requesting identity."*

---

### Question 4: SQL Injection Comment Syntax Mechanics
- **Difficulty Rating:** 0
- **Question Text:**
  > *"In a MySQL injection payload, what does appending -- after the injected condition achieve?"*
- **Options Evaluated:**
  - `[ ]` It instructs the database to log the query
    - *Technical Refutation:* Comments have no effect on logging subsystems.
  - `[ ]` It escapes the surrounding single quotes
    - *Technical Refutation:* Escaping uses backslashes (`\`); comments discard text.
  - `[ ]` It concatenates a second SELECT onto the query
    - *Technical Refutation:* Query chaining/unions are performed by `UNION SELECT`.
  - `[x]` **It comments out the remainder of the original query**
    - *Verified Technical Rationale:* In SQL syntax (including MySQL, PostgreSQL, and SQLite), `--` (or `-- ` with trailing whitespace) designates an inline single-line comment. Appending `--` causes the database query parser to discard all subsequent SQL statements hardcoded by the developer (such as trailing `' AND password = '...'`), ensuring the injected payload does not cause a syntax error.
  - `[ ]` It forces the query to return every row
    - *Technical Refutation:* Always-true tautologies (`OR 1=1`) return rows; comments simply truncate parsing.
- **Verified Correct Answer:** `It comments out the remainder of the original query`
- **Platform Explanation:**
  > *"Leftover syntax such as an AND password check is ignored, so the payload does not break the statement."*

---

### Question 5: Server-Side Request Forgery Architecture
- **Difficulty Rating:** 0
- **Question Text:**
  > *"In a Server-Side Request Forgery attack, which system actually sends the request to the attacker's chosen destination?"*
- **Options Evaluated:**
  - `[ ]` The victim's browser
    - *Technical Refutation:* Client-initiated requests define CSRF or XSS. SSRF is distinguished by the backend server dispatching the request.
  - `[ ]` The attacker's own machine directly
    - *Technical Refutation:* If the attacker could reach the internal target directly, no SSRF would be needed.
  - `[ ]` An intermediate CDN edge node
    - *Technical Refutation:* CDNs cache responses, but the SSRF vulnerability resides in application logic.
  - `[x]` **The vulnerable application server**
    - *Verified Technical Rationale:* By definition, in an SSRF attack, the attacker manipulates input parameters to trick the **vulnerable application server** into issuing outbound TCP/HTTP requests on the attacker's behalf, exploiting the server's trusted network position.
  - `[ ]` The DNS resolver of the target network
    - *Technical Refutation:* Resolvers translate domain names; they do not construct or transmit HTTP application payloads.
- **Verified Correct Answer:** `The vulnerable application server`
- **Platform Explanation:**
  > *"The attacker manipulates a parameter so the back-end application issues the HTTP request on their behalf."*

---

### Question 6: Four-Way Match-Pair Analysis of XSS Taxonomy
- **Difficulty Rating:** 1
- **Question Text:**
  > *"Match each XSS variant to its defining characteristic."*

#### 1. Stored XSS
- **Correct Match:** `Saved by the application and served to later visitors`
- **Technical Analysis:** In Stored XSS, the payload is persisted on disk, database, or logging subsystems. Every legitimate user who visits the resource receives the payload automatically without requiring phishing links (Zero-Click Execution).

#### 2. DOM-based XSS
- **Correct Match:** `Executed purely client side via an unsafe sink`
- **Technical Analysis:** In DOM XSS, the vulnerability exists entirely in client-side JavaScript. Untrusted data flows from a client-side source (`location.hash`) directly into a dangerous execution sink (`eval()`, `innerHTML`) without ever interacting with the backend server.

#### 3. Reflected XSS
- **Correct Match:** `Echoed back immediately in the same response`
- **Technical Analysis:** In Reflected XSS, the payload is delivered in an HTTP request parameter and echoed immediately into the response body. Exploitation requires social engineering (inducing the victim to click a specially crafted URL).

#### 4. Blind XSS
- **Correct Match:** `Fires later in another user's session, unseen by attacker`
- **Technical Analysis:** A specialized form of Stored XSS where the payload executes within an internal administrative dashboard or moderation queue out of the attacker's direct sight. Confirmation requires an Out-of-Band (OOB) network callback.

---

## 🎯 Verification Summary

All 6 assessment questions verified against TryHackMe scoring engine with 48/48 points awarded (100% Complete).
