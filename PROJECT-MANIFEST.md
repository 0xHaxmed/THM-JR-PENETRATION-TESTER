# PROJECT MANIFEST: TryHackMe Jr Penetration Tester (Verified Real Curriculum)
## فهرس المنهج الفعلي المعتمد لمسار مختبر الاختراق المبتدئ

* **Path Name:** Jr Penetration Tester
* **Platform:** [TryHackMe](https://tryhackme.com)
* **Outline URL:** [https://tryhackme.com/path/outline/jrpenetrationtester](https://tryhackme.com/path/outline/jrpenetrationtester)
* **Curriculum Status:** 100% Verified against User Completed Account (18 Modules, ~90+ Rooms & Challenges)
* **Certification Target:** TryHackMe PT1 (Junior Penetration Tester)

---

## 🏛️ Comprehensive Curriculum Inventory (18 Modules)

### Module 01: Start Your Cyber Security Journey
*Description: Explore offensive and defensive cyber security via interactive exercises and acquire essential search skills to find information.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 01.01 | Offensive Security Intro | `offensivesecurityintro` | First hands-on website hack in safe lab |
| 01.02 | Defensive Security Intro | `defensivesecurityintro` | Threat Intel, SOC, DFIR, Malware, SIEM |
| 01.03 | Search Skills | `searchskills` | Internet research, specialized engines, docs |
| 01.04 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 02: Penetration Testing Foundations
*Description: Learn core penetration testing concepts through two guided engagements and the frameworks that shape every real world test.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 02.01 | Guided Pentest: Web | `guidedpentestweb` | Chaining flaws from recon to full compromise |
| 02.02 | Guided Pentest: Infrastructure | `guidedpentestinfrastructure` | Real-life infrastructure compromise lifecycle |
| 02.03 | Dive Into Pentesting | `diveintopentesting` | Pentesting basics, methodology, responsible practice |
| 02.04 | Cyber Kill Chain | `cyberkillchain` | Lockheed Martin Cyber Kill Chain phases |
| 02.05 | Penetration Testing Frameworks | `pentestingframeworks` | Landscape of industry frameworks (PTES, NIST) |
| 02.06 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 03: Network Reconnaissance
*Description: Gather intelligence on a target through passive and active reconnaissance, then probe network services that keep modern infrastructure running.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 03.01 | Passive Reconnaissance | `passiverecon` | whois, nslookup, dig, OSINT |
| 03.02 | Active Reconnaissance | `activerecon` | traceroute, ping, telnet, browser |
| 03.03 | Protocols and Servers | `protocolsandservers` | HTTP, FTP, POP3, SMTP, IMAP vulnerabilities |
| 03.04 | Protocols and Servers 2 | `protocolsandservers2` | Cleartext attacks, SSH, SSL/TLS mitigation |
| 03.05 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 04: Nmap
*Description: Learn how to leverage the Nmap network scanner to discover live hosts and open ports using basic and advanced scan options.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 04.01 | Nmap Live Host Discovery | `nmap01` | ARP, ICMP, TCP/UDP ping scans |
| 04.02 | Nmap Basic Port Scans | `nmap02` | TCP Connect, TCP SYN, UDP port scans |
| 04.03 | Nmap Advanced Port Scans | `nmap03` | Null, FIN, Xmas, Idle scans, spoofing |
| 04.04 | Nmap Post Port Scans | `nmap04` | Service & OS detection, NSE, saving results |
| 04.05 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 05: Web Application Security Fundamentals
*Description: Map how modern web applications are built, uncover their hidden endpoints, and exploit the most common web server weaknesses.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 05.01 | Walking An Application | `walkinganapplication` | Browser developer tools security review |
| 05.02 | Content Discovery | `contentdiscovery` | OSINT, manual techniques, Gobuster |
| 05.03 | Modern Web Stacks | `modernwebstacks` | MERN, Next.js, Django, LAMP exploitation |
| 05.04 | Web Server Attacks - I | `webserverattacks1` | Apache, Nginx, Node.js, Python HTTP Server |
| 05.05 | Web Server Attacks - II | `webserverattacks2` | IIS, Tilde enumeration, WebDAV shell upload |
| 05.06 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 06: Burp Suite
*Description: Burp Suite is the industry-standard tool for web application hacking, and is essential in any web penetration test.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 06.01 | Burp Suite: The Basics | `burpsuitebasics` | Intercepting proxy configuration & usage |
| 06.02 | Burp Suite: Repeater | `burpsuiterepeater` | Manual request duplication and replay |
| 06.03 | Burp Suite: Intruder | `burpsuiteintruder` | Automated requests, fuzzing, attack types |
| 06.04 | Burp Suite: Other Modules | `burpsuiteom` | Sequencer, Decoder, Comparer |
| 06.05 | Burp Suite: Extensions | `burpsuiteextender` | BApp Store extensions |
| 06.06 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 07: Web Application Vulnerabilities I
*Description: Exploit five of the most impactful web vulnerabilities, from SQL injection to IDOR, and prove your skills in a hands-on challenge.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 07.01 | SQL Injection Introduction | `sqlinjection` | Detection and exploitation of SQLi |
| 07.02 | CSRF Introduction | `csrf` | Cross-Site Request Forgery mechanics |
| 07.03 | XSS Introduction | `xss` | Cross-Site Scripting to control browsers |
| 07.04 | Intro to SSRF | `ssrfqi` | Server-Side Request Forgery & internal access |
| 07.05 | IDOR | `idor` | Insecure Direct Object Reference exploitation |
| 07.06 | Recruit | `recruit` | **Live Challenge Lab:** Infiltrate portal, map & gain access |
| 07.07 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 08: Web Application Vulnerabilities II
*Description: Tackle deeper web vulnerabilities like broken authentication, command injection, and API flaws, then prove yourself in a live challenge.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 08.01 | Session Management | `sessionmanagement` | Session attacks & insecure implementations |
| 08.02 | Broken Authentication | `brokenauthentication` | Defeating logins & auth logic flaws |
| 08.03 | File Inclusion | `fileinclusion` | LFI, RFI, directory traversal, PHP wrappers |
| 08.04 | Command Injection | `commandinjection` | OS command execution & remediations |
| 08.05 | API Pentesting | `apipentesting` | Common API vulnerabilities & exploitation |
| 08.06 | Support | `support` | **Live Challenge Lab:** Pentest Support Ops for RCE |
| 08.07 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 09: Vulnerability Knowledge
*Description: Learn how to research vulnerabilities in public databases, scan for them with industry tools, and identify them in real systems.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 09.01 | Understanding Vulnerability Databases | `understandingvulnerabilitydatabases` | NVD, CVE, Exploit-DB |
| 09.02 | Vulnerability Scanning Tools | `vulnerabilityscanningtools` | Nmap, OpenVAS, Nikto |
| 09.03 | Basic Vulnerability Identification Techniques | `basicvulnerabilityidentificationtechniques` | Identification workflows |
| 09.04 | NoScope: Finding RCE | `noscopefindingrce` | Zero-day CVE-2024-38463 All-In-RCE |
| 09.05 | mIRC CVE-2024-40613 | `mirccve202440613` | Exploiting mIRC client vulnerability |
| 09.06 | AD: BadSuccessor | `adbadsuccessor` | Privilege escalation in Active Directory |
| 09.07 | CVE-2024-46300: Fragmedia | `fragmedia` | LFI surfacing post-patch |
| 09.08 | CVE-2024-42945: Nginx Rift | `nginxrift` | Unauthenticated heap overflow in rewrite module |
| 09.09 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 10: OWASP Top 10 (2025)
*Description: Explore, exploit, and learn how to remediate the most critical web application security risks listed in the OWASP Top 10 (2025).*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 10.01 | OWASP Top 10 2025: IAAA Failures | `owasptop102025iaaa` | A01, A07, A09 IAAA model failures |
| 10.02 | OWASP Top 10 2025: Application Design Flaws | `owasptop102025designflaws` | A02, A04, A06, A10 design weaknesses |
| 10.03 | OWASP Top 10 2025: Insecure Data Handling | `owasptop102025datahandling` | A03, A05, A08 insecure data handling |
| 10.04 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 11: Password Attacks
*Description: Explore the full password attack arsenal, from phishing to offline cracking, and prove yourself in a live challenge.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 11.01 | Phishing Basics | `phishingbasics` | Phishing techniques and pentest tooling |
| 11.02 | Hydra | `hydra` | Fast network logon brute-forcing |
| 11.03 | Introduction to Wordlists | `wordlists` | OSINT-targeted wordlists |
| 11.04 | Password Cracking | `passwordcracking` | Hash types, Hashcat, John the Ripper |
| 11.05 | Checkmate | `checkmate` | **Live Challenge Lab:** Weak passwords compromise |
| 11.06 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 12: Metasploit and Exploitation
*Description: Master Metasploit and the manual exploitation skills behind it, from scanning and post-exploitation to building your own shells and payloads.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 12.01 | Metasploit: The Basics | `metasploitintro` | msfconsole, modules, sessions |
| 12.02 | Metasploit: Scanning and Exploitation | `metasploitexploitation` | Database, scanning, live exploitation |
| 12.03 | Metasploit: Post-Exploitation | `meterpreter` | Meterpreter, privilege escalation, loot |
| 12.04 | Metasploit: Payload Generation | `metasploitpayloadgeneration` | msfvenom formats, handlers |
| 12.05 | Exploitation and Weaponization | `exploitationandweaponization` | Weaponizing findings, real-world impact |
| 12.06 | Shells & Listeners Fundamentals | `shellslistenersfundamentals` | Reverse & bind shells creation and stabilization |
| 12.07 | Shell Payload Generation & Delivery | `shellpayloadgeneration` | msfvenom, web shells, delivery |
| 12.08 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 13: Privilege Escalation
*Description: Learn privilege escalation across Linux and Windows, from enumeration and automation to two live challenge environments.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 13.01 | Host-Server Configuration Reviews | `hostserverconfigurationreviews` | System configuration audit basics |
| 13.02 | Linux Privilege Escalation: Enumeration | `linprivesc` | Gathering target host intelligence |
| 13.03 | Linux Privilege Escalation: Basics | `linprivescbasics` | 8+ hands-on privesc techniques |
| 13.04 | Linux Privilege Escalation: Automation | `linprivescautomation` | Automated scripts (LinPEAS) and public exploits |
| 13.05 | Windows Privilege Escalation | `winprivesc` | Windows privesc fundamentals |
| 13.06 | Jump | `jump` | **Live Challenge Lab:** Jump from normal user to root |
| 13.07 | Windows Jump | `windowsjump` | **Live Challenge Lab:** Jump from guest to SYSTEM |
| 13.08 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 14: Active Directory Security Testing Basics
*Description: Step into the world of Active Directory attacks, from breaching the domain to lateral movement, then prove yourself in two live challenges.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 14.01 | Active Directory Basics | `activedirectorybasics` | Concepts & functionality of AD |
| 14.02 | Intro to AD Authentication | `introadauthentication` | AD protocols, Kerberos, NTLM |
| 14.03 | Intro to AD Breaching | `introadbreaching` | Username enum, password spraying, coercion |
| 14.04 | AD: Basic Enumeration | `adbasicenumeration` | Enumerating AD network & initial access |
| 14.05 | AD: Authenticated Enumeration | `adauthenticatedenumeration` | Enumerating with authenticated accounts |
| 14.06 | Intro to Credential Harvesting | `introtocredentialharvesting` | Windows/AD cached credential extraction |
| 14.07 | Intro to AD Lateral Movement | `introadlateralmovement` | PsExec, WinRM, Pass-the-Hash, SSH tunnels |
| 14.08 | Proxy | `proxy` | **Live Challenge Lab:** Service account to Domain Controller |
| 14.09 | Forward | `forward` | **Live Challenge Lab:** Active Directory challenge |
| 14.10 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 15: Specialized Domains
*Description: Branch out into specialized cyber security domains, from wireless and mobile security to cloud, LLM pentesting, defense, and DevSecOps.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 15.01 | Wireless Security | `wirelesssecurity` | Wireless mechanisms, attacks, defenses |
| 15.02 | Mobile Application Security | `mobileapplicationsecurity` | Static analysis, MobSF, OWASP Mobile Top 10 |
| 15.03 | Cloud Security Fundamentals | `cloudsecurityfundamentals` | Cloud-agnostic attack chains end-to-end |
| 15.04 | LLM Pentesting | `llmpentesting` | Fingerprinting & exploiting LLM components |
| 15.05 | The Blue Team Perspective | `theblueteamperspective` | Understanding defender mindset and telemetry |
| 15.06 | DevSecOps Basics | `devsecopsbasics` | Shifting left, software pipelines, security gates |
| 15.07 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 16: Python Scripting Basics
*Description: Pick up Python from the ground up and use it to build practical scripts that power real penetration testing work.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 16.01 | Python: Simple Demo | `pythonsimpledemo` | Basic Python program structures |
| 16.02 | Python: Core Concepts | `pythoncoreconcepts` | Syntax, loops, data structures, functions |
| 16.03 | Python: Building Scripts | `pythonbuildingscripts` | CLI arguments, socket programming, I/O |
| 16.04 | Python: Pentesting Scripts | `pythonpentestingscripts` | Web recon, port scan, hash crack, SSH brute |
| 16.05 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 17: Pentesting Methodologies and Reporting
*Description: Understand the methodology and reporting behind every professional pentest, from threat modeling and scoping to retesting fixes.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 17.01 | Threat Modeling for Pentesters | `threatmodelingforpentesters` | Threat modeling frameworks (STRIDE, PASTA) |
| 17.02 | Planning and Scoping | `planningandscoping` | Defining scope, RoE, and commercial planning |
| 17.03 | Writing Pentest Reports | `writingpentestreports` | Executive & technical reporting to stakeholders |
| 17.04 | Re-Testing | `retesting` | Verifying remediation and audit reports |
| 17.05 | Topic Transition Recap | `recap` | Synthesis & review quiz |

---

### Module 18: Jr Pentester Challenges
*Description: Put your full pentesting skill set to the test across eight end-to-end challenges and practical labs that mirror real engagements from start to finish.*
| Index | Room Name | Slug / Type | Focus |
|---|---|---|---|
| 18.01 | Domino | `domino` | IDOR, Blind XSS session hijacking, RFI to RCE, SSH credential reuse, Cron privesc |
| 18.02 | Silent Monitor | `silent-monitor` | SQL injection, OS command injection, KeePass .kdbx master key cracking |
| 18.03 | Dead Drop | `dead-drop` | Android APK reverse engineering, Active Directory AddMember privilege escalation |
| 18.04 | Operation Promotion | `operationpromotion` | Upload extension filter bypass, Sudo SCRIPT_FILENAME hijacking to root |
| 18.05 | Operation Coldstart | `operationcoldstart` | Anonymous FTP, Python Flask SSRF to internal notes, Tar wildcard injection |
| 18.06 | Net Sec Challenge | `netsecchallenge` | Full port scanning, service banner grabbing, Hydra FTP brute force, TCP stealth scans |
| 18.07 | Interceptor | `interceptor` | Backup file discovery, Burp response manipulation authentication bypass, OS command injection |
| 18.08 | Topic Transition Recap | `6afe01c8b7a1485ac0cf8ade` | Synthesis of all practical challenge domains & grand capstone evaluation |
