# Advanced Engineering Notes & Deep Dive: Cross-Site Scripting (XSS)

---

## 1. Formal Taxonomy of Cross-Site Scripting

In academic literature and standard security taxonomies (e.g., OWASP, MITRE CWE-79), Cross-Site Scripting is categorized into three primary structural types:

```
                            ┌─────────────────────────────────────────┐
                            │    CROSS-SITE SCRIPTING (XSS) TAXONOMY  │
                            └────────────────────┬────────────────────┘
                                                 │
         ┌───────────────────────────────────────┼───────────────────────────────────────┐
         │                                       │                                       │
         ▼                                       ▼                                       ▼
┌──────────────────┐                   ┌──────────────────┐                   ┌──────────────────┐
│  REFLECTED XSS   │                   │    STORED XSS    │                   │   DOM-BASED XSS  │
│  (Type 1 / Non-  │                   │ (Type 2 / Pers-  │                   │ (Type 0 / Client-│
│   Persistent)    │                   │     istent)      │                   │      Side)       │
├──────────────────┤                   ├──────────────────┤                   ├──────────────────┤
│ - Server reflects│                   │ - Payload stored │                   │ - Server unin-   │
│   input in HTTP  │                   │   in DB / disk / │                   │   volved / blind │
│   response.      │                   │   logs.          │                   │ - Executed by    │
│ - Requires direct│                   │ - Fires for any  │                   │   client script  │
│   link click /   │                   │   visitor.       │                   │   reading Source │
│   interaction.   │                   │ - Zero-click     │                   │   to Sink.       │
│ - Ephemeral.     │                   │   trigger.       │                   │ - Works on #     │
└──────────────────┘                   └──────────────────┘                   └──────────────────┘
```

---

## 2. Exhaustive DOM Source and Sink Catalog

In modern Single Page Applications (SPAs built with React, Vue, Angular, or vanilla JavaScript), DOM XSS represents the dominant vulnerability pattern. To detect and remediate DOM XSS, engineers must trace the complete data pipeline from **Sources** (untrusted user inputs) to **Sinks** (dangerous rendering/execution functions).

### A. Primary Client-Side Sources:
| Source Category | DOM Property / API | Network Visibility to Server |
| :--- | :--- | :--- |
| **URL Properties** | `location.search` | ✅ Transmitted in HTTP GET request line |
| **URL Fragment** | `location.hash` | ❌ **Client-only** (Never sent across HTTP) |
| **URL String** | `location.href`, `document.URL` | ✅ Base URL sent; fragment remains local |
| **Referrer** | `document.referrer` | ✅ Sent in `Referer` header unless policy blocks |
| **Window Messaging** | `window.name`, `postMessage()` events | ❌ Internal client memory context |
| **Client Storage** | `localStorage`, `sessionStorage`, `IndexedDB` | ❌ Client storage sandbox |

### B. Dangerous Execution & Injection Sinks:
| Sink Type | Dangerous APIs | Exploit Vector / Vulnerability |
| :--- | :--- | :--- |
| **Direct Execution Sinks** | `eval(str)`, `Function(str)()`, `setTimeout(str, ms)`, `setInterval(str, ms)` | Interprets strings directly as executable JavaScript code. |
| **DOM Tree Mutation Sinks** | `element.innerHTML`, `element.outerHTML`, `document.write()`, `document.writeln()` | Parses strings as raw HTML, creating executable elements (`<script>`, `<img onerror=...>`). |
| **Navigation / Action Sinks** | `location.href = str`, `location.assign(str)`, `location.replace(str)` | Allows pseudo-protocol injection (`javascript:alert(1)`). |
| **Element Attribute Sinks** | `element.setAttribute('src', str)`, `element.setAttribute('href', str)` | Injects inline handlers or `javascript:` URIs into tags. |

---

## 3. Content Security Policy (CSP): Architecture & Bypasses

**Content Security Policy (CSP)** is an HTTP response header that restricts the resources (scripts, images, stylesheets, frames) that the browser is allowed to load and execute for a given origin.

### Standard Hardened CSP Configuration:
```http
Content-Security-Policy: default-src 'self'; script-src 'self' 'nonce-r4nd0mStr1ng' https://cdn.trusted.com; object-src 'none'; base-uri 'self';
```

### Key Directives:
- `script-src`: Restricts allowed sources of JavaScript. Omitting `'unsafe-inline'` blocks all inline `<script>` tags and event handlers (`onload`, `onerror`). Omitting `'unsafe-eval'` disables `eval()`.
- `object-src 'none'`: Blocks legacy plugins (Flash, Silverlight, Java applets) that could bypass script protections.
- `base-uri 'self'`: Prevents attackers from injecting `<base href="https://evil.thm/">`, which hijacks relative script imports.

### Common CSP Bypasses in Penetration Testing:
1. **Unsafe Directives Present:** If the policy includes `'unsafe-inline'`, attribute event handlers (`<img onerror=...>`) execute freely. If `'unsafe-eval'` is present, string-to-code sinks function normally.
2. **JSONP Endpoint Abuse:** If the CSP whitelists a popular domain containing JSONP endpoints (e.g., `https://*.google.com` or `https://*.googleapis.com`), attackers can invoke arbitrary callbacks:
   ```html
   <script src="https://accounts.google.com/o/oauth2/revoke?callback=alert(1)"></script>
   ```
3. **Open Redirect Chaining:** If a whitelisted domain contains an open redirect, attackers can redirect the browser to fetch a payload from an external malicious server.
4. **Nonce Reuse / Nonce Leakage:** If the server generates a static or predictable CSP nonce across page loads, attackers hardcode the valid nonce in their injected script (`<script nonce="r4nd0mStr1ng">`).

---

## 4. Remediation: Context-Aware Output Encoding & Sanitization

The golden rule of XSS remediation is: **Never treat user input as executable code, and always encode output according to its exact syntactic context.**

### A. Context-Aware Encoding Rules:
| Syntactic Context | Example Injection Target | Required Encoding Mechanism |
| :--- | :--- | :--- |
| **HTML Body Context** | `<div>[INPUT]</div>` | Convert special HTML characters to safe entities: <br>`&` ➔ `&amp;`<br>`<` ➔ `&lt;`<br>`>` ➔ `&gt;`<br>`"` ➔ `&quot;`<br>`'` ➔ `&#x27;` |
| **HTML Attribute Context** | `<input value="[INPUT]">` | Strict attribute encoding; escape all non-alphanumeric characters with ASCII hex (`&#xHH;`). |
| **JavaScript String Context** | `<script>var name = '[INPUT]';</script>` | Unicode escaping (`\uXXXX`) or strict JSON serialization (`json_encode()` / `JSON.stringify()`). |
| **URL Parameter Context** | `<a href="/search?q=[INPUT]">` | Standard URL component encoding (`encodeURIComponent()`). |

### B. Client-Side Sanitization Libraries:
When applications legitimately require users to submit rich HTML formatting (e.g., Markdown editors, email bodies, forums), simple encoding is insufficient. Developers must employ a battle-tested sanitization engine like **DOMPurify**:

```javascript
// Safe sanitization using DOMPurify:
var cleanHTML = DOMPurify.sanitize(userInput);
document.getElementById('display').innerHTML = cleanHTML;
```
DOMPurify strips out malicious tags (`<script>`, `<iframe>`, `<object>`) and removes all dangerous event handlers (`onload`, `onerror`) while preserving safe tags (`<b>`, `<i>`, `<p>`).

---

## 5. Modern Framework Protections (React, Angular, Vue)

Modern frontend frameworks include automatic context-aware escaping by default:

### React:
- By default, JSX escapes all values rendered inside curly braces:
  ```jsx
  // SAFE: React automatically escapes <script> into text
  return <div>{userInput}</div>;
  ```
- **Dangerous Escape Hatch:** If developers explicitly use `dangerouslySetInnerHTML`, XSS vulnerabilities reappear:
  ```jsx
  // VULNERABLE: Direct HTML injection
  return <div dangerouslySetInnerHTML={{ __html: userInput }} />;
  ```

### Angular:
- Angular automatically treats all values as untrusted and passes them through its built-in `DomSanitizer`.
- **Dangerous Escape Hatch:** Using `DomSanitizer.bypassSecurityTrustHtml(userInput)`.

---

## 6. Anatomy of an XSS Polyglot

An **XSS Polyglot** is a single payload engineered to execute across multiple syntactic contexts (HTML body, attributes, scripts, styles, textareas) simultaneously:

```javascript
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```

### Polyglot Dissection:
1. `jaVasCript:` Exploits URL/navigation sinks (`href="jaVasCript:..."`).
2. `/*-/*`/*\`/*'/*"/**/`: Closes and breaks out of single quotes, double quotes, backticks, and JavaScript multiline comments.
3. `(//onerror=alert('THM'))`: Prepares a fallback error handler.
4. `//</stYle/</titLe/</teXtarEa/</scRipt/--!>`: Closes any enclosing `<style>`, `<title>`, `<textarea>`, or `<script>` tags, while handling HTML comments.
5. `\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e`: Injects a standalone SVG element with an `onload` event that executes in modern XML/HTML parsers.
