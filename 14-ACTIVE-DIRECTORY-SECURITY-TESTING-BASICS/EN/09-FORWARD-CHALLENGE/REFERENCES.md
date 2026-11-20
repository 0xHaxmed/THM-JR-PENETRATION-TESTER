# Technical References: Forward (Challenge)

This document compiles authoritative technical references, specifications, standards, and research papers regarding Resource-Based Constrained Delegation (RBCD), Machine Account Quota abuse, Impacket tooling, and Active Directory defense.

---

## 1. Microsoft Protocol Specifications (MS-DOCS)

- **[MS-SFU]:** *Kerberos Protocol Extensions: Service for User and Constrained Delegation Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-sfu/  
  *Details the S4U protocol extensions, S4U2self, S4U2proxy, and RBCD enforcement.*
- **[MS-ADTS]:** *Active Directory Technical Specification*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/  
  *Covers the `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute and `ms-DS-MachineAccountQuota` definition.*
- **Kerberos Resource-Based Constrained Delegation Overview:**  
  URL: https://learn.microsoft.com/en-us/windows-server/security/kerberos/kerberos-constrained-delegation-overview#resource-based-constrained-delegation  
  *Architectural specification of decentralized resource-based delegation.*
- **Active Directory MachineAccountQuota Hardening:**  
  URL: https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory-domain-services/default-workstation-machine-account-quota  
  *Guidelines on restricting machine account creation to administrators.*

---

## 2. Tools & Open-Source Frameworks

- **Impacket (Fortra):**  
  GitHub: https://github.com/fortra/impacket  
  *Includes `addcomputer.py` (MAQ exploitation), `rbcd.py` (Security descriptor modification), and `getST.py` (S4U Kerberos ticket forge).*
- **Powermad (Kevin Robertson):**  
  GitHub: https://github.com/Kevin-Robertson/Powermad  
  *PowerShell machine account creation utility.*
- **PowerView / PowerSploit:**  
  GitHub: https://github.com/PowerShellMafia/PowerSploit  
  *Cmdlets: `Get-DomainComputer`, `Set-DomainObjectOwner`, `Add-DomainObjectAcl`.*
- **GhostPack Rubeus:**  
  GitHub: https://github.com/GhostPack/Rubeus  
  *Commands: `Rubeus.exe s4u /user:ATTACKPC$ /rc4:... /impersonateuser:Administrator /msdsspn:cifs/TARGET /ptt`.*

---

## 3. MITRE ATT&CK Enterprise Matrix Mappings

| Technique ID | Technique Name | Description |
|---|---|---|
| **T1558.003** | Steal or Forge Kerberos Tickets: Kerberos Delegation | Abusing Resource-Based Constrained Delegation (RBCD) via S4U2self/proxy to forge administrator tickets. |
| **T1078.002** | Valid Accounts: Domain Accounts | Utilizing unprivileged domain accounts to register machine accounts and authenticate across services. |
| **T1098** | Account Manipulation | Modifying the `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute on target computer objects. |
| **T1136.002** | Create Account: Domain Account | Exploiting `ms-DS-MachineAccountQuota` to create rogue domain computer accounts. |
| **T1003.001** | OS Credential Dumping: LSASS Memory | Dumping LSASS memory on the intermediate server to harvest Domain Administrator sessions. |
| **T1550.002** | Use Alternate Authentication Material: Pass the Hash | Using the harvested Domain Administrator hash to compromise the Domain Controller. |
