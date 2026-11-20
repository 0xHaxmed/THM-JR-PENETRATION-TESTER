# Additional Technical Notes: Forward (Challenge) — RBCD & Advanced Kerberos Abuse

---

## 1. Deep Dive: Traditional Constrained Delegation vs. RBCD

The primary architectural limitation of traditional Constrained Delegation is that it is **configured on the front-end service** using the `msDS-AllowedToDelegateTo` attribute, which requires the `SeEnableDelegationPrivilege` user right (held exclusively by Domain Admins).

Resource-Based Constrained Delegation (RBCD), introduced in Windows Server 2012, inverts this trust model:

| Comparison Metric | Traditional Constrained Delegation | Resource-Based Constrained Delegation (RBCD) |
|---|---|---|
| **Where Configured** | On the **Front-End Service Account** (`msDS-AllowedToDelegateTo`). | On the **Back-End Resource (Target Computer)** (`msDS-AllowedToActOnBehalfOfOtherIdentity`). |
| **Active Directory Attribute** | Multi-string attribute containing SPNs (`cifs/host`, etc.). | Binary Security Descriptor (DACL) specifying allowed SIDs. |
| **Privileges Required to Configure** | **Domain Administrator** (`SeEnableDelegationPrivilege`). | **Object Owner**, `GenericAll`, `GenericWrite`, or `WriteProperty` over the target computer. |
| **Protocol Transition Requirement** | Requires `TRUSTED_TO_AUTH_FOR_DELEGATION` for S4U2self. | **Any service account** can execute S4U2self without special flags! |
| **Attacker Primitive** | Compromise the service account listed in the delegation configuration. | Compromise or create **any machine account** and write to the target's DACL. |

---

## 2. Low-Level Security Descriptor in `msDS-AllowedToActOnBehalfOfOtherIdentity`

The attribute `msDS-AllowedToActOnBehalfOfOtherIdentity` is stored in LDAP as a binary Windows Security Descriptor structure:

```
[Security Descriptor Header]
├── Revision: 1
├── Control: 0x8004 (SE_DACL_PRESENT | SE_SELF_RELATIVE)
├── Owner SID: Domain Admins
├── Group SID: Domain Admins
└── Discretionary Access Control List (DACL)
    ├── ACL Revision: 2
    ├── AceCount: 1
    └── Access Control Entry (ACE)
        ├── AceType: ACCESS_ALLOWED_ACE_TYPE (0x00)
        ├── AceFlags: 0x00
        ├── Mask: 0x000F01FF (GENERIC_ALL / Full Control)
        └── Trustee SID: S-1-5-21-...-1150 (SID of our created ATTACKPC$)
```

When the KDC processes an S4U2proxy request targeting `FILESRV.ctf.local`:
1. The KDC reads `msDS-AllowedToActOnBehalfOfOtherIdentity` from `FILESRV$`'s LDAP object.
2. It parses the binary DACL and checks whether the SID of the requesting entity (`ATTACKPC$`) is granted access.
3. If an `ACCESS_ALLOWED_ACE` matches `ATTACKPC$`'s SID, the KDC issues the service ticket for `Administrator`!

---

## 3. The Machine Account Quota (MAQ) Mechanism

Active Directory includes a domain-level setting called `ms-DS-MachineAccountQuota`:
- **Default Value:** `10`
- **Location:** The root domain naming context (`DC=ctf,DC=local`).
- **Functionality:** Allows any authenticated domain user (even standard, unprivileged users) to create up to 10 computer objects in the domain without helpdesk assistance.
- **Offensive Utility:** Because an attacker needs a service account with an SPN to request S4U tickets, registering a new machine account provides an instant, fully authenticated Kerberos service principal (`sAMAccountName` ending in `$`, e.g., `ATTACKPC$`).

### Disabling MAQ via PowerShell:
```powershell
Set-ADDomain -Identity ctf.local -Replace @{'ms-DS-MachineAccountQuota'=0}
```

---

## 4. S4U Protocol Flow in Resource-Based Constrained Delegation

Unlike traditional constrained delegation, RBCD does **not** require the service account to have the `TRUSTED_TO_AUTH_FOR_DELEGATION` flag enabled in `userAccountControl`.

```
       Attacker (using ATTACKPC$)                            Domain Controller (KDC)
             |                                                          |
             | 1. S4U2self: TGS-REQ (PA-FOR-USER: "Administrator")      |
             |--------------------------------------------------------->|
             |                                                          | [Issues S4U2self ticket]
             |                                                          | [FORWARDABLE bit is NOT set!]
             | 2. TGS-REP (Ticket to ATTACKPC$ for Administrator)       |
             |<---------------------------------------------------------|
             |                                                          |
             | 3. S4U2proxy: TGS-REQ (additional-tickets: Step 2 Ticket)|
             |    Target: cifs/FILESRV.ctf.local                        |
             |--------------------------------------------------------->|
             |                                                          | [KDC checks msDS-AllowedToAct...]
             |                                                          | [Finds ATTACKPC$ SID in DACL]
             |                                                          | [KDC WAIVES FORWARDABLE requirement!]
             | 4. TGS-REP (Valid Service Ticket for Administrator!)     |
             |<---------------------------------------------------------|
```

**Crucial Technical Insight:** In traditional constrained delegation, the S4U2self ticket must have the `FORWARDABLE` flag set (which requires Protocol Transition). In RBCD, when the target resource's `msDS-AllowedToActOnBehalfOfOtherIdentity` permits delegation from the client, the KDC **explicitly waives the requirement for the FORWARDABLE flag**, allowing any machine account to impersonate any user!

---

## 5. Forensic Telemetry & SIEM Detection

| Stage | Event ID | Channel | Anomaly Pattern |
|---|---|---|---|
| **Computer Account Creation** | **4741** | Security | `SubjectUserName` is a standard domain user; `TargetUserName` ends in `$`. |
| **RBCD DACL Modification** | **4662** | Security | Operation performed on computer object; Property `msDS-AllowedToActOnBehalfOfOtherIdentity` written. |
| **S4U2self Request** | **4769** | Security | `TargetUserName: Administrator`, `ServiceName: ATTACKPC$`. |
| **S4U2proxy Request** | **4769** | Security | `TargetUserName: Administrator`, `ServiceName: cifs/FILESRV.ctf.local`. |
