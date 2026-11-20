# Official References & Standards: Linux Privilege Escalation: Automation

## Automated Enumeration Tool Repositories

1. **PEASS-ng: Privilege Escalation Awesome Scripts Suite (LinPEAS)**
   * *Author:* Carlos Polop
   * *Scope:* Comprehensive privilege escalation scanner auditing files, credentials, network sockets, SUID binaries, and system configurations with color-coded severity.
   * *Repository:* https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS

2. **Linux Exploit Suggester (LES)**
   * *Author:* mzet-
   * *Scope:* Automated heuristic tool analyzing kernel builds and system architecture to identify candidate CVE exploits.
   * *Repository:* https://github.com/mzet-/linux-exploit-suggester

3. **pspy: Unprivileged Linux Process Snooping**
   * *Author:* Dominic Breuker
   * *Scope:* Inotify-driven real-time process monitoring tool designed to capture cron executions and ephemeral tasks without root access.
   * *Repository:* https://github.com/DominicBreuker/pspy

4. **LinEnum: Scripted Local Linux Enumeration**
   * *Author:* Rebootuser
   * *Scope:* Shell script for structured privilege escalation discovery.
   * *Repository:* https://github.com/rebootuser/LinEnum

---

## Vulnerability & Exploit Databases

5. **Exploit Database (Exploit-DB) & Searchsploit**
   * *Maintainer:* OffSec
   * *Scope:* Public archive of verified exploit code and security advisories.
   * *URL:* https://www.exploit-db.com/

6. **NIST National Vulnerability Database (NVD)**
   * *Publisher:* National Institute of Standards and Technology
   * *Scope:* Official CVE repository providing vulnerability descriptions and CVSS scoring.
   * *URL:* https://nvd.nist.gov/

---

## Cybersecurity Frameworks & Standards

7. **MITRE ATT&CK Enterprise Matrix**
   * *Tactics & Techniques:*
     * TA0007: Discovery
       - T1082: System Information Discovery
       - T1057: Process Discovery
     * TA0004: Privilege Escalation
       - T1068: Exploitation for Privilege Escalation
       - T1053.003: Cron Scheduled Tasks
       - T1548.003: Sudo and Sudo Caching
   * *URL:* https://attack.mitre.org/

---

## TryHackMe Laboratory Resources

8. **TryHackMe Room: Linux Privilege Escalation: Automation**
   * *Room Slug:* `linprivautomation`
   * *Path:* Jr Penetration Tester
   * *Module:* Module 13 — Privilege Escalation
   * *URL:* https://tryhackme.com/room/linprivautomation
