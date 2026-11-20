# Official References & Standards: Jump (Challenge)

## Technical References & GTFOBins

1. **GTFOBins: Less Privilege Escalation**
   * *Maintainer:* GTFOBins Community
   * *Scope:* Documentation on interactive shell escape from pagers (`less`, `more`) executed under sudo or SUID.
   * *URL:* https://gtfobins.github.io/gtfobins/less/

2. **Linux Systemd & Cron Insecure Path Resolution**
   * *Publisher:* Linux Man-Pages Project
   * *Scope:* Secure PATH configuration guidelines for system daemons and automated maintenance scripts.
   * *URL:* https://man7.org/linux/man-pages/man5/systemd.service.5.html

---

## Cybersecurity Frameworks & Standards

3. **MITRE ATT&CK Enterprise Matrix**
   * *Tactics & Techniques:*
     * TA0004: Privilege Escalation
       - T1548.003: Sudo and Sudo Caching
       - T1574.007: Path Interception by PATH Environment Variable
       - T1053.003: Cron Scheduled Tasks
     * TA0008: Lateral Movement
       - T1078.003: Local Accounts
   * *URL:* https://attack.mitre.org/

---

## TryHackMe Laboratory Resources

4. **TryHackMe Room: Jump (Challenge)**
   * *Room Slug:* `jump`
   * *Path:* Jr Penetration Tester
   * *Module:* Module 13 — Privilege Escalation
   * *URL:* https://tryhackme.com/room/jump
