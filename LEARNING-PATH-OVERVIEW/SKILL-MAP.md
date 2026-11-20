# Practical Skill Map: TryHackMe Jr Penetration Tester
## خريطة المهارات والجدارات العملية لمختبر الاختراق

---

### 🗺️ Overview | نظرة عامة
This skill map visualizes the progression of practical competencies acquired across the **Jr Penetration Tester** curriculum. It bridges academic theory with field-deployable technical capabilities required during red teaming, penetration testing, and CTF competitions.

```mermaid
flowchart TD
    subgraph Foundations ["Phase 1: Foundations & Legal Frameworks"]
        F1["Pentest Ethics & Scope"] --> F2["Methodologies (PTES, NIST)"]
        F2 --> F3["Cyber Kill Chain Stages"]
    end

    subgraph Recon ["Phase 2: Discovery & Enumeration"]
        R1["Passive OSINT & DNS"] --> R2["Active Scanning (Nmap)"]
        R2 --> R3["Service & OS Fingerprinting"]
        R3 --> R4["Web Content Discovery (ffuf, gobuster)"]
    end

    subgraph WebTesting ["Phase 3: Web Assessment & Burp Suite"]
        W1["Burp Suite Proxy & Intercept"] --> W2["Manual Request Replay (Repeater)"]
        W2 --> W3["Automated Fuzzing (Intruder)"]
        W3 --> W4["Web Flaw Exploitation (SQLi, LFI, SSRF, XSS)"]
    end

    subgraph Exploitation ["Phase 4: Exploitation & Tooling"]
        E1["CVE & Exploit-DB Research"] --> E2["Metasploit Framework Exploits"]
        E2 --> E3["Payload Crafting (msfvenom)"]
        E3 --> E4["Reverse / Bind Shell Stabilization"]
    end

    subgraph PostExploitation ["Phase 5: Post-Exploitation & Domain Hacking"]
        P1["Linux PrivEsc (SUID, Sudo, Cron)"] --> P3["Root Privilege Access"]
        P2["Windows PrivEsc (Services, Tokens)"] --> P4["SYSTEM Privilege Access"]
        P3 --> P5["Active Directory Enumeration (BloodHound)"]
        P4 --> P5
        P5 --> P6["Domain Compromise (Kerberoasting, Pass-the-Hash)"]
    end

    Foundations --> Recon
    Recon --> WebTesting
    Recon --> Exploitation
    WebTesting --> Exploitation
    Exploitation --> PostExploitation
```

---

### 📊 Competency Matrix | مصفوفة الجدارات العملية

| Domain | Key Capabilities | Relevant Tools | Rooms Mapped |
|---|---|---|---|
| **Engagement Planning** | Scoping, ROE, Risk Scoring ($T \times V = R$), Legal Boundaries | Markdown, Reporting Templates | `pentestingfundamentals`, `principlesofsecurity`, `diveintopentesting`, `pentestingframeworks` |
| **Network Reconnaissance** | Host discovery, firewall evasion, banner grabbing, NSE scripting | `nmap`, `dig`, `whois`, `netcat`, `tcpdump` | `passiverecon`, `activerecon`, `nmap01` to `nmap04`, `netsecchallenge` |
| **Web Enumeration** | Virtual host fuzzing, directory brute-forcing, JavaScript source auditing | `gobuster`, `ffuf`, Browser DevTools, `curl` | `walkinganapplication`, `contentdiscovery`, `subdomainenumeration`, `modernwebstacks` |
| **Proxy & Traffic Auditing** | Request interception, payload fuzzing, entropy verification, token analysis | `burpsuite` (Repeater, Intruder, Decoder, Sequencer) | `burpsuitebasics`, `burpsuiterepeater`, `burpsuiteintruder`, `burpsuiteom` |
| **Application Exploitation** | SQL Injection, Remote Code Execution, Path Traversal, SSRF, XSS, IDOR | Custom Python, Burp Suite, SQLmap | `authenticationbypass`, `idor`, `fileinclusion`, `ssrfqi`, `xss`, `commandinjection`, `sqlinjection` |
| **Vulnerability Research** | CVE lookup, CVSS rating, proof-of-concept compilation & adaptation | `searchsploit`, Exploit-DB, NVD | `vulnerabilities101`, `exploitvulnerabilities`, `vulnerabilitycapstone` |
| **Exploitation Frameworks** | MSF handler orchestration, staged payloads, auxiliary scanners | `msfconsole`, `msfvenom` | `metasploitintro`, `metasploitexploitation`, `meterpreter` |
| **Interactive Shells** | Terminal stabilization, encrypted tunnels, Socat TTY, PTY spawning | `nc`, `socat`, `python3 -m pty`, `stty` | `whattheshell` |
| **Host Privilege Escalation** | SUID abuse, sudo misconfiguration, unquoted service paths, token theft | LinPEAS, WinPEAS, GTFOBins, PowerUp | `linprivesc`, `winprivesc` |
| **Enterprise AD Operations** | LDAP enumeration, Kerberos attacks (AS-REP, Kerberoasting), Pass-the-Hash | BloodHound, Impacket, Mimikatz | `activedirectorybasics`, `breachingad`, `enumeratingad`, `lateralmovementad` |
