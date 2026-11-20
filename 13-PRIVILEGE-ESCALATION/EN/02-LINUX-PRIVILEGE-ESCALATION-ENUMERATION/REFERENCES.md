# Official References & Standards: Linux Privilege Escalation: Enumeration

## Linux System & Security Documentation

1. **Linux Kernel Credential Subsystem Documentation**
   * *Publisher:* The Linux Kernel Organization
   * *Scope:* Architectural documentation on process credentials (`struct cred`), real vs. effective UIDs, capability bounding sets, and filesystem access checks.
   * *URL:* https://www.kernel.org/doc/Documentation/security/credentials.txt

2. **Sudo Manual Pages: sudoers(5) & sudo(8)**
   * *Maintainer:* Todd C. Miller / Sudo Project
   * *Scope:* Formal specifications for `/etc/sudoers` configuration, directive tags (`NOPASSWD`, `NOEXEC`), environment filtering (`env_reset`, `secure_path`), and security implications.
   * *URL:* https://www.sudo.ws/docs/man/sudoers.man/

3. **Linux Procfs Documentation (`proc(5)`)**
   * *Publisher:* Linux Man-Pages Project
   * *Scope:* Technical reference for virtual files under `/proc`, process-specific metadata directories (`/proc/[pid]/environ`, `/proc/[pid]/cmdline`, `/proc/[pid]/fd`), and kernel tunable nodes.
   * *URL:* https://man7.org/linux/man-pages/man5/proc.5.html

---

## Offensive Security References & Exploit Repositories

4. **GTFOBins: Curated List of Unix Binaries for Privilege Escalation**
   * *Maintainer:* GTFOBins Community
   * *Scope:* Complete operational instructions on using standard UNIX binaries (such as `nmap`, `find`, `vim`, `less`, `awk`, `bash`) to bypass local security restrictions and elevate privileges via misconfigured sudo or SUID.
   * *Resource:* https://gtfobins.github.io/gtfobins/nmap/

5. **PayloadsAllTheThings: Linux Privilege Escalation**
   * *Maintainer:* Swissky & Contributors
   * *Scope:* Comprehensive methodology cheat sheet for Linux local privilege escalation, cron job exploitation, path manipulation, and manual enumeration commands.
   * *Repository:* https://github.com/swisskyrepo/PayloadsAllTheThings

---

## Cybersecurity Frameworks & Standards

6. **MITRE ATT&CK Enterprise Matrix: Discovery & Privilege Escalation**
   * *Tactics & Techniques:*
     * TA0007: Discovery
       - T1082: System Information Discovery (`uname`, `hostname`, `/etc/issue`)
       - T1083: File and Directory Discovery (`find`, `ls -la`)
       - T1087.001: Account Discovery: Local Accounts (`id`, `/etc/passwd`)
       - T1049: System Network Connections Discovery (`ss`, `netstat`)
       - T1057: Process Discovery (`ps aux`, `ps axjf`)
     * TA0004: Privilege Escalation
       - T1548.003: Sudo and Sudo Caching
       - T1053.003: Cron Scheduled Tasks
   * *URL:* https://attack.mitre.org/

7. **NIST SP 800-115: Technical Guide to Information Security Testing and Assessment**
   * *Publisher:* National Institute of Standards and Technology (NIST)
   * *Scope:* Post-exploitation flaw analysis, administrative delegation review, and host assessment methodologies.
   * *URL:* https://csrc.nist.gov/publications/detail/sp/800-115/final

---

## TryHackMe Laboratory Resources

8. **TryHackMe Room: Linux Privilege Escalation: Enumeration**
   * *Room Slug:* `linprivenum`
   * *Path:* Jr Penetration Tester
   * *Module:* Module 13 — Privilege Escalation
   * *URL:* https://tryhackme.com/room/linprivenum
