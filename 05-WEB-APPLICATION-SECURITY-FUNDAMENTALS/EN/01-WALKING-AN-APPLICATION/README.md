# Comprehensive Documentation: Walking An Application

---

### 1. Metadata & Room Information
* **Room Title:** Walking An Application
* **Learning Path:** TryHackMe — Jr Penetration Tester (PT1 Aligned Curriculum)
* **Module:** Module 05: Web Application Security Fundamentals — Room 01
* **Room Slug:** `walkinganapp`
* **Difficulty:** Easy / Foundations
* **Core Focus:** Client-side manual security auditing via Browser Developer Tools: source code inspection, comment analysis, directory listing exploitation, client-side access control bypasses in the DOM, breakpoint debugging in JavaScript, asynchronous network request auditing (AJAX), and cookie security attribute evaluation (`HttpOnly`).
* **TryHackMe URL:** [https://tryhackme.com/room/walkinganapp](https://tryhackme.com/room/walkinganapp)
* **Documentation & Parity Status:** 100% Complete & Synchronized with user's live account.

---

### 2. Architecture & Overview
Before launching automated scanners, professional web penetration testing requires methodical client-side exploration—a process known as **Walking An Application**. 

This laboratory demonstrates that modern web browsers are comprehensive auditing environments capable of inspecting live DOM trees, setting execution breakpoints in dynamic JavaScript, intercepting background AJAX payloads, and manipulating local session state.

---

### 3. Executive Summary
Across 8 structured tasks, this lab covers client-side web application assessment:
1. Mapping endpoints manually, identifying the dedicated ticket creation route: **`/customers/ticket/new`**.
2. Inspecting the raw HTML page source to harvest four distinct vulnerability flags:
   * HTML comment leakage: **`THM{HTML_COMMENTS_ARE_DANGEROUS}`**.
   * Hidden navigation link: **`THM{NOT_A_SECRET_ANYMORE}`**.
   * Server directory listing misconfiguration (`/assets/`): **`THM{INVALID_DIRECTORY_PERMISSIONS}`**.
   * Framework disclosure: **`THM{KEEP_YOUR_SOFTWARE_UPDATED}`**.
3. Bypassing client-side overlay paywalls via the DOM Inspector, nullifying CSS blockers to extract: **`THM{NOT_SO_HIDDEN}`**.
4. Dissecting minified JavaScript (`flash.min.js`) using pretty-printing and setting a runtime breakpoint to freeze a transient red alert box, capturing: **`THM{CATCH_ME_IF_YOU_CAN}`**.
5. Intercepting background asynchronous traffic in the Network tab, extracting an unrendered API response flag: **`THM{GOT_AJAX_FLAG}`**.
6. Auditing cookie security flags in the Storage tab, discovering that the session token omits the vital **`HttpOnly`** flag (`false`).

---

### 4. Technical Theory & Foundations

#### Client-Side Interception and Bypass Sequence
```mermaid
sequenceDiagram
    autonumber
    participant Attacker as Auditor DevTools
    participant DOM as Active Browser DOM / Memory
    participant Server as Target Web Server

    Note over Attacker,Server: 1. Directory Listing Enumeration
    Attacker->>Server: GET /assets/
    Server-->>Attacker: 200 OK (Directory Index Listing)
    Note right of Attacker: Flags: THM{INVALID_DIRECTORY_PERMISSIONS}

    Note over Attacker,Server: 2. Client-Side Paywall Annihilation
    Attacker->>DOM: Delete Node (.premium-customer-blocker)
    Note over DOM: Overlay removed; text unmasked locally!
    Note right of Attacker: Flag: THM{NOT_SO_HIDDEN}

    Note over Attacker,Server: 3. Runtime Breakpoint Freezing
    Attacker->>DOM: Set Breakpoint at flash['remove']()
    DOM->>DOM: Execution Paused in Debugger!
    Note right of Attacker: Flag: THM{CATCH_ME_IF_YOU_CAN}

    Note over Attacker,Server: 4. Asynchronous Request Inspection
    Attacker->>Server: POST /contact-msg (AJAX payload)
    Server-->>Attacker: 200 OK {"flag": "THM{GOT_AJAX_FLAG}"}
    Note right of Attacker: Flag extracted from Response body!
```

---

### 5. Audit Methodology & Mindset
* **The Manual Client-Side Exploration Workflow:**
  1. **Phase 1: Static Source Review (`Ctrl+U`):** Review HTML comments, hardcoded tokens, and framework references.
  2. **Phase 2: Live DOM Manipulation (Inspector):** Delete client-side overlays, toggle disabled input fields, and reveal hidden form parameters.
  3. **Phase 3: Dynamic Script Debugging (Debugger):** Pretty-print minified files, inspect event listeners, and set breakpoints on sanitization functions.
  4. **Phase 4: Network & Storage Auditing:** Monitor XHR/Fetch calls for sensitive API leakages and inspect cookie attributes (`HttpOnly`, `Secure`, `SameSite`).

---

### 6. Step-by-Step Practical Walkthrough

#### Tasks 1 & 2: Functional Mapping & Endpoint Discovery
* Navigating the customer portal revealed the ticket generation endpoint: **`/customers/ticket/new`**.

#### Task 3: Page Source Dissection
* **Comment Leak:** Reading source comments revealed: `THM{HTML_COMMENTS_ARE_DANGEROUS}`.
* **Secret Link:** Following unlinked `<a>` tags exposed: `THM{NOT_A_SECRET_ANYMORE}`.
* **Directory Indexing:** Querying `http://MACHINE_IP/assets/` revealed an unindexed folder containing: `THM{INVALID_DIRECTORY_PERMISSIONS}`.
* **Framework Changelog:** Inspecting footer framework references yielded: `THM{KEEP_YOUR_SOFTWARE_UPDATED}`.

#### Task 4: DOM Inspector Manipulation
* Premium articles presented a `.premium-customer-blocker` overlay. Deleting the element node in DevTools revealed the underlying text: **`THM{NOT_SO_HIDDEN}`**.

#### Task 5: Debugger Runtime Breakpoints
* Minified `assets/flash.min.js` briefly flashed an alert before calling `flash['remove']();`. Setting a line breakpoint paused execution while the element remained visible: **`THM{CATCH_ME_IF_YOU_CAN}`**.

#### Task 6: Network Asynchronous Telemetry
* Submitting the contact form dispatched a POST request to `/contact-msg`. Inspecting the response payload revealed: **`THM{GOT_AJAX_FLAG}`**.

#### Task 7: Client-Side Storage Inspection
* Authenticating to the portal and reviewing the cookie table in the Storage tab confirmed the **`HttpOnly`** security attribute was set to **`false`**.

#### Task 8: Lab Completion
* Verified all client-side audit vectors.

---

### 7. Command Analysis & Tool Dissection

```bash
# Terminal Simulation of Client-Side Inspection
curl -s http://MACHINE_IP | grep -E "<!--|href="
curl -s -I http://MACHINE_IP/assets/
curl -X POST http://MACHINE_IP/contact-msg -d "name=auditor&msg=test"
```
* `curl -s ... | grep "<!--"`: Rapidly extracts developer comments.
* `curl -s -I .../assets/`: Validates HTTP status codes for directory indexing.
* `curl -X POST ...`: Interacts directly with AJAX endpoints from the command line.

---

### 8. Defensive & Blue Team Countermeasures
1. **Server-Side Authorization Gates:** Never rely on CSS (`display: none`) or JavaScript to restrict sensitive content; filter unauthorized data at the server boundary.
2. **Build-Pipeline Sanitization:** Automatically strip HTML comments, source maps, and debug statements during production compilation.
3. **Directory Indexing Hardening:** Enforce `Options -Indexes` in Apache or `autoindex off;` in Nginx.
4. **Mandatory Cookie Attributes:** Enforce `HttpOnly; Secure; SameSite=Lax` across all session tokens to mitigate session hijacking via XSS.

---

### 9. Common Pitfalls & Edge Cases
* **Assuming Hidden Equals Secure:** Obfuscating elements via CSS provides zero protection against motivated users.
* **Overlooking Minified Source Maps:** Leaving `.map` files on production servers exposes proprietary TypeScript and backend architecture.
* **Omitting HttpOnly Flags:** Allows trivial session hijacking via `document.cookie` if a single stored or reflected XSS flaw exists.

---

### 10. Key Educational Takeaways
1. The web browser is an untrusted client environment fully controllable by the user.
2. Server responses frequently contain sensitive JSON fields that are never rendered in the user interface.
3. Client-side access controls are inherently bypassable; authorization must reside on the backend.

---

### 11. Verification & Proof of Work
* 100% verification parity achieved across all 8 tasks against the user's live completed TryHackMe account.
* All flags and the `HttpOnly=false` finding verified directly against the application telemetry.

---

### 12. Tasks Index & Matrix
* For detailed 7-stage task analyses:
  * [Task 01: Walking An Application (TASK-01.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS/TASK-01.md)
  * [Task 02: Exploring The Website (TASK-02.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS/TASK-02.md)
  * [Task 03: Viewing The Page Source (TASK-03.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS/TASK-03.md)
  * [Task 04: Developer Tools - Inspector (TASK-04.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS/TASK-04.md)
  * [Task 05: Developer Tools - Debugger (TASK-05.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS/TASK-05.md)
  * [Task 06: Developer Tools - Network (TASK-06.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS/TASK-06.md)
  * [Task 07: Developer Tools - Storage (TASK-07.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS/TASK-07.md)
  * [Task 08: Conclusion (TASK-08.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS/TASK-08.md)
* See also [Tasks Index & Answers Summary (TASKS.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/TASKS.md).

---

### 13. Additional Technical Notes
* For in-depth browser rendering pipelines, source map exploitation, and RFC 6265bis specifications:
  * [Additional Notes & Deep Dive (ADDITIONAL-NOTES.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/ADDITIONAL-NOTES.md)

---

### 14. References & Standard RFCs
* OWASP standards, RFC 6265bis, and DevTools reference cheatsheet:
  * [Official References & Cheatsheet (REFERENCES.md)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/REFERENCES.md)
