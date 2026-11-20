# The Practical Penetration Testing Handbook: Foundations to Field Operations
## Official Textbook Companion to the TryHackMe Jr Penetration Tester Curriculum

---

### 📖 Preface & Textbook Objectives
This volume is an authoritative, end-to-end technical textbook constructed from the comprehensive curriculum of the **TryHackMe Jr Penetration Tester** path.

Rather than presenting disjointed lab writeups, this book is engineered as a unified academic and field manual that guides the practitioner through the complete offensive lifecycle:
$$\text{Foundational Principles} \longrightarrow \text{Reconnaissance & Mapping} \longrightarrow \text{Web Assessments} \longrightarrow \text{Vulnerability Research & MSF} \longrightarrow \text{Shellcraft & PrivEsc} \longrightarrow \text{Active Directory Compromise}$$

---

### 📚 Table of Contents & Chapter Blueprint

* **Chapter 1: Legal Frameworks, Ethics, and Penetration Testing Methodology**
  * Authorized offensive security vs. criminal compromise.
  * Standardized frameworks: PTES, NIST SP 800-115, OSSTMM, and OWASP.
  * The 7 phases of the Lockheed Martin Cyber Kill Chain.
  * Scoping engagements, risk equations ($Risk = Threat \times Vulnerability$), and Rules of Engagement (RoE).

* **Chapter 2: Network Reconnaissance, Scanning Mechanics, and Port Interrogation**
  * Differentiating passive intelligence gathering (OSINT, DNS, WHOIS) from active scanning.
  * Internal mechanics of Nmap: TCP packet header analysis (SYN, ACK, FIN, RST, PSH, URG).
  * State determination: Open, Closed, Filtered, Unfiltered, and Open|Filtered.
  * Firewall detection, IDS/IPS evasion strategies, fragmenting, and decoys (`-D`, `-f`, `-g`).
  * Interrogating standard protocols: SMB, DNS, SNMP, HTTP/S, and SSH.

* **Chapter 3: Web Application Architecture, Mapping, and Proxy Interception**
  * Modern multi-tier web stacks, Single-Page Applications (SPAs), and RESTful APIs.
  * Content and virtual host discovery utilizing fuzzing engines (`gobuster`, `ffuf`).
  * Burp Suite internal mechanics: Intercepting proxy dynamics, TLS termination, and certificate authority trusts.
  * Request manipulation via Repeater and high-speed parameterized attacks via Intruder.

* **Chapter 4: Web Application Vulnerabilities and Exploitation Techniques**
  * Broken access control mechanics: Authentication Bypass and Insecure Direct Object References (IDOR).
  * Arbitrary file inclusion: Local File Inclusion (LFI), path traversal sequences, PHP stream wrappers, and Remote File Inclusion (RFI).
  * Server-Side Request Forgery (SSRF) against internal boundaries and cloud metadata APIs.
  * Command injection: Shell metacharacter chaining, filter circumvention, and out-of-band extraction.
  * SQL Injection: In-band Union-based attacks, boolean inference, and time-based exfiltration.
  * Cross-Site Scripting (XSS): Contextual execution (Stored, Reflected, DOM), cookie theft, and session hijacking.

* **Chapter 5: Vulnerability Research, Exploit Engineering, and the Metasploit Framework**
  * Vulnerability classification: CVE naming syntax, CWE weakness abstractions, and CVSS v3.1 scoring formulas.
  * Sourcing and auditing public exploit code using Exploit-DB and `searchsploit`.
  * Metasploit Framework architecture: Modularity, datastores, database synchronization, staged vs. stageless payloads, and Meterpreter post-exploitation capabilities.

* **Chapter 6: Interactive Shell Mechanics and Host Privilege Escalation**
  * Mechanics of network sockets: Reverse shells vs. bind shells, firewall egress rules, and TTY stabilization.
  * Linux Privilege Escalation: Abusing SUID/SGID bits, `sudo` misconfigurations (GTFOBins), cron wildcards, and environment variables.
  * Windows Privilege Escalation: Exploiting unquoted service paths, permissive service ACLs, registry autostarts, and token impersonation.

* **Chapter 7: Enterprise Environments and Active Directory Compromise**
  * Active Directory domain primitives: Forest architecture, Domain Controllers, Kerberos authentication protocol, and Group Policies.
  * Offline credential extraction: AS-REP Roasting and Kerberoasting attacks.
  * Lateral movement techniques: Pass-the-Hash, Pass-the-Ticket, and visual attack-path graphing with BloodHound.

---
*(Chapters will be populated with in-depth technical prose, sequence diagrams, and real lab evidence concurrently with room progression)*
