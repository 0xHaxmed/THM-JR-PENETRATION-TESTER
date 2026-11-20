# Task 03: Active Directory

---

## Stage A: Original Task Content & Briefing Summary

Task 03 examines the internal structure of Active Directory Domain Services, detailing security principals, default security groups, machine accounts, and the vital operational distinction between Organizational Units (OUs) and Security Groups.

### Lab Connectivity & Access:
* **Host:** `ROOTDC.THM.LOC` (`192.168.10.100`)
* **Credentials:** `THM\Administrator` : `learningadisfun1!`
* **Access Command:**
  ```bash
  xfreerdp /u:Administrator /p:'learningadisfun1!' /d:THM.LOC /v:192.168.10.100 /dynamic-resolution
  ```

### Active Directory Security Principals:
* **Security Principal:** Any entity that can be authenticated by the operating system and assigned permissions across network resources. Every security principal possesses a unique **Security Identifier (SID)**.
* **User Accounts:** Represent individual human employees or programmatic service accounts.
* **Machine Accounts (Computer Accounts):**
  * Created automatically when a computer joins a domain.
  * Identified by a trailing dollar sign appended to the NetBIOS hostname (e.g. `TOM-PC$`, `ROOTDC$`, `SERVER1$`).
  * Machine accounts have complex 120-character random passwords rotated automatically every 30 days by Windows.
  * In offensive security, compromising a machine account allows domain-authenticated LDAP/RPC queries and potential delegation abuses.
* **Security Groups:**
  * Collections of users, computers, or nested groups used to assign permissions (Discretionary Access Control Lists - DACLs).
  * *Domain Admins:* Members possess full administrative rights over every computer, server, and resource in the domain.
  * *Server Operators:* Can manage Domain Controllers, backup files, and restart services without modifying administrative group memberships.
  * *Backup Operators:* Can bypass file system DACLs to read and write any file on the system.
  * *Account Operators:* Can create and modify domain accounts.

### Organizational Units (OUs) vs. Security Groups:
* **Organizational Units (OUs):** Hierarchical administrative containers used to organize objects and **apply Group Policy Objects (GPOs)**. An object can only reside within a single OU at any given moment.
* **Security Groups:** Flat collections of security principals used exclusively to **grant access permissions** to files, shares, and services. An object can belong to dozens of security groups simultaneously.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Machine Accounts as Stealth Footholds:** In an adversarial engagement, executing as `NT AUTHORITY\SYSTEM` on a domain-joined workstation allows an attacker to interact with the domain controller using that workstation's machine account (`HOSTNAME$`). This provides valid domain credentials without needing to crack user passwords, allowing initial domain enumeration (BloodHound / LDAP queries).
* **Targeting High-Privilege Groups:** `Domain Admins` is the holy grail of domain attacks. Mapping user sessions (`Find-DomainUserLocation` or BloodHound session collection) allows attackers to identify where Domain Admins are logged in to dump their credentials via LSASS.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Review the descriptions of built-in security groups for Question 1. Look at the naming convention for computer accounts for Question 2.
* **Tier 2 Hint:** What group holds full administrative rights across all domain endpoints? For machine accounts, what character is always appended to the machine name?
* **Tier 3 Hint:** The group is `Domain Admins`. The machine account name for `TOM-PC` is `TOM-PC$`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Investigating Built-in Groups:**
   * Open Active Directory Users and Computers (`dsa.msc`).
   * Navigate to the `Users` container.
   * Locate `Domain Admins` (SID: `S-1-5-21-<domain>-512`). This group is automatically added to the local `Administrators` group of every machine that joins the domain.
2. **Machine Account Formatting:**
   * Machine accounts are distinguished from human user accounts by their `sAMAccountName` attribute, which strictly ends with `$`.
   * For a workstation named `TOM-PC`, its corresponding security principal in AD is `TOM-PC$`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Which group normally administers all computers and resources in a domain?
  * **Answer:** `Domain Admins`
* **Question 2:** What would be the name of the machine account associated with a machine named TOM-PC?
  * **Answer:** `TOM-PC$`

---

## Stage F: Practical Verification & Proof of Work

* Verified answers on TryHackMe: `Domain Admins` and `TOM-PC$`.
* RDP connection established to `ROOTDC.THM.LOC` (`192.168.10.100`), verifying the Active Directory Users and Computers hierarchy.

---

## Stage G: Key Educational Takeaway

Active Directory distinguishes between administrative containers (OUs for GPO deployment) and permission containers (Security Groups for resource access). Domain Admins represents Tier 0 administrative supremacy, and machine accounts (`NAME$`) are fully functional domain security principals.
