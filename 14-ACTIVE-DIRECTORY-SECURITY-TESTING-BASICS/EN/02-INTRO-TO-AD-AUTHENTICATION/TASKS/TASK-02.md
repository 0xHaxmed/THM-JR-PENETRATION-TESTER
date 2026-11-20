# Task 02: Authentication in AD

---

## Stage A: Original Task Content & Briefing Summary

Task 02 establishes the vital architectural and operational boundary between **Authentication** and **Authorisation** within Windows Active Directory environments.

### Core Conceptual Definitions:
* **Authentication Material:**
  * To interact with enterprise resources, a security principal must present cryptographic proof of identity.
  * In Active Directory, this material takes three primary forms:
    1. *Cleartext Credentials:* Username and password entered during interactive logon.
    2. *Cryptographic Certificates:* Public/private key pairs issued by an enterprise Certificate Authority (Active Directory Certificate Services - AD CS) for smart card or machine authentication.
    3. *Password Hashes / Kerberos Keys:* The mathematical derivatives of the user's password used directly by the underlying network protocols.
* **Authentication vs. Authorisation:**
  * **Authentication:** The process of verifying identity. Answers the fundamental question: *"Are you who you claim to be?"* (e.g. Validating that the user presenting themselves is indeed `claire`). Authentication strictly precedes authorisation.
  * **Authorisation:** The process of determining rights and privileges. Answers the question: *"What are you allowed to do or access?"* (e.g. Evaluating Discretionary Access Control Lists - DACLs, security group memberships, and file permissions to decide if `claire` can read `SHARE1`).
* **Underlying Protocols:**
  * Higher-level application protocols (SMB, LDAP, HTTP, RPC, WinRM) do not implement custom authentication routines. Instead, they leverage the Security Support Provider Interface (SSPI) to negotiate either **NetNTLM** or **Kerberos**.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Separation of Concerns in Security Testing:**
  * In penetration testing, breaking **Authentication** means impersonating or compromising an identity (e.g. cracking an NTLM hash, forging a ticket, or performing Pass-the-Hash).
  * Breaking **Authorisation** means exploiting over-permissive ACLs, group nestings, or token privileges to access resources that the authenticated identity was never intended to reach.
  * An attacker can have valid authentication (as a standard domain user) while exploiting authorization flaws to achieve Domain Admin rights.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the definitions provided in the briefing distinguishing identity verification from access control.
* **Tier 2 Hint:** What term describes proving *who* you are? What term describes determining *what* you can do?
* **Tier 3 Hint:** The answers are `Authentication` and `Authorisation` (using the British English spelling with 's').

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Analyzing Identity Verification:**
   * Proving identity to a security authority is defined as **Authentication**.
2. **Analyzing Access Rights Determination:**
   * Evaluating permissions, group memberships, and object DACLs to regulate access is defined as **Authorisation**.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the process called that proves your identity?
  * **Answer:** `Authentication`
* **Question 2:** What is the process called that determines what you are allowed to access?
  * **Answer:** `Authorisation`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `Authentication` and `Authorisation`.
* Validated conceptual definitions against Microsoft Windows Security Architecture and NIST SP 800-63 (Digital Identity Guidelines).

---

## Stage G: Key Educational Takeaway

Authentication establishes identity ("Who are you?"), while Authorisation enforces permissions ("What can you access?"). In Active Directory, all higher-level service access flows depend on this two-stage sequence.
