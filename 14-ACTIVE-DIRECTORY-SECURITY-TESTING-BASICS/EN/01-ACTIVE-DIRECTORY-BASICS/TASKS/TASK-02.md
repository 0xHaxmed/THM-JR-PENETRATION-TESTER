# Task 02: Windows Domains

---

## Stage A: Original Task Content & Briefing Summary

Task 02 explores the fundamental architectural rationale behind Windows Domains compared to standalone workgroups.

### Core Theoretical Principles:
* **The Scalability Bottleneck of Workgroups:**
  * In a small home office or lab with 3 to 5 computers, local account management is manageable. Each machine maintains its own Security Account Manager (SAM) database (`%SystemRoot%\System32\config\SAM`).
  * If a user requires access to 5 machines, an identical username and password must be created on each machine manually.
  * In an enterprise environment with 5,000 employees and 10,000 devices across global offices, decentralized management becomes impossible. Revoking an employee's access or changing a security policy would require manual interventions on every single endpoint.
* **The Windows Domain Concept:**
  * A **Windows Domain** is a logical boundary and administrative container that groups users, computers, printers, shares, and security policies under a single centralized relational database.
  * Rather than checking a local SAM database, systems authenticate credentials against the centralized directory service.
* **The Domain Controller (DC):**
  * The dedicated Windows Server hosting the **Active Directory Domain Services (AD DS)** role.
  * Houses the directory database (`NTDS.dit`), authenticates user logon requests, enforces domain-wide security policies, and replicates state changes to other Domain Controllers in the domain.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Offensive Significance of the DC:**
  * In an enterprise penetration test, compromising a Domain Controller represents the ultimate objective (Domain Compromise / Tier 0 Takeover).
  * The DC holds the `NTDS.dit` database containing every NTLM password hash and Kerberos key for every domain account, including `krbtgt` and `Domain Admins`.
  * From an operational perspective, direct aggressive probing of a DC triggers high-severity security events in modern SIEM solutions (Event ID 4624, 4625, 4768, 4769). Stealthy attackers enumerate domain objects through unprivileged LDAP queries from standard domain-joined workstations rather than interacting aggressively with the DC console.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the section defining the central server responsible for executing Active Directory Domain Services.
* **Tier 2 Hint:** What two-word term describes the server that controls and manages authentication for the entire domain?
* **Tier 3 Hint:** The answer is `Domain Controller`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Analyzing the Domain Architecture:
1. **Directory Database (`NTDS.dit`):** Extensible Storage Engine (ESE) database located by default at `C:\Windows\NTDS\ntds.dit`.
2. **Directory Partitions:**
   * *Schema Partition:* Defines object classes and attributes allowed in the forest.
   * *Configuration Partition:* Contains forest-wide topology, sites, subnets, and directory services configuration.
   * *Domain Partition:* Stores domain-specific users, groups, computers, and OUs (replicated only within the domain).
   * *Application Partitions:* Houses dynamic data such as Active Directory-Integrated DNS (`ForestDNSZones`, `DomainDNSZones`).
3. Identifying the central server: Any Windows Server promoted to run AD DS becomes a **Domain Controller**.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the name of the server that runs Active Directory services?
  * **Answer:** `Domain Controller`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answer on TryHackMe: `Domain Controller`.
* Validated understanding of AD DS role and `NTDS.dit` database architecture.

---

## Stage G: Key Educational Takeaway

A Windows Domain centralizes security, identity, and resource management into a single administrative fabric. The Domain Controller (DC) is the authoritative engine running AD DS, making it the central pillar of enterprise IT and the primary target in domain penetration testing.
