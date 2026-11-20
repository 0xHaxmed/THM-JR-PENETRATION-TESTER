# Task 02: Active Directory Breaches

---

## Stage A: Original Task Content & Briefing Summary

Task 02 breaks down the conceptual and architectural framework of Active Directory Breaches, defining the initial access attack surface across core enterprise protocols.

### Core Attack Surface Breakdown:
* **The Strategic Value of the Initial Foothold:**
  * In Active Directory, any single valid user account—even an unprivileged contractor or temp employee—possesses the default right to bind to LDAP (TCP 389/636) and query the complete directory tree (`CN=Users`, `CN=Computers`, `OU=Departments`).
  * A single domain credential allows querying domain trust relationships, reading GPOs, discovering service accounts with registered SPNs (for Kerberoasting), and mapping high-value paths with BloodHound.
* **The Five Critical Network Services Exposed in AD:**
  1. **SMB (TCP 445):** Shares, IPC pipes, remote administration, and the primary target for password spraying and NTLM relaying.
  2. **LDAP / LDAPS (TCP 389 / 636):** Directory querying, user/group enumeration, and LDAP passback attacks.
  3. **HTTP / HTTPS (TCP 80 / 443):** Web portals, developer tools (Gitea, GitLab), CI/CD servers (Jenkins), and embedded device interfaces leaking credentials.
  4. **Kerberos (TCP/UDP 88):** Authentication and pre-authentication user validation without triggering account lockouts.
  5. **DNS (TCP/UDP 53):** Identifying domain controllers (`_ldap._tcp`), KDCs (`_kerberos._tcp`), and global catalogs via SRV records.
* **The First Phase:**
  * In the Active Directory attack methodology (Breaching -> Enumeration -> Credential Harvesting -> Lateral Movement -> Domain Dominance), the initial phase is strictly **Breaching**.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Protocol Abuse vs. Software Exploitation:**
  * Active Directory breaching rarely requires deploying memory corruption exploits (buffer overflows or zero-days).
  * Rather, breaching leverages inherent protocol design behaviors: Kerberos pre-authentication error codes, NTLM challenge-response handshakes, and developer oversights (hardcoded passwords in repositories).
  * This protocol-level abuse means attacks blend in with legitimate network traffic unless specifically detected by targeted telemetry rules.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the section defining the very first stage of any Active Directory attack chain.
* **Tier 2 Hint:** What term describes obtaining the initial foothold? What protocol listens on port 88 and can be abused to validate usernames?
* **Tier 3 Hint:** The first phase is `Breaching`. The port 88 service is `Kerberos`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Identifying the First Attack Phase:**
   * Gaining initial access to an unauthenticated Active Directory domain is formally known as **Breaching**.
2. **Identifying the Port 88 Enumeration Service:**
   * The **Kerberos** service operates on TCP/UDP port 88. When an Authentication Service request (`AS-REQ`) is submitted for an invalid user, the KDC returns `KDC_ERR_C_PRINCIPAL_UNKNOWN`. When submitted for a valid user, it returns `KDC_ERR_PREAUTH_REQUIRED`, allowing precise username validation without invalid password attempts.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the first phase of any AD attack chain?
  * **Answer:** `Breaching`
* **Question 2:** What service, running on TCP port 88, can be abused to validate whether usernames exist in the domain?
  * **Answer:** `Kerberos`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `Breaching` and `Kerberos`.
* Validated service definitions against standard Active Directory attack lifecycles.

---

## Stage G: Key Educational Takeaway

Active Directory breaching is the foundational phase of enterprise compromise. Exploiting exposed services—particularly Kerberos on port 88—enables silent reconnaissance and credential discovery without triggering traditional account lockout defenses.
