# Task 01: Introduction

---

## Stage A: Original Task Content & Briefing Summary

Every operating system ships with factory default configurations. System administrators subsequently layer modifications on top—installing enterprise packages, creating user accounts, configuring network services, and managing file permissions. When these configurations deviate from secure practices, whether through human oversight, convenience, or lack of security awareness, they introduce weaknesses that attackers exploit to escalate privileges.

Privilege escalation is the process of moving from a lower-privileged account (such as `www-data` or an unprivileged service account) to a higher-privileged account (such as `root` on Linux or `SYSTEM` / `Administrator` on Windows).

Privilege escalation vectors fall into two broad architectural categories:
1. **Vulnerability-Based Escalation:** Exploits software bugs, such as unpatched operating system kernel vulnerabilities (e.g. Dirty COW, Dirty Pipe), memory corruption flaws in installed setuid binaries, or known CVEs in local service daemons. Requires unpatched software; patching the vulnerability eliminates the vector.
2. **Configuration-Based Escalation:** Exploits administrative setup errors, such as overly permissive file permissions, insecure service configurations, unquoted service paths, or plaintext credentials stored in accessible locations. Works reliably on fully patched, modern operating systems because it exploits administrative decisions rather than code-level software bugs.

### Learning Objectives:
* Define what a host-server configuration review is and identify when it occurs during an engagement.
* Distinguish between vulnerability-based and configuration-based privilege escalation.
* Describe the structure and purpose of CIS Benchmarks and DISA STIGs.
* Identify automated compliance tooling (Nessus, Lynis, OpenSCAP) and offensive enumeration equivalents (LinPEAS, WinPEAS).
* Categorize major misconfigurations (permissions, services, scheduled tasks, credentials).
* Apply a structured 3-phase enumeration methodology to evaluate target systems.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Patch Independence:** In modern enterprise environments with mature automated patch management, vulnerability-based escalation (kernel exploits) is relatively rare and risky, carrying a high probability of crashing target systems. Configuration-based escalation is far more common, completely reliable, and does not depend on outdated kernels.
* **Dual Perspective:** The defensive checklist used by an auditor to harden an operating system serves as the exact enumeration roadmap for an ethical hacker seeking privilege escalation paths.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read through the introduction briefing to understand the two branches of privilege escalation.
* **Tier 2 Hint:** Notice how configuration-based escalation targets administrative decisions rather than software bugs.
* **Tier 3 Hint:** Click the completion button to proceed.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

The introductory briefing establishes the conceptual foundation of host-server configuration reviews, setting the stage for analyzing CIS Benchmarks, DISA STIGs, and manual enumeration methods.

---

## Stage E: Final Answer & Key Values

* **Question 1:** I understand the difference between vulnerability-based and configuration-based escalation.
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed task completion status on TryHackMe room `hostserverconfigurationreviews`.
* Verified pedagogical alignment with CIS Benchmarks and DISA STIG standards.

---

## Stage G: Key Educational Takeaway

Privilege escalation relies primarily on configuration weaknesses rather than unpatched software bugs. Understanding secure baselines enables testers to systematically spot high-value misconfigurations.
