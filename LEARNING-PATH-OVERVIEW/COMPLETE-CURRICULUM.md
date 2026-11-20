# Complete Curriculum: TryHackMe Jr Penetration Tester
## المنهج التفصيلي الكامل ومصفوفة الغرف والوحدات

---

## 📌 Executive Summary | الملخص التنفيذي
This curriculum represents the verified architecture of the **TryHackMe Jr Penetration Tester** path. It covers foundational penetration testing ethics, network enumeration, web application assessments, exploit research, post-exploitation privilege escalation, and enterprise infrastructure security.

---

## 📚 Curriculum Breakdown by Module

### Module 01: Introduction to Pentesting (Foundations)
* **Description:** Establishes the professional, ethical, and legal foundation of penetration testing engagements. Introduces standardized offensive methodologies, risk frameworks, and initial guided attack scenarios.
* **Estimated Duration:** ~8 Hours
* **Prerequisites:** Basic familiarity with Linux terminal and fundamental networking (TCP/IP).
* **Rooms Included:**
  1. **Pentesting Fundamentals:** Black-box, white-box, and grey-box testing; Rules of Engagement (RoE); scoping; stages of a penetration test.
  2. **Principles of Security:** The CIA Triad, Defence-in-Depth, Principle of Least Privilege, security architecture models.
  3. **Dive Into Pentesting:** Practical mindset, risk formula ($Risk = Threat \times Vulnerability$), professional documentation, and note-taking.
  4. **Cyber Kill Chain:** Detailed walkthrough of the 7 stages (Reconnaissance, Weaponization, Delivery, Exploitation, Installation, C2, Actions on Objectives).
  5. **Penetration Testing Frameworks:** Industry standards (PTES, NIST SP 800-115, OSSTMM, OWASP).
  6. **Guided Pentest: Web:** Guided hands-on web assessment walkthrough (RecruitX scenario).
  7. **Guided Pentest: Infrastructure:** Guided hands-on infrastructure assessment (UnrealIRCd exploitation to root).

---

### Module 02: Network Reconnaissance & Port Scanning (Network Security)
* **Description:** In-depth technical mastery of discovering live hosts, identifying listening ports, probing services, and interacting with common enterprise network protocols.
* **Estimated Duration:** ~14 Hours
* **Key Tools:** `nmap`, `whois`, `nslookup`, `dig`, `tcpdump`, `wireshark`, `netcat`.
* **Rooms Included:**
  1. **Passive Reconnaissance:** WHOIS lookups, DNS enumeration, BGP lookups, OSINT, search engine dorking.
  2. **Active Reconnaissance:** Web crawling, ping sweeps, traceroute, banner grabbing.
  3. **Nmap Live Host Discovery:** ARP scanning, ICMP echo/timestamp requests, TCP SYN/ACK pinging, UDP host discovery (`-sn`, `-PR`, `-PE`, `-PS`, `-PA`, `-PU`).
  4. **Nmap Basic Port Scans:** TCP Connect scans (`-sT`), TCP SYN (Stealth) scans (`-sS`), UDP scans (`-sU`), port selection flags (`-p`, `-F`, `--top-ports`).
  5. **Nmap Advanced Port Scans:** TCP Null (`-sN`), FIN (`-sF`), Xmas (`-sX`), Maimon scans (`-sM`), TCP ACK scans (`-sA`), Window scans (`-sW`), spoofing & decoys (`-D`, `-S`, `-e`, `-g`).
  6. **Nmap Post Port Scans:** Service version detection (`-sV`), OS fingerprinting (`-O`), Nmap Scripting Engine (`-sC`, `--script`), timing templates (`-T0` to `-T5`), output formatting (`-oN`, `-oG`, `-oX`).
  7. **Protocols and Servers:** Deep dive into HTTP/HTTPS, FTP, SMB, POP3/IMAP, SMTP.
  8. **Protocols and Servers 2:** Deep dive into SSH, RDP, VNC, Telnet, SNMP.
  9. **Net Sec Challenge:** Comprehensive hands-on network scanning and evasion capstone lab.

---

### Module 03: Web Application Security Fundamentals & Reconnaissance
* **Description:** Understanding modern web applications from the browser to the backend database, identifying exposed assets, and performing structured content discovery.
* **Estimated Duration:** ~8 Hours
* **Key Tools:** Browser Developer Tools, `gobuster`, `dirsearch`, `ffuf`, `wfuzz`, `sublist3r`, `crt.sh`.
* **Rooms Included:**
  1. **Walking An Application:** Inspecting HTML/CSS/JS source code, debugging cookies, local storage, network requests, and developer console.
  2. **Content Discovery:** Discovering unlinked endpoints via robots.txt, sitemaps, HTTP headers, favicons (MD5/MurmurHash), automated directory brute-forcing.
  3. **Subdomain Enumeration:** SSL/TLS certificate transparency logs (`crt.sh`), search engine operators, DNS brute-forcing, virtual host fuzzing.
  4. **Modern Web Stacks:** Single-Page Applications (SPAs), REST & GraphQL APIs, microservices, containerized web servers.

---

### Module 04: Burp Suite Mastery
* **Description:** Complete operational mastery of the industry-standard web application security testing framework developed by PortSwigger.
* **Estimated Duration:** ~10 Hours
* **Key Tool:** Burp Suite Community / Professional Edition.
* **Rooms Included:**
  1. **Burp Suite: The Basics:** Architecture of an intercepting proxy, installing CA root certificates, intercepting and modifying live HTTP/S traffic, Scope configuration.
  2. **Burp Suite: Repeater:** Crafting, modifying, and replaying raw HTTP requests; analyzing response headers, status codes, and timing.
  3. **Burp Suite: Intruder:** Automated fuzzing, attacks types (Sniper, Battering Ram, Pitchfork, Cluster Bomb), payload configuration, grep match rules.
  4. **Burp Suite: Other Modules:** Decoder (URL, Base64, Hex, HTML), Comparer (visual request/response diffing), Sequencer (session token entropy analysis).
  5. **Burp Suite: Extensions:** Installing and configuring BApp Store extensions (e.g., FoxyProxy, Hackvertor, Autorize).

---

### Module 05: Web Application Vulnerabilities & Exploitation
* **Description:** Exhaustive technical dissection and hands-on exploitation of high-impact OWASP Top 10 vulnerabilities.
* **Estimated Duration:** ~16 Hours
* **Rooms Included:**
  1. **Authentication Bypass:** Username enumeration, password brute-forcing, logic flaws in authentication workflows, cookie manipulation.
  2. **IDOR (Insecure Direct Object Reference):** Missing access control checks, predictable object identifiers, horizontal vs. vertical privilege escalation.
  3. **File Inclusion:** Local File Inclusion (LFI) via path traversal (`../`), null-byte injection (`%00`), PHP wrappers (`php://filter`, `data://`), Remote File Inclusion (RFI) for code execution.
  4. **Intro to SSRF:** Server-Side Request Forgery mechanics, attacking internal cloud metadata services (`169.254.169.254`), internal port scanning.
  5. **Cross-Site Scripting (XSS):** Stored XSS, Reflected XSS, DOM-based XSS, cookie stealing, credential harvesting, CSRF exploitation via XSS payloads.
  6. **Command Injection:** Operating system command execution via unsanitized inputs, shell metacharacters (`;`, `&&`, `|`, `` ` ``, `$()`), blind command injection using time delays and out-of-band exfiltration.
  7. **SQL Injection (SQLi):** In-band (Union-based) SQLi, error-based SQLi, blind boolean-based SQLi, blind time-based SQLi, extracting database schemas, reading/writing local files.

---

### Module 06: Vulnerability Research & Analysis
* **Description:** Systematic discovery, verification, and evaluation of security flaws using public intelligence feeds, CVE databases, and exploit repositories.
* **Estimated Duration:** ~6 Hours
* **Key Tools:** `searchsploit`, Exploit-DB, NVD, GitHub advisories.
* **Rooms Included:**
  1. **Vulnerabilities 101:** Common Vulnerabilities and Exposures (CVE), Common Weakness Enumeration (CWE), Common Vulnerability Scoring System (CVSS v2/v3).
  2. **Exploit Vulnerabilities:** Searching for and verifying public exploits with `searchsploit`, evaluating exploit reliability, modifying exploit code (ports, IPs, payloads).
  3. **Vulnerability Capstone:** End-to-end practical challenge to research, adapt, and execute an exploit against a vulnerable target.

---

### Module 07: Metasploit Framework
* **Description:** Comprehensive mastery of Rapid7's Metasploit Framework for structured vulnerability verification and penetration testing.
* **Estimated Duration:** ~8 Hours
* **Key Tools:** `msfconsole`, `msfvenom`, PostgreSQL integration.
* **Rooms Included:**
  1. **Metasploit: Introduction:** MSF architecture, workspace management, module categories (Exploits, Auxiliaries, Post, Payloads, Encoders, Nops), search syntax.
  2. **Metasploit: Exploitation:** Configuring targets, LHOST/LPORT, multi/handler listeners, payload execution, managing active sessions.
  3. **Metasploit: Meterpreter:** Core Meterpreter commands (`sysinfo`, `getuid`, `hashdump`, `migrate`, `screenshot`, `portfwd`, `route`).

---

### Module 08: Privilege Escalation & Post-Exploitation
* **Description:** Transitioning from an initial unstable access foothold to administrative (`root` / `NT AUTHORITY\SYSTEM`) control on Linux and Windows targets.
* **Estimated Duration:** ~12 Hours
* **Key Tools:** `netcat`, `socat`, Python pty, LinPEAS, WinPEAS, PowerUp.
* **Rooms Included:**
  1. **What the Shell?:** Reverse shells vs. bind shells; Netcat, Socat, and OpenSSL encrypted shells; spawning TTY shells with Python, `script`, and `stty`.
  2. **Linux Privilege Escalation:** SUID/SGID binaries, `sudo -l` privileges (GTFOBins), cron jobs & wildcard injection, PATH variable hijacking, kernel exploits.
  3. **Windows Privilege Escalation:** Unquoted service paths, weak service permissions, Registry Run keys, AlwaysInstallElevated, token impersonation (SeImpersonatePrivilege / RottenPotato).

---

### Module 09: Enterprise Infrastructure & Active Directory
* **Description:** Modern corporate network architectures, Active Directory domain controller interaction, and common enterprise attack paths.
* **Estimated Duration:** ~10 Hours
* **Key Tools:** BloodHound, SharpHound, Impacket suite (`GetNPUsers.py`, `GetUserSPNs.py`, `secretsdump.py`), Mimikatz.
* **Rooms Included:**
  1. **Active Directory Basics:** Domains, Forests, Domain Controllers, LDAP, Kerberos, Group Policy Objects (GPOs).
  2. **Breaching AD:** AS-REP Roasting, password spraying, default domain configurations.
  3. **Enumerating Active Directory:** Graph-based attack path analysis with BloodHound, querying LDAP objects.
  4. **Lateral Movement and Persistence:** Pass-the-Hash, Pass-the-Ticket, Kerberoasting, golden/silver tickets.
