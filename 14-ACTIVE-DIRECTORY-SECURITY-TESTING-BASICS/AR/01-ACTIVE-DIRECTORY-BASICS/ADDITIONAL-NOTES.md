# Additional Technical Notes: Active Directory Basics

---

## 1. Deep Dive: Kerberos Packet Structure & Encryption Architecture

Kerberos authentication relies on symmetric cryptography and mutual trust anchored in the Domain Controller's **Key Distribution Center (KDC)**.

### The 3 Core Message Exchanges:
```
[Client]                                  [KDC: Port 88]                         [Application Server]
   |                                            |                                         |
   | -------- 1. AS-REQ (Pre-Auth Timestamp) -> |                                         |
   | <------- 2. AS-REP (TGT + Session Key) --- |                                         |
   |                                            |                                         |
   | -------- 3. TGS-REQ (TGT + Target SPN) --> |                                         |
   | <------- 4. TGS-REP (TGS + Svc Session) -- |                                         |
   |                                            |                                         |
   | ---------------------------------- 5. AP-REQ (TGS Ticket + Authenticator) ---------> |
   | <--------------------------------- 6. AP-REP (Mutual Authentication) -------------- |
```

### Key Kerberos Ticket Fields:
1. **Ticket Granting Ticket (TGT):**
   * Encrypted using the `krbtgt` account's Long-Term Secret Key (AES-256 / RC4 hash).
   * Contains: Client Username, Domain, Client IP, Validity Lifetime, Session Key, and the **Privilege Attribute Certificate (PAC)**.
2. **The PAC (Privilege Attribute Certificate):**
   * Microsoft's proprietary extension to RFC 4120 Kerberos.
   * Encapsulates the user's User ID (RID) and all Group Memberships (Group SIDs).
   * Signed twice: once with the Server Key, and once with the KDC Key (`krbtgt`). This prevents clients from modifying their group memberships inside the ticket.
   * If an attacker compromises the `krbtgt` hash, they can forge a **Golden Ticket** with arbitrary group SIDs injected into the PAC (e.g. `Domain Admins`, `Enterprise Admins`).
3. **Ticket Granting Service (TGS) Ticket:**
   * Encrypted with the secret key of the target service owner account (the service account mapped to the SPN).
   * Because any domain user can request a TGS for any registered SPN, extracting this ticket allows offline password cracking without contacting the DC again (**Kerberoasting**).

---

## 2. Active Directory Physical vs. Logical Architecture

| Dimension | Logical Architecture | Physical Architecture |
| :--- | :--- | :--- |
| **Primary Component** | Domains, Trees, Forests, OUs | Domain Controllers, Sites, Subnets |
| **Purpose** | Identity governance, security boundaries, GPO targeting | Network traffic optimization, replication topology |
| **Replication Scope** | Logical partitions (`Schema`, `Config`, `Domain`) | IP subnets, inter-site transport (RPC over IP / SMTP) |
| **Database File** | Directory Schema, Objects, SIDs | `C:\Windows\NTDS\ntds.dit` (ESE database) |
| **DNS Dependency** | Contiguous vs. Non-contiguous DNS namespaces | SRV records (`_ldap._tcp`, `_kerberos._tcp`) |

---

## 3. Trust Relationships: Security Boundaries & Attack Vectors

### Forest vs. Domain Boundaries:
* **The Domain is NOT a Security Boundary:**
  * Prior to Windows Server 2003, administrators assumed domains were security boundaries.
  * Microsoft clarified that the **Forest** is the only true security boundary.
  * An administrator of a child domain (`tbm.thm.loc`) can manipulate the physical domain controller and forge Kerberos requests with `SID History` pointing to `Enterprise Admins` in the forest root domain (`thm.loc`) unless **SID Filtering** (`/filter-sids:yes`) is explicitly enabled.

### Cross-Forest Trusts:
* When a cross-forest trust is established between `thm.loc` and `tvm.loc`:
  * **Forest-wide authentication:** All users in the trusted forest are automatically allowed to authenticate to all resources in the trusting forest (resource DACLs still decide access).
  * **Selective authentication:** Users in the trusted forest cannot authenticate to any server in the trusting forest unless explicitly granted the "Allowed to Authenticate" permission on that specific server object.

---

## 4. Group Policy Replication Mechanics (SYSVOL Internals)

* **Physical Location:** `C:\Windows\SYSVOL\sysvol\<DomainDNSName>\Policies\{GUID}`
* **Replication Engine:** Distributed File System Replication (DFSR).
* **Directory Contents:**
  * `GPT.INI`: Stores GPO version number.
  * `Machine\`: Contains Computer Configuration policies, registry adjustments (`registry.pol`), and startup/shutdown scripts.
  * `User\`: Contains User Configuration policies and logon/logoff scripts.
* **Security Implications:**
  * Any user on the domain can read files in `SYSVOL`.
  * Historically, Group Policy Preferences stored passwords in XML files (e.g. `Groups.xml`) encrypted with a publicly known AES-256 key published on MSDN (`cpassword`). Searching `SYSVOL` for XML files is a standard phase of domain enumeration.
