# Task 07: Mitigations

---

## Stage A: Original Task Content & Briefing Summary

Task 07 examines the comprehensive defensive architecture, Group Policy Object (GPO) baselines, and architectural controls designed to mitigate the Active Directory breaching vectors demonstrated throughout the room.

### 1. Defensive Hardening by Attack Vector:
* **Secrets Management in Code & CI/CD:**
  * Implement automated pre-commit scanning (using tools like TruffleHog, Gitleaks, or GitHub Secret Scanning) to prevent secrets from entering repositories.
  * Centralize credentials in enterprise key vaults (HashiCorp Vault, CyberArk, Azure Key Vault) rather than hardcoding them in scripts.
* **Password Policy & Lockout Engineering:**
  * Enforce password lengths $\ge 14$ characters and implement automated banned-password lists (Azure AD Password Protection on-premises).
  * Require distinct, randomly generated onboarding passwords for new employees.
  * Configure rational lockout policies (e.g. 5 failed attempts over a 30-minute window) combined with SIEM alerts on distributed failed logons across multiple accounts from single IP sources.
* **Hardening Embedded Devices (Printers/Scanners):**
  * Change default vendor administrative passwords immediately upon deployment.
  * Enforce encrypted **LDAPS (TCP port 636)** rather than plaintext LDAP (TCP port 389) to prevent cleartext credential transmission during directory binds.
  * Place embedded devices in an isolated management VLAN inaccessible to general workstations.
* **File Share Security & Coercion Prevention:**
  * Enforce strict write permissions on network shares; standard users should not have write privileges on folders accessible by executives or administrators.
  * Block or alert on file uploads containing executable or shortcut extensions (`.url`, `.lnk`, `.scf`, `desktop.ini`).
* **NTLM Protocol Hardening:**
  * Enforce the Group Policy setting **`Network Security: LAN Manager authentication level`** to "Send NTLMv2 response only. Refuse LM & NTLM".
  * Mandate SMB Signing across all endpoints and Domain Controllers to defeat NTLM relaying.
  * Block outbound TCP port 445 (SMB) at the corporate perimeter firewall to prevent coercion attacks from leaking NetNTLM hashes to external internet hosts.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Defensive Trade-Offs in Enterprise Environments:**
  * Disabling NTLM entirely is often constrained by legacy enterprise line-of-business applications. In such cases, enforcing NTLMv2, requiring SMB signing, and restricting NTLM via GPO (`Network security: Restrict NTLM`) provides defense-in-depth while maintaining operational uptime.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the NTLM Hardening section to identify the exact Group Policy setting name for Question 1. Look at the secure port number for encrypted LDAP for Question 2.
* **Tier 2 Hint:** What is the full string under Computer Configuration for LAN Manager authentication? What is the standard LDAPS port over TLS?
* **Tier 3 Hint:** The GPO setting is `Network Security: LAN Manager authentication level`. The LDAPS port is `636`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Identifying the LAN Manager GPO Setting:**
   * Policy Path: `Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Local Policies -> Security Options`.
   * Setting Name: **`Network Security: LAN Manager authentication level`**.
   * Setting this value to Level 5 ("Send NTLMv2 response only. Refuse LM & NTLM") prevents legacy LM/NTLM downgrade attacks.
2. **Identifying the Encrypted LDAP Port:**
   * Plaintext LDAP operates over TCP port 389.
   * Secure LDAP over TLS/SSL (**LDAPS**) operates over TCP port **`636`** (or port 3269 for the Global Catalog over SSL).

---

## Stage E: Final Answer & Key Values

* **Question 1:** What Group Policy setting can be configured to enforce NTLMv2 and refuse older LM and NTLM responses?
  * **Answer:** `Network Security: LAN Manager authentication level`
* **Question 2:** What port should be used instead of port 389 to ensure LDAP traffic is encrypted?
  * **Answer:** `636`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `Network Security: LAN Manager authentication level` and `636`.
* Reconciled GPO settings against Microsoft Windows Server Security Baseline documentation.

---

## Stage G: Key Educational Takeaway

Active Directory defense requires a defense-in-depth strategy: encrypting directory traffic with LDAPS on port 636, eliminating legacy NTLM levels via Group Policy, restricting write permissions on SMB shares, and scanning code pipelines for leaked secrets.
