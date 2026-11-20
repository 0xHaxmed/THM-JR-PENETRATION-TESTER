# Task 07: Reading a CIS Benchmark

---

## Stage A: Original Task Content & Briefing Summary

Understanding the structure of an official CIS Benchmark recommendation allows penetration testers to interpret automated compliance scanner reports, understand the exact security intent of a configuration, and extract the underlying audit logic for manual offensive enumeration.

### Anatomy of a CIS Benchmark Recommendation:

1. **Title and Number:**
   * Example: `7.1.5 Ensure permissions on /etc/shadow are configured (Automated)`
   * Uniquely indexes the security check within the benchmark taxonomy.
2. **Profile Applicability:**
   * Level 1 vs. Level 2 (Indicates whether the setting is operational baseline or defense-in-depth).
3. **Description:**
   * Plain-language explanation of the security requirement (e.g. `/etc/shadow` contains encrypted user password hashes and must be owned by `root:shadow` with permissions `0640` or more restrictive).
4. **Rationale:**
   * The technical justification and risk statement (e.g. If unprivileged users can read `/etc/shadow`, they can extract password hashes and perform offline dictionary attacks to recover administrative credentials).
5. **Audit Procedure:**
   * The exact CLI command used to evaluate compliance:
     ```bash
     stat -L -c "%a %u %g" /etc/shadow
     ```
   * Pass criteria: Mode is `0640` or more restrictive; Owner UID is `0` (`root`); Group GID is `0` (`root`) or matching the shadow group.
6. **Remediation Procedure:**
   * The precise command sequence used by administrators to remediate the failure:
     ```bash
     chown root:shadow /etc/shadow
     chmod u-x,g-wx,o-rwx /etc/shadow
     ```

### The Offensive Inversion (Translation to Privilege Escalation):
* **Auditor Perspective:** Check 7.1.5 failed -> Actual permission is `0644` -> Fix permissions to prevent hash theft.
* **Attacker Perspective:** Check 7.1.5 failed -> `/etc/shadow` is world-readable (`0644`) -> Read `/etc/shadow` -> Extract root's SHA-512 hash (`$6$...`) -> Crack hash offline with Hashcat / John the Ripper using `rockyou.txt` -> Authenticate as root (`su root`).
* Automated enumeration scripts like **LinPEAS** simply automate the audit procedures defined in CIS Benchmark sections 7.1.x.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Translating Audits into Custom Scripts:** A penetration tester can extract the "Audit Procedure" code blocks directly from a CIS Benchmark PDF to build bespoke, stealthy audit one-liners that evade EDR heuristics triggered by standard offensive scripts like LinPEAS.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Review the structure and sections of a CIS Benchmark recommendation.
* **Tier 2 Hint:** Understand the offensive inversion of audit procedures.
* **Tier 3 Hint:** Click the confirmation button to proceed.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

The briefing details how every CIS Benchmark check maps directly to a potential privilege escalation vector when audit tests fail.

---

## Stage E: Final Answer & Key Values

* **Question 1:** I understand how to read and interpret a CIS Benchmark recommendation.
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed task completion status on TryHackMe room `hostserverconfigurationreviews`.
* Verified structural composition of CIS Ubuntu Linux and Windows Server benchmarks.

---

## Stage G: Key Educational Takeaway

Every failed CIS Benchmark recommendation represents an audit gap on defense, and a potential privilege escalation vector on offense. Understanding audit rationales and audit procedures allows testers to pinpoint high-probability compromise paths.
