# Task 03: Security Baselines and Frameworks

---

## Stage A: Original Task Content & Briefing Summary

A **security baseline** is a documented, consensus-driven standard that defines how a host operating system, service, or network device must be configured to achieve an acceptable security posture. Without baselines, administrators must guess which settings are secure, leading to inconsistent and fragile security configurations across an organization.

### 1. CIS Benchmarks:
Published by the **Center for Internet Security (CIS)**, these benchmarks provide prescriptive hardening guidance developed through global consensus across industry, government, and academia:
* **Coverage:** Linux distributions, Windows Server, Windows Desktop, cloud platforms (AWS, Azure, GCP), databases, and network devices.
* **Structure of Recommendations:**
  * Title and Unique Identifier (e.g. `1.1.1.1`).
  * Profile Applicability (Level 1 vs. Level 2).
  * Description of the required setting.
  * Security Rationale explaining the underlying threat.
  * Audit Procedure (exact CLI command to test compliance).
  * Remediation Procedure (exact CLI command or configuration edit to resolve non-compliance).
* **CIS Profile Levels:**
  * **Level 1 (Basic / Operational):** Practical, essential security settings that can be deployed broadly with minimal impact on application functionality.
  * **Level 2 (Defense-in-Depth):** High-security settings for sensitive environments. May restrict certain operational features and requires thorough compatibility testing prior to deployment.

### 2. DISA STIGs:
Published by the **Defense Information Systems Agency (DISA)** for the U.S. Department of Defense (DoD), **Security Technical Implementation Guides (STIGs)** are highly prescriptive, legally mandated standards for military and federal government networks.
* **DISA STIG Severity Categories:**
  * **CAT I (Category 1 — Critical / Highest Risk):** Vulnerabilities that directly and immediately result in unauthorized access, complete loss of confidentiality, integrity, or system takeover.
  * **CAT II (Category 2 — Medium Risk):** Vulnerabilities that result in loss of confidentiality, integrity, or availability if exploited in combination with other weaknesses.
  * **CAT III (Category 3 — Low Risk):** Vulnerabilities that degrade security measures, introduce minor administrative risk, or expose system information.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Compliance Mapping in Penetration Testing:** When compiling a penetration testing report, mapping discovered misconfigurations directly to CIS Benchmark numbers (e.g. *"Check 7.1.5 Non-compliant: /etc/shadow permissions are 0644"*) or DISA STIG IDs provides clients with immediate, standardized remediation playbooks that executive audit committees recognize.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Review the DISA STIG severity categories outlined in the briefing.
* **Tier 2 Hint:** Notice the Roman numeral used to denote Category 1.
* **Tier 3 Hint:** The highest risk category is `CAT I`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

In the DISA STIG framework:
* Category I (`CAT I`) represents the highest severity level, defined as any vulnerability that will directly result in loss of life, significant damage to national security, or immediate root/administrator-level compromise of a host.
* Category II (`CAT II`) represents medium severity.
* Category III (`CAT III`) represents low severity.

---

## Stage E: Final Answer & Key Values

* **Question 1:** In a DISA STIG, which severity category represents the highest risk?
  * **Answer:** `CAT I`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed `CAT I` against the completed TryHackMe account.
* Verified DISA STIG severity classification definitions.

---

## Stage G: Key Educational Takeaway

CIS Benchmarks (Levels 1 & 2) and DISA STIGs (`CAT I`, `CAT II`, `CAT III`) provide the global industry standards for host configuration hardening. In DISA STIGs, `CAT I` denotes the highest risk vulnerabilities directly leading to complete system compromise.
