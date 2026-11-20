# Task 01: Introduction

---

## Stage A: Original Task Content & Briefing Summary

Task 01 bridges the theoretical and manual enumeration concepts learned in the previous room (*Linux Privilege Escalation: Enumeration*) into practical, hands-on exploitation. Once misconfigurations, weak permissions, or insecure administrative delegations are discovered on a target host, the penetration tester must weaponize these vectors to elevate privileges from an initial unprivileged user (`john`) to full administrative root access (`root`).

### Core Escalation Vectors Covered in this Room:
1. **Sudo Delegation Abuse:** Exploiting overly permissive `sudoers` rules, interactive application functions, and environment variable preservation (`LD_PRELOAD`).
2. **SUID / SGID Binaries:** Exploiting binaries running with the file owner's privileges, abusing file reading/writing primitives, or spawning elevated shells via GTFOBins.
3. **PATH Environment Hijacking:** Manipulating `$PATH` lookups when SUID binaries invoke system utilities without absolute paths.
4. **Linux Capabilities:** Taking advantage of granular privilege assignments (such as `cap_setuid+ep`) on binaries like Python to alter process effective UIDs.
5. **Scheduled Cron Automation:** Hijacking writable scripts, crontab files, or wildcard expansions executed by root cron daemons.
6. **Insecure Network File Sharing (NFS):** Exploiting NFS exports configured with `no_root_squash` to plant and execute root-owned SUID payloads from an external attacker machine.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Modular Target Architecture:** In this laboratory room, each task corresponds to an isolated, purpose-built target virtual machine or scenario, demonstrating how each vector operates in isolation without interference from other misconfigurations.
* **Exploitation Stability & Hygiene:** Unlike kernel exploits which risk crashing production servers, configuration-based exploitation is predictable and safe. However, modifying shared files like `/etc/passwd` or crontab requires extreme care: syntax errors in `/etc/sudoers` or `/etc/passwd` can lock out legitimate administrators or disable critical daemons.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read through the introduction briefing to understand the six major privilege escalation vectors.
* **Tier 2 Hint:** Notice that each subsequent task focuses on a distinct escalation category.
* **Tier 3 Hint:** Click the completion button to proceed to Task 02.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

Privilege escalation represents the critical tipping point in an infrastructure assessment. Moving from a constrained user context to root grants unlimited access to kernel memory, cryptographic keyrings, network packet capture, and system configuration files.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Ready to escalate privileges!
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Verified task completion status on TryHackMe room `linprivbasics`.
* Confirmed readiness to begin hands-on exploitation across all six technical categories.

---

## Stage G: Key Educational Takeaway

Every Linux privilege escalation vulnerability boils down to a failure of least privilege. Whether an administrator granted excessive sudo rights, misapplied an SUID bit, or exported an NFS share without root squash, understanding the underlying mechanism allows you to exploit it reliably.
