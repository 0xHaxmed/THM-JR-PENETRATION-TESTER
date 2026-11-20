# Advanced Engineering Notes & Deep Dive: Recruit Challenge & Chained Web Exploitation

---

## 1. Threat Modeling of Applicant Tracking Systems (ATS)

Applicant Tracking Systems (ATS) and job recruitment portals present a uniquely hazardous attack surface in enterprise environments because of their fundamental operational requirements:

```
+─────────────────────────────────────────────────────────────────────────────────────────+
|                                    ATS THREAT MODEL MATRIX                              |
|                                                                                         |
|  [ Inherent Business Need ]           [ Latent Vulnerability Risk ]                     |
|                                                                                         |
|  1. Public file submission            ➔ Unrestricted File Upload / Web Shell Execution  |
|     (Resumes, CVs, Portfolios)          (CWE-434 / RCE)                                 |
|                                                                                         |
|  2. Rich-text candidate notes         ➔ Stored XSS in HR Reviewer Dashboard             |
|     and cover letters                   (CWE-79 / Session Hijacking)                    |
|                                                                                         |
|  3. Link previews for portfolios      ➔ Server-Side Request Forgery                     |
|     (GitHub / Personal Websites)        (CWE-918 / Cloud Metadata Theft)                |
|                                                                                         |
|  4. Candidate profile management      ➔ Insecure Direct Object References               |
|     and self-service applications       (CWE-639 / PII & Candidate Data Leakage)        |
+─────────────────────────────────────────────────────────────────────────────────────────+
```

---

## 2. Anatomy of the Chained Exploitation Pipeline

In professional red teaming, single vulnerabilities are rarely sufficient to achieve an objective. Attackers construct **exploit chains** where the output of one vulnerability fulfills the preconditions for the next:

```
   [ Phase 1: Reconnaissance & Mapping ]
   Discovers endpoints: /apply.php, /login.php, /reset.php, /admin/
                    │
                    ▼
   [ Phase 2: Horizontal Broken Access Control (IDOR) ]
   Abuses missing authorization in /confirm_reset.php or /view_application.php
   Achieves: Candidate Account Authentication ➔ [ CAPTURES FLAG 1 ]
                    │
                    ▼
   [ Phase 3: Authenticated Attack Surface Expansion ]
   Unlocks authenticated candidate dashboard & resume upload functionality
                    │
                    ▼
   [ Phase 4: File Upload Extension Bypass & Execution ]
   Uploads .phtml / .php5 web shell past client-side blacklist filters
   Extracts internal database configuration / admin credentials
                    │
                    ▼
   [ Phase 5: Administrative Privilege Escalation ]
   Authenticates to /admin/dashboard.php as Administrator ➔ [ CAPTURES FLAG 2 ]
```

---

## 3. Engineering Secure File Upload Architectures

Allowing users to upload files is inherently risky. A production-grade secure file upload pipeline requires defense-in-depth:

```
                                [ Incoming File Upload ]
                                           │
                                           ▼
                       [ 1. Whitelist File Extension Check ]
                     (Allow ONLY: .pdf, .docx, .odt - Case Insensitive)
                                           │
                                           ▼
                         [ 2. Magic Number & MIME Inspection ]
                    (Verify file header bytes match expected format)
                                           │
                                           ▼
                         [ 3. Filename & Path Sanitization ]
                   (Discard original filename; generate random UUIDv4)
                                           │
                                           ▼
                        [ 4. Non-Executable Storage Root ]
               (Store strictly OUTSIDE of /var/www/html/ or public root)
                                           │
                                           ▼
                        [ 5. Isolated CDN / Delivery Host ]
                   (Serve files with Content-Disposition: attachment
                    and Content-Type: application/octet-stream from
                    a separate origin like files-cdn.company.com)
```

### Web Server Hardening for Upload Directories:
- **Nginx Configuration:**
  ```nginx
  location ^~ /uploads/ {
      # Disable script interpreters completely
      location ~ \.(php|phar|phtml|php5)$ {
          deny all;
      }
      add_header Content-Disposition "attachment";
  }
  ```
- **Apache (`.htaccess` in uploads folder):**
  ```apache
  # Prevent PHP script execution:
  <FilesMatch "(?i)\.(php|php5|phtml|phar|pl|py|cgi)$">
      Require all denied
  </FilesMatch>
  php_flag engine off
  ```

---

## 4. Hardening Password Reset & Account Recovery Flows

The initial foothold in this challenge was enabled by insecure password reset handling. To prevent account takeovers via reset flaws:

1. **Cryptographically Secure Pseudo-Random Tokens (CSPRNG):**
   - Generate at least 256 bits of entropy: `bin2hex(random_bytes(32))`.
2. **Strict Session & Hash Association:**
   - Store the token hash in the database tied directly to the user record:
     ```sql
     UPDATE users SET reset_token = :token_hash, reset_expires = :expiry WHERE id = :user_id;
     ```
3. **No Direct Parameter Overrides:**
   - The password reset confirmation handler must **never** accept an explicit `user_id` or `email` parameter from the client. The server must look up the user solely by matching the incoming secret token:
     ```sql
     SELECT id FROM users WHERE reset_token = :token_hash AND reset_expires > NOW();
     ```
4. **Immediate Invalidation (Single-Use Token):**
   - Invalidate the reset token immediately upon first use or after a 15-minute expiration window.
   - Automatically revoke all active sessions for that account upon password change.
