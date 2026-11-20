# Official References & Standards: Linux Privilege Escalation: Basics

## Operating System & Kernel Documentation

1. **Linux Capabilities Manual (`capabilities(7)`)**
   * *Publisher:* Linux Man-Pages Project
   * *Scope:* Complete breakdown of POSIX capabilities, thread capability sets, capability bounding sets, and transformations during `execve`.
   * *URL:* https://man7.org/linux/man-pages/man7/capabilities.7.html

2. **Linux Sudoers Manual (`sudoers(5)`)**
   * *Maintainer:* Sudo Project
   * *Scope:* Directives for command aliases, `NOPASSWD`, `secure_path`, environment variables (`env_keep`, `env_reset`), and `LD_PRELOAD` security implications.
   * *URL:* https://www.sudo.ws/docs/man/sudoers.man/

3. **Linux Network File System Exports (`exports(5)`)**
   * *Publisher:* Linux Man-Pages Project
   * *Scope:* Configuration syntax for NFS server exports, `root_squash` vs. `no_root_squash` behaviors, and anonuid/anongid mappings.
   * *URL:* https://man7.org/linux/man-pages/man5/exports.5.html

4. **Linux Crontab Specification (`crontab(5)`)**
   * *Publisher:* Linux Man-Pages Project
   * *Scope:* Cron daemon execution architecture, scheduling syntax, environment limitations, and security considerations.
   * *URL:* https://man7.org/linux/man-pages/man5/crontab.5.html

---

## Offensive Security Resources & Tooling

5. **GTFOBins Catalog: Unix Privilege Escalation**
   * *Maintainer:* GTFOBins Community
   * *Key Binaries Covered:*
     - Nano: https://gtfobins.github.io/gtfobins/nano/
     - Vim: https://gtfobins.github.io/gtfobins/vim/
     - Python: https://gtfobins.github.io/gtfobins/python/
   * *Scope:* Comprehensive reference for abusing Unix binaries to bypass security boundaries, escape shells, and escalate privileges via SUID, Sudo, or Capabilities.

6. **PayloadsAllTheThings: Linux Local Privilege Escalation**
   * *Maintainer:* Swissky & Contributors
   * *Scope:* Curated collection of methodologies for Cron job manipulation, PATH hijacking, and NFS exploitation.
   * *Repository:* https://github.com/swisskyrepo/PayloadsAllTheThings

---

## Cybersecurity Frameworks & Standards

7. **MITRE ATT&CK Enterprise Matrix: Privilege Escalation**
   * *Tactics & Techniques:*
     * TA0004: Privilege Escalation
       - T1548.001: Setuid and Setgid
       - T1548.003: Sudo and Sudo Caching
       - T1053.003: Cron Scheduled Tasks
       - T1574.007: Path Interception by PATH Environment Variable
       - T1134: Access Token Manipulation (Linux Capabilities)
     * TA0005: Defense Evasion
       - T1070: Indicator Removal
   * *URL:* https://attack.mitre.org/

8. **NIST SP 800-115: Technical Guide to Information Security Testing and Assessment**
   * *Publisher:* National Institute of Standards and Technology (NIST)
   * *Scope:* Target vulnerability validation, escalation testing, and administrative control reviews.
   * *URL:* https://csrc.nist.gov/publications/detail/sp/800-115/final

---

## TryHackMe Laboratory Resources

9. **TryHackMe Room: Linux Privilege Escalation: Basics**
   * *Room Slug:* `linprivbasics`
   * *Path:* Jr Penetration Tester
   * *Module:* Module 13 — Privilege Escalation
   * *URL:* https://tryhackme.com/room/linprivbasics
