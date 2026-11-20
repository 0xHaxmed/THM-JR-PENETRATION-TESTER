# Task 01: Introduction

---

## Stage A: Original Task Content & Briefing Summary

Privilege escalation involves moving from a lower permission account to a higher permission one through the exploitation of vulnerabilities, design flaws, or configuration oversights. In real-world penetration tests, an initial foothold rarely grants administrative or root-level access. Achieving root privileges empowers security testers and system auditors to:
* Reset passwords and manage user credentials.
* Bypass system access control lists (ACLs) and mandatory access controls (AppArmor/SELinux).
* Access, modify, or exfiltrate sensitive files (`/etc/shadow`, private SSH keys, databases).
* Reconfigure running software daemons and system-wide security policies.
* Establish persistence through root-level cron jobs, systemd services, or SSH authorized keys.
* Execute arbitrary low-level kernel and administrative commands.

### Room Learning Objectives:
* Understand the role of manual enumeration in the Linux privilege escalation lifecycle.
* Execute foundational operating system enumeration commands to identify system architecture, kernel releases, and scheduled tasks.
* Enumerate local user accounts, active environment variables, execution history, and `sudo` rights.
* Audit network interfaces, routing tables, and internal listening sockets.
* Perform targeted filesystem enumeration using `find`, `grep`, and permission filters to uncover hidden flags, sensitive files, and setuid/setgid binaries.

---

## Stage B: Independent Thinking & OpSec Considerations

* **The Fallacy of Automated Scanners:** While automated enumeration scripts like LinPEAS or LinEnum are fast, they generate massive process telemetry, trigger EDR behavioral rules, and often overwhelm the analyst with hundreds of lines of colored text. Understanding manual commands allows operators to work silently and with surgical precision.
* **Living-off-the-Land (LotL):** In hardened enterprise environments, security policies may block transferring or compiling custom binaries. Mastering built-in Linux utilities (`uname`, `id`, `env`, `netstat`, `ss`, `find`, `cat`) ensures you can identify privilege escalation vectors on any UNIX-like target without introducing external artifacts.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the introductory briefing to understand the core objectives of Linux privilege escalation.
* **Tier 2 Hint:** Note that privilege escalation relies on moving from low-privilege accounts (e.g. `john`, `www-data`) to administrative accounts (`root`).
* **Tier 3 Hint:** Click the completion button to mark the task complete.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

Privilege escalation forms Phase 4 (Privilege Escalation) of the Penetration Testing Execution Standard (PTES) and maps directly to the MITRE ATT&CK Privilege Escalation tactic (TA0004). Without initial enumeration, any escalation attempt is blind guessing that risks crashing services or tripping security alarms.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Ready for some enumeration!
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Verified task completion status on TryHackMe room `linprivenum`.
* Confirmed alignment with Linux Fundamentals and post-exploitation workflows.

---

## Stage G: Key Educational Takeaway

Privilege escalation begins with meticulous reconnaissance. Every misconfiguration, permissive file permission, and unmanaged sudo rule is discovered through deliberate, structured enumeration.
