# Task 05: Trees, Forests and Trusts

---

## Stage A: Original Task Content & Briefing Summary

Task 05 introduces multi-domain enterprise architectures, defining how domains are aggregated into **Trees** and **Forests**, and how **Trust Relationships** enable cross-boundary authentication.

### Core Architectural Hierarchy:
1. **Domain Trees:**
   * A **Tree** is a hierarchy of one or more Windows domains that share a **contiguous DNS namespace**.
   * The initial domain created in the tree is the **root domain** (e.g. `thm.loc`).
   * Additional domains added beneath it are **child domains** (subdomains), forming a contiguous hierarchy (e.g. `tbm.thm.loc`, `us.tbm.thm.loc`).
   * All domains within a single tree automatically establish bidirectional, transitive parent-child Kerberos trusts.
2. **Forests:**
   * A **Forest** is the highest logical security boundary in an Active Directory deployment.
   * A forest consists of one or more domain trees that may have completely disparate, non-contiguous namespaces (e.g. Tree 1: `thm.loc`, Tree 2: `tryhackme.com`).
   * All trees and domains within a forest share:
     - A single common **Schema** (class and attribute definitions).
     - A common **Configuration Partition** (forest topology and replication metadata).
     - A common **Global Catalog (GC)** (partial attribute set of all objects in the forest for rapid universal searching).
   * The forest root domain hosts the ultimate enterprise administrative group: **Enterprise Admins**.
3. **Trust Relationships:**
   * Logical bridges between domains that allow users in one domain to access securable resources in another domain without needing duplicate credentials.
   * **Trust Direction:**
     - The trust arrow points to the **trusted domain** (the domain containing the user identity).
     - Access flow is in the opposite direction (from the trusted domain toward the trusting domain where the resource resides).
     - *One-Way Trust:* If Domain A trusts Domain B ($A \rightarrow B$), accounts in B can access authorized resources in A, but accounts in A cannot access resources in B.
     - *Two-Way Trust:* Accounts in both domains can authenticate and access authorized resources across boundaries.
   * **Transitivity:** A transitive trust propagates automatically ($A \rightarrow B$ and $B \rightarrow C$ implies $A \rightarrow C$).

---

## Stage B: Independent Thinking & OpSec Considerations

* **Forest as the True Security Boundary:** Microsoft officially defines the **Forest**—not the domain—as the ultimate security boundary. If an attacker achieves `Domain Admin` in a child domain within a forest, they can abuse **SID Filtering** exemptions or forge inter-realm Kerberos tickets with an injected `Enterprise Admins` RID (`S-1-5-21-...-519`) in the PAC to escalate from child domain admin to full forest root control.
* **Trust Abuse in Red Teaming:** Trust relationships represent prime avenues for lateral movement. Tools like PowerView (`Get-DomainTrust`) or BloodHound automatically map trust paths, identifying where external users hold administrative memberships on target servers.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the definition of domains that share a contiguous DNS namespace.
* **Tier 2 Hint:** What botanical structure describes a hierarchical collection of domains sharing a common root namespace?
* **Tier 3 Hint:** The answer is `Tree`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Namespace Continuity in AD Hierarchy:**
   * When domains share a contiguous namespace hierarchy (such as `tbm.thm.loc` under `thm.loc`), they belong to the same **Tree**.
   * If a second domain structure with an entirely different namespace (e.g., `tryhackme.com`) joins the directory structure, it forms a separate Tree within the shared **Forest**.
2. **Verifying Terminology:**
   * Question: "What is a group of Windows domains that share the same namespace called?"
   * Correct Answer: `Tree`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is a group of Windows domains that share the same namespace called?
  * **Answer:** `Tree`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answer on TryHackMe: `Tree`.
* Reconciled Active Directory logical architecture against Microsoft Windows Server Domain and Trust architecture specifications.

---

## Stage G: Key Educational Takeaway

Active Directory scales horizontally through Trees (contiguous namespaces) and Forests (shared schema and configuration partitions). Understanding trust directions and transitivity is essential for discovering cross-domain pivot paths during enterprise engagements.
