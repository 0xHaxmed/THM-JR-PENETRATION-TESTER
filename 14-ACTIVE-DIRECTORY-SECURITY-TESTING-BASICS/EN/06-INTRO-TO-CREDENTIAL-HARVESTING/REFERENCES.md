# Technical References: Intro to Credential Harvesting

This document compiles authoritative technical references, specifications, standards, and research papers regarding Windows credential storage, LSASS architecture, DPAPI, LSA Secrets, DCSync replication protocols, and Pass-the-Hash exploitation.

---

## 1. Microsoft Protocol Specifications (MS-DOCS)

- **[MS-DRSR]:** *Directory Replication Service (DRS) Remote Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-drsr/  
  *Defines the RPC interfaces (`IDL_DRSGetNCChanges`) leveraged by DCSync.*
- **[MS-SAMR]:** *Security Account Manager (SAM) Remote Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-samr/  
  *Covers remote management and enumeration of local SAM databases.*
- **[MS-NLMP]:** *NT LAN Manager (NTLM) Authentication Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-nlmp/  
  *Details NTLM challenge-response authentication, Type 1/2/3 messages, and cryptographic calculations underlying Pass-the-Hash.*
- **Windows Data Protection API (DPAPI) Architecture:**  
  URL: https://learn.microsoft.com/en-us/previous-versions/ms995355(v=msdn.10)  
  *Cryptographic design of MasterKeys, user encryption profiles, and Windows Vault storage.*
- **Configuring Additional LSA Protection (RunAsPPL):**  
  URL: https://learn.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/configuring-additional-lsa-protection  
  *Hardening LSASS with Protected Process Light.*

---

## 2. Tools, Frameworks & Cryptographic Engines

- **Mimikatz:**  
  Author: Benjamin Delpy (`gentilkiwi`)  
  GitHub: https://github.com/gentilkiwi/mimikatz  
  *The foundational Windows post-exploitation credential harvesting framework.*
- **Impacket:**  
  Maintainer: Fortra  
  GitHub: https://github.com/fortra/impacket  
  *Includes `secretsdump.py` (SAM, LSA, NTDS, DCSync) and `psexec.py` (Pass-the-Hash execution).*
- **John the Ripper (JtR):**  
  GitHub: https://github.com/openwall/john  
  *Community-enhanced password cracker; native support for format `mscash2` (DCC2).*
- **Hashcat:**  
  Website: https://hashcat.net/hashcat/  
  *GPU-accelerated password recovery; Mode `2100` (Domain Cached Credentials 2 / mscash2).*

---

## 3. MITRE ATT&CK Enterprise Matrix Mappings

| Technique ID | Technique Name | Description |
|---|---|---|
| **T1003.001** | OS Credential Dumping: LSASS Memory | Dumping memory from `lsass.exe` to harvest plaintext credentials, NTLM hashes, and Kerberos tickets. |
| **T1003.002** | OS Credential Dumping: Security Account Manager (SAM) | Extracting local account hashes from the SAM registry hive using BootKey from SYSTEM hive. |
| **T1003.003** | OS Credential Dumping: NTDS | Extracting domain password hashes from the `ntds.dit` file on Domain Controllers. |
| **T1003.005** | OS Credential Dumping: Cached Domain Credentials | Extracting MS-Cache v2 (DCC2) hashes from the LSA Secrets hive for offline cracking. |
| **T1003.006** | OS Credential Dumping: DCSync | Simulating a Domain Controller via DRSUAPI to replicate directory secrets. |
| **T1555.004** | Credentials from Password Stores: Windows Credential Manager | Decrypting DPAPI vaults and saved web passwords via `vault::cred /export`. |
| **T1550.002** | Use Alternate Authentication Material: Pass the Hash | Authenticating across network services using NTLM hashes without knowing the plaintext password. |
