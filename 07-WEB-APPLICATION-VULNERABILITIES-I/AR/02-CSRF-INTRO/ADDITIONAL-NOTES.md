# Advanced Engineering Notes & Deep Dive: Cross-Site Request Forgery (CSRF)

---

## 1. Architectural Anatomy: The Confused Deputy Problem

Cross-Site Request Forgery (CSRF) is fundamentally an instance of the classical computer science dilemma known as the **Confused Deputy Problem** (first formally documented by Norm Hardy in 1988):
- **The Client/User:** The legitimate principal who possesses rights.
- **The Browser:** The "deputy" serving the client, which holds ambient authority (authentication cookies, basic auth headers, TLS client certificates).
- **The Target Server:** A service that acts upon commands submitted by the deputy, trusting that any command carrying valid ambient authority represents the authentic desire of the principal.
- **The Attacker:** A third party who cannot read the principal's secrets directly, but tricks the deputy into executing commands against the target server on their behalf.

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                THE CONFUSED DEPUTY MODEL                                |
|                                                                                         |
|       [ Attacker Web Page ]                                                             |
|                 │                                                                       |
|                 │ 1. Tricked Directive ("Submit this POST form")                        |
|                 ▼                                                                       |
|         [ Web Browser ]  ◄── Holds Ambient Authority (PHPSESSID Cookie)                 |
|         (Confused Deputy)                                                               |
|                 │                                                                       |
|                 │ 2. Dispatches Request + Inadvertently Appends PHPSESSID               |
|                 ▼                                                                       |
|      [ Target Web Server ]                                                              |
|       Checks: Valid Cookie? Yes!                                                        |
|       Assumption: User must have intended this!                                         |
|       Result: Unauthorized Database State Mutation                                      |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## 2. Anti-CSRF Defense Architectures: In-Depth Breakdown

### A. The Synchronizer Token Pattern (STP) - State-Bound Protection
The gold standard recommended by OWASP for stateful web applications:
1. When the user establishes an authenticated session, the server generates a cryptographically secure, high-entropy pseudo-random token (`token_secret = crypto_random(32)`).
2. The server stores this token in the server-side session cache (e.g., Redis, memcached, or database session store).
3. Whenever an HTML form representing a state-changing operation is rendered, the token is injected as a hidden field:
   ```html
   <input type="hidden" name="csrf_token" value="4f9c8d2a1b7e6f3c5d8a9b0e2f1c4a7d">
   ```
4. For AJAX / SPA applications, the token is passed in an HTTP header (e.g., `X-CSRF-Token: <token>`).
5. Upon receiving the request, the server compares the incoming token against the token stored in the server session using constant-time string comparison (`hash_equals()` in PHP, `crypto.timingSafeEqual()` in Node.js) to avoid timing attacks.
6. If mismatched or absent, the transaction is immediately rejected with HTTP `403 Forbidden`.

### B. Double Submit Cookie Pattern - Stateless / Microservice Protection
Used when servers do not maintain server-side session state (e.g., pure JWT architectures):
1. Upon authentication, the server generates a pseudo-random value and writes it to a readable cookie (e.g., `Set-Cookie: XSRF-TOKEN=rand123; path=/`).
2. When the frontend application submits a state-changing request, the client-side JavaScript reads the cookie and inserts its value into a custom request header or hidden form input (`X-XSRF-TOKEN: rand123`).
3. The server inspects the request and compares the value in the cookie against the value in the request parameter/header.
4. **Why this works:** An external attacker's website on `evil.thm` cannot read the `XSRF-TOKEN` cookie set on `target.thm` due to the Same-Origin Policy. Therefore, the attacker cannot populate the matching header/parameter.
5. **Caveat:** If any subdomain of `target.thm` suffers from Cross-Site Scripting (XSS) or cookie tossing, the attacker can overwrite the cookie and forge the double submit. To mitigate this, developers use **Encrypted / HMAC-Signed Double Submit Cookies**.

---

## 3. The `SameSite` Cookie Attribute: Evolution and Mechanics

Specified in **RFC 6265bis**, the `SameSite` attribute controls whether cookies are sent with cross-site requests.

```
                            [ Origin: https://evil.com ]
                                         │
                   ┌─────────────────────┴─────────────────────┐
                   │                                           │
         Top-Level Navigation                        Cross-Origin Subrequest
       <a href="https://bank.com">                <img src="https://bank.com">
                   │                              <form action="https://bank.com" method="POST">
                   ▼                                           │
  ┌─────────────────────────────────┐                          ▼
  │ SameSite=Strict: Cookie BLOCKED │         ┌─────────────────────────────────┐
  │ SameSite=Lax:    Cookie SENT    │         │ SameSite=Strict: Cookie BLOCKED │
  │ SameSite=None:   Cookie SENT    │         │ SameSite=Lax:    Cookie BLOCKED │
  └─────────────────────────────────┘         │ SameSite=None:   Cookie SENT    │
                                              └─────────────────────────────────┘
```

### Detailed Attribute Behaviors:

1. **`SameSite=Strict`**:
   - The cookie will NEVER be sent in cross-site requests under any circumstance, including when following a normal link from an external website.
   - *Pros:* Complete protection against CSRF.
   - *Cons:* Impairs user experience; if a user clicks a legitimate link to your site from an external email or messaging app, they appear logged out on first arrival.

2. **`SameSite=Lax`** (Default in modern browsers like Chromium and Firefox):
   - Cookies are withheld on cross-site subrequests (such as images, iframes, and cross-site `POST` forms).
   - Cookies ARE permitted on "safe" top-level navigations using HTTP `GET` (e.g., when a user clicks a regular `<a href="...">` link leading to the site).
   - *Critical Security Implication:* If developers mistakenly process state-changing actions via HTTP `GET` (as demonstrated in Task 6 of this room), `SameSite=Lax` **FAILS** to protect the application!

3. **`SameSite=None`**:
   - Cookies are sent in all cross-site contexts.
   - Browsers require the `Secure` flag to accompany `SameSite=None` (`SameSite=None; Secure`).

---

## 4. CSRF vs XSS: The Fundamental Interplay

| Dimension | Cross-Site Request Forgery (CSRF) | Cross-Site Scripting (XSS) |
| :--- | :--- | :--- |
| **Vulnerability Class** | Client-Side Ambient Authority Abuse | Code Injection / Malicious Script Execution |
| **Execution Context** | Attacker's own domain (`evil.thm`) | Target application's domain (`target.thm`) |
| **Ability to Read Response** | ❌ Blocked by Same-Origin Policy (SOP) | ✅ Full access to DOM, response bodies, local storage |
| **Impact on CSRF Tokens** | Blocked by unpredictable tokens | **Defeats CSRF tokens completely** |
| **Primary Remediation** | CSRF Tokens + SameSite Cookies | Context-aware output encoding + Content Security Policy (CSP) |

> ⚠️ **Key Rule:** If an application contains an XSS vulnerability, **all CSRF protections are rendered null and void**. The injected JavaScript executes within the target origin, allowing the attacker to make asynchronous `fetch()` requests, read the legitimate CSRF token directly from the DOM, and submit requests with authentic tokens.

---

## 5. Enterprise Framework Defense Implementations

### A. Python / Django:
Django provides built-in CSRF protection enabled by default via `django.middleware.csrf.CsrfViewMiddleware`.
- In templates:
  ```html
  <form method="post">
      {% csrf_token %}
      <input type="text" name="data">
      <button type="submit">Submit</button>
  </form>
  ```
- For AJAX requests: Django reads the `csrftoken` cookie and attaches it as `X-CSRFToken` in request headers.

### B. Java / Spring Security:
Spring Security enables CSRF defense by default using `CsrfFilter`.
- Java Configuration:
  ```java
  @Bean
  public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
      http
          .csrf(csrf -> csrf
              .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
          );
      return http.build();
  }
  ```

### C. Node.js / Express:
Using standard modern middleware such as `csrf-csrf` or `csurf`:
```javascript
const { doubleCsrf } = require("csrf-csrf");
const { doubleCsrfProtection, generateToken } = doubleCsrf({
    getSecret: () => "super-secret-key-128bit",
    cookieName: "ps-csrf-token",
    cookieOptions: { sameSite: "lax", secure: true },
    size: 64,
    ignoredMethods: ["GET", "HEAD", "OPTIONS"]
});

app.post("/api/update-profile", doubleCsrfProtection, (req, res) => {
    // Controller logic executed only if token validates
});
```

---

## 6. Common CSRF Penetration Testing Bypass Vectors

When testing hardened web applications, penetration testers should test the following validation bypass techniques:

1. **Validation Dependent on Token Presence:**
   - Some poorly configured filters only validate the token if the parameter `csrf_token` is present in the request. If the attacker strips the parameter entirely, the server skips validation and processes the request.
2. **Validation Dependent on Request Method:**
   - Some frameworks apply CSRF filters exclusively to `POST` requests. If the backend routing accepts `GET` as a fallback or allows method override headers (`X-HTTP-Method-Override: POST` sent via `GET`), the protection is bypassed.
3. **Token Not Bound to User Session (Token Swapping):**
   - The application checks that the submitted token is valid from an internal pool of tokens, but fails to check whether the token belongs to the *current authenticated user*. An attacker can log in with their own account, obtain a valid token, and embed their token into the exploit sent to the victim.
4. **Token Tied to a Non-Session Cookie:**
   - In double-submit cookie patterns, if the application does not cryptographically bind the cookie to the user's session identifier, an attacker who can inject cookies (via HTTP header injection, CRLF injection, or subdomain takeovers) can set their own cookie and submit matching tokens.
