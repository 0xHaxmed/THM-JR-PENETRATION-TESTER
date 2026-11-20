# Task 04: Automated Compliance Tooling

---

## Stage A: Original Task Content & Briefing Summary

Evaluating complex enterprise hosts manually against hundreds of individual CIS Benchmark recommendations across thousands of servers is practically impossible without automation. Specialized compliance auditing tools evaluate target hosts programmatically against established security baselines.

### 1. Defensive Compliance Scanners:
* **Nessus (Tenable):** A leading commercial vulnerability scanner that includes extensive compliance auditing engines. By providing SSH or Windows credentials, Nessus audits hosts against official CIS Benchmarks, DISA STIGs, and custom `.audit` files, generating comprehensive compliance reports highlighting pass, fail, and warning states.
* **Lynis:** An open-source, lightweight security auditing and compliance tool for Linux, Unix, and macOS systems. Installed and executed locally on the target host, Lynis audits hundreds of system attributes, assigns a numerical **Hardening Index** score, and outputs prioritized security suggestions and warnings.
* **OpenSCAP:** An open-source, certified Security Content Automation Protocol (SCAP) engine maintained by the open-source community and Red Hat. It reads standardized XCCDF and OVAL definitions to evaluate hosts against government and industry security baselines, generating machine-readable XML and HTML pass/fail audit reports.
* **CIS-CAT (CIS Configuration Assessment Tool):** The official auditing tool developed by CIS, available in free (Lite) and commercial (Pro) versions, designed to assess hosts directly against CIS Benchmarks.

### 2. Offensive Enumeration Tools:
Offensive security professionals run specialized scripts on compromised hosts to discover privilege escalation vectors:
* **LinPEAS (Linux Privilege Escalation Awesome Script):** Audits permissions, environment variables, SUID binaries, cron jobs, network sockets, and software configurations, formatting findings in color-coded output indicating high-probability escalation paths.
* **WinPEAS (Windows Privilege Escalation Awesome Script):** Audits Windows services, unquoted service paths, registry keys, credential vaults, and installed applications.
* **PowerUp:** A PowerShell framework focused on auditing Windows privilege escalation vectors.

### The Duality of Compliance and Exploitation:
Compliance auditing tools and offensive enumeration tools are two sides of the same coin:
* The compliance scanner flags a "Fail: Check 7.1.5 - /etc/shadow is world-readable" so administrators can remediate it.
* The offensive enumeration script flags the exact same world-readable `/etc/shadow` file so an ethical hacker can crack the root password hash and escalate privileges.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Internal Assessment Advantages:** In internal grey-box or white-box penetration tests, obtaining read-only access to existing Nessus compliance reports provides an instantaneous, pre-compiled roadmap of every misconfiguration and hardening gap across the client's internal network.
* **Script Footprints:** Running heavy automated enumeration tools like LinPEAS or WinPEAS generates extensive process telemetry and filesystem reads. In stealthy red team engagements, manual targeted CLI inspection is preferred over dropping large scripts.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the briefing section on commercial vulnerability scanners developed by Tenable.
* **Tier 2 Hint:** What famous vulnerability scanner was created by Tenable?
* **Tier 3 Hint:** The tool is `Nessus`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

Tenable **Nessus** is the industry-standard commercial vulnerability scanner that integrates compliance and configuration auditing:
* It ingests `.audit` policy files based on CIS Benchmarks and DISA STIGs.
* It uses authenticated credentials (SSH for Linux, SMB/WMI for Windows) to interrogate configuration files, permissions, and registry settings without agent deployment.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What commercial vulnerability scanner, developed by Tenable, includes compliance auditing functionality against CIS Benchmarks?
  * **Answer:** `Nessus`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed `Nessus` against the completed TryHackMe account.
* Verified that Tenable Nessus maintains an extensive library of CIS Benchmark audit policies.

---

## Stage G: Key Educational Takeaway

Automated compliance tools (Nessus, Lynis, OpenSCAP) and offensive privilege escalation scripts (LinPEAS, WinPEAS) interrogate the exact same configuration baselines: one flags them for hardening, the other targets them for exploitation.
