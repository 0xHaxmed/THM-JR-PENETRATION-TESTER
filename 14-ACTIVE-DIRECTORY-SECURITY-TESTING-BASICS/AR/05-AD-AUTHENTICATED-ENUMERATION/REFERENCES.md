# Technical References: AD: Authenticated Enumeration

This document compiles the authoritative technical documentation, specifications, standards, and references relevant to authenticated Active Directory reconnaissance, AS-REP Roasting, Living-off-the-Land enumeration, and graph analysis.

---

## 1. Standards & Protocol RFCs

- **RFC 4120:** *The Kerberos Network Authentication Service (V5)*  
  URL: https://datatracker.ietf.org/doc/html/rfc4120  
  *Covers Kerberos protocol specifications, AS-REQ, AS-REP, pre-authentication padata types, and encryption mechanisms.*
- **RFC 4511:** *Lightweight Directory Access Protocol (v3)*  
  URL: https://datatracker.ietf.org/doc/html/rfc4511  
  *Defines LDAP search operations, filters, and attributes.*

---

## 2. Microsoft Technical Specifications (MS-DOCS)

- **[MS-KILE]:** *Kerberos Protocol Extensions*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-kile/  
  *Details Microsoft-specific extensions to Kerberos, PAC structure, and authorization data.*
- **[MS-SAMR]:** *Security Account Manager (SAM) Remote Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-samr/  
  *Defines RPC interfaces used by `net user /domain` and `net group /domain` to query domain user and group databases.*
- **UserAccountControl Flags Reference:**  
  URL: https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory-domain-services/useraccountcontrol-manipulate-account-properties  
  *Exhaustive list of all bitmask flags including `DONT_REQ_PREAUTH` (0x00400000).*
- **Active Directory PowerShell Module Documentation:**  
  URL: https://learn.microsoft.com/en-us/powershell/module/activedirectory/  
  *Reference for `Get-ADUser`, `Get-ADComputer`, `Get-ADGroup`, and `Get-ADGroupMember`.*

---

## 3. Open-Source Tools & Frameworks

- **BloodHound Community Edition (SpecterOps):**  
  GitHub: https://github.com/SpecterOps/BloodHound  
  *Next-generation Active Directory graph analysis platform.*
- **bloodhound-python:**  
  GitHub: https://github.com/dirkjanm/BloodHound.py  
  *Python-based ingestor for BloodHound by Dirk-jan Mollema.*
- **PowerSploit / PowerView:**  
  GitHub: https://github.com/PowerShellMafia/PowerSploit  
  *PowerShell post-exploitation framework and Active Directory reconnaissance module by Will Schroeder (@harmj0y).*
- **Impacket:**  
  GitHub: https://github.com/fortra/impacket  
  *Collection of Python classes for working with network protocols, including `GetNPUsers.py`.*
- **GhostPack Rubeus:**  
  GitHub: https://github.com/GhostPack/Rubeus  
  *C# toolset for raw Kerberos interaction and abuses.*
- **Hashcat:**  
  Website: https://hashcat.net/hashcat/  
  *Hashcat documentation for Hash-Mode 18200 (Kerberos 5, etype 23, AS-REP).*

---

## 4. MITRE ATT&CK Enterprise Matrix Mappings

| Technique ID | Technique Name | Description |
|---|---|---|
| **T1558.004** | Steal or Forge Kerberos Tickets: AS-REP Roasting | Requesting AS-REP tickets for accounts without pre-auth and cracking them offline. |
| **T1087.002** | Account Discovery: Domain Account | Enumerating domain accounts using `net user /domain`, `Get-ADUser`, or LDAP. |
| **T1069.002** | Permission Groups Discovery: Domain Groups | Enumerating domain groups using `net group /domain`, `Get-ADGroup`, or PowerView. |
| **T1018** | Remote System Discovery | Discovering domain computers and DCs via `Get-ADComputer` or BloodHound. |
| **T1082** | System Information Discovery | Gathering OS build, domain affiliation, and system architecture via `systeminfo`. |
| **T1033** | System Owner/User Discovery | Identifying current user context and privileges using `whoami /all`. |
| **T1059.001** | Command and Scripting Interpreter: PowerShell | Executing PowerShell scripts (`PowerView.ps1`) for situational awareness. |
