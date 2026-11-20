# Technical References: Proxy (Challenge)

This document compiles authoritative technical references, specifications, standards, and research papers regarding Kerberos Delegation, Microsoft S4U extensions (S4U2self / S4U2proxy), Impacket tooling, and Active Directory defense.

---

## 1. Microsoft Protocol Specifications (MS-DOCS)

- **[MS-SFU]:** *Kerberos Protocol Extensions: Service for User and Constrained Delegation Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-sfu/  
  *Authoritative specification defining S4U2self (PA-FOR-USER) and S4U2proxy (additional-tickets) protocol data structures.*
- **[MS-KILE]:** *Kerberos Protocol Extensions*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-kile/  
  *Details Microsoft extensions to RFC 4120 Kerberos V5.*
- **Kerberos Constrained Delegation Overview:**  
  URL: https://learn.microsoft.com/en-us/windows-server/security/kerberos/kerberos-constrained-delegation-overview  
  *Microsoft architectural overview of delegation boundaries, Protocol Transition, and security requirements.*
- **Protected Users Security Group:**  
  URL: https://learn.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/protected-users-security-group  
  *Definitive mitigation documentation preventing account delegation.*

---

## 2. Tools & Open-Source Frameworks

- **Impacket (Fortra):**  
  GitHub: https://github.com/fortra/impacket  
  *Includes `getST.py` (Kerberos S4U ticket forge utility) and `psexec.py` (Pass-the-Ticket execution via `-k -no-pass`).*
- **GhostPack Rubeus:**  
  Author: Will Schroeder (`harmj0y`)  
  GitHub: https://github.com/GhostPack/Rubeus  
  *C# toolset for raw Kerberos interaction; commands: `Rubeus.exe s4u /user:... /impersonateuser:Administrator /msdsspn:cifs/DC.thm.loc /ptt`.*
- **NetExec (NXC):**  
  GitHub: https://github.com/Pennyw0rth/NetExec  
  *LDAP module supporting `--trusted-for-delegation` enumeration.*

---

## 3. MITRE ATT&CK Enterprise Matrix Mappings

| Technique ID | Technique Name | Description |
|---|---|---|
| **T1558.003** | Steal or Forge Kerberos Tickets: Kerberos Delegation | Abusing Kerberos delegation mechanisms (Constrained, Unconstrained, RBCD) to impersonate privileged accounts. |
| **T1078.002** | Valid Accounts: Domain Accounts | Abusing compromised service account privileges to execute S4U2self requests. |
| **T1550.003** | Use Alternate Authentication Material: Pass the Ticket | Using forged or extracted Kerberos TGS tickets (.ccache / .kirbi) to access network services. |
| **T1021.002** | Remote Services: SMB/Windows Admin Shares | Using forged Kerberos tickets with PsExec to obtain SYSTEM execution on the Domain Controller. |
