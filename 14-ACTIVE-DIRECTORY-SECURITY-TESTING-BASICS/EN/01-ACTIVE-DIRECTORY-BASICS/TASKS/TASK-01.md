# Task 01: Introduction

---

## Stage A: Original Task Content & Briefing Summary

Task 01 introduces the foundational purpose of Microsoft Active Directory Domain Services (AD DS) within enterprise environments and sets the operational scenario for the room.

### Operational Scenario:
* The learner steps into the professional role of a newly hired systems and infrastructure administrator at **THM Inc.**
* Large enterprise organizations cannot rely on workgroup or peer-to-peer topologies; they require centralized identity, authentication, and security governance across thousands of endpoints and employees spread across multiple regional offices.
* Over the course of the room, the administrator will:
  1. Administer a parent domain (`thm.loc`) managed by `ROOTDC.THM.LOC`.
  2. Deploy and promote a new child domain (`tbm.thm.loc`) on `CDC1`.
  3. Join client and server systems (`Server1`) to the domain hierarchy.
  4. Structure users, computers, and Organizational Units (OUs).
  5. Deploy centralized configurations through Group Policy Objects (GPOs) and the `SYSVOL` replication share.
  6. Establish bidirectional cross-forest trusts with an external partner organization (`TryVendorMe` / `tvm.loc`) to facilitate secure cross-domain resource delegation.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Enterprise Active Directory as the Master Target:** In offensive cyber operations and adversarial penetration testing, Active Directory is the primary objective of post-exploitation. Gaining a foothold on a single domain-joined machine provides the attacker with access to domain-authenticated RPC, LDAP, and Kerberos services. Understanding how administrators design, deploy, and configure Active Directory is an absolute prerequisite to identifying the architectural flaws, weak ACLs, and trust misconfigurations that permit domain takeover.
* **Lab Environment Pre-Flight:**
  * Network Subnet: `192.168.10.0/24`
  * Gateway/Flag Verification Engine: `http://192.168.10.250:5000`

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Review the scenario overview and verify network connectivity to the lab subnet.
* **Tier 2 Hint:** Understand the distinction between isolated local accounts and centralized domain identities.
* **Tier 3 Hint:** Click the "Completed" button to confirm readiness and proceed to Task 02.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

This introductory task requires establishing awareness of the lab network and confirming readiness.

1. Ensure the AttackBox or local VPN connection can route packets into the `192.168.10.0/24` subnet.
2. Confirm access to the automated flag verification dashboard at `http://192.168.10.250:5000`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** I am ready to learn about Active Directory!
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed task completion status in the TryHackMe interface.
* Target environment topology and IP assignments mapped and verified.

---

## Stage G: Key Educational Takeaway

Active Directory Domain Services is the central nervous system of enterprise networks. Understanding the structural administrative primitives of domains, OUs, GPOs, and forest trusts is essential for both systems hardening and adversarial security testing.
