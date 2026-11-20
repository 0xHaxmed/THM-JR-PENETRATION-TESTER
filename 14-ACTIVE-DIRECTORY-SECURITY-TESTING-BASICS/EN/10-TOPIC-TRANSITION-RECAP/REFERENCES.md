# Technical References: Module 14 Recap — Active Directory Security Testing Basics

This document compiles authoritative technical references, specifications, standards, and research papers relevant to the comprehensive Active Directory Security Testing Basics curriculum.

---

## 1. Standards & Protocol RFCs

- **RFC 4120:** *The Kerberos Network Authentication Service (V5)*  
  URL: https://datatracker.ietf.org/doc/html/rfc4120  
  *Covers Kerberos protocol specifications, ticket structures, pre-authentication mechanisms, and encryption types.*
- **RFC 4511:** *Lightweight Directory Access Protocol (v3)*  
  URL: https://datatracker.ietf.org/doc/html/rfc4511  
  *Defines LDAP search operations, filters, and attributes.*

---

## 2. Microsoft Technical Specifications (MS-DOCS)

- **[MS-DTYP]:** *Windows Data Types*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-dtyp/  
  *Defines Security Identifier (SID) structures, authority identifiers, and well-known Relative Identifiers (RIDs).*
- **[MS-SAMR]:** *Security Account Manager (SAM) Remote Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-samr/  
  *Covers RPC interfaces used for user enumeration, RID cycling, and domain database interaction.*
- **[MS-NLMP]:** *NT LAN Manager (NTLM) Authentication Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-nlmp/  
  *Details NTLM challenge-response authentication, Type 1/2/3 messages, and Pass-the-Hash mechanics.*
- **[MS-SFU]:** *Kerberos Protocol Extensions: Service for User and Constrained Delegation Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-sfu/  
  *Covers S4U2self, S4U2proxy, and Resource-Based Constrained Delegation (RBCD).*

---

## 3. Tools & Open-Source Frameworks

- **Hashcat:**  
  Website: https://hashcat.net/hashcat/  
  *Reference for Kerberos cracking modes: Mode 18200 (AS-REP), Mode 13100 (TGS-REP), Mode 5600 (Net-NTLMv2).*
- **Impacket (Fortra):**  
  GitHub: https://github.com/fortra/impacket  
  *Comprehensive network protocol toolkit: `GetNPUsers.py`, `secretsdump.py`, `psexec.py`, `getST.py`, `rbcd.py`.*
- **BloodHound Community Edition:**  
  GitHub: https://github.com/SpecterOps/BloodHound  
  *Active Directory attack path visualization and graph analysis platform.*
- **MIT Kerberos Documentation:**  
  URL: https://web.mit.edu/kerberos/krb5-latest/doc/basic/ccache_def.html  
  *Specification of the `KRB5CCNAME` environment variable and credential cache formats.*

---

## 4. MITRE ATT&CK Enterprise Matrix Mappings

| Technique ID | Technique Name | Description |
|---|---|---|
| **T1558.004** | Steal or Forge Kerberos Tickets: AS-REP Roasting | Requesting AS-REP tickets for accounts without pre-authentication and cracking them offline (Hashcat 18200). |
| **T1558.003** | Steal or Forge Kerberos Tickets: Kerberos Delegation | Abusing Constrained Delegation and RBCD via S4U extensions to mint administrator tickets. |
| **T1550.002** | Use Alternate Authentication Material: Pass the Hash | Authenticating across network services using raw NT hashes without knowing the plaintext password. |
| **T1087.002** | Account Discovery: Domain Account | Enumerating domain accounts via LDAP, RID cycling, and RPC. |
| **T1552.001** | Unsecured Credentials: Credentials in Files | Discovering hardcoded passwords in Git history, config files, and build outputs. |
| **T1090.001** | Proxy: Internal Proxy | Utilizing compromised hosts as SOCKS proxies to route attacks into isolated subnets. |
