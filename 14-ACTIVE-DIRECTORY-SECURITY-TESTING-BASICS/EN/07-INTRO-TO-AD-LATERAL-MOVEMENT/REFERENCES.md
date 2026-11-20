# Technical References: Intro to AD Lateral Movement

This document compiles authoritative technical references, specifications, standards, and research papers regarding Windows lateral movement techniques, RPC protocols, WinRM remoting, Pass-the-Hash cryptography, SOCKS tunneling, and defensive mitigations.

---

## 1. Microsoft Protocol Specifications (MS-DOCS)

- **[MS-SCMR]:** *Service Control Manager Remote Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-scmr/  
  *Defines RPC interfaces used by PsExec (`CreateServiceW`, `StartServiceW`, `DeleteServiceW`).*
- **[MS-WSMV]:** *Web Services Management Protocol Extensions for Windows Vista*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-wsmv/  
  *Details WS-Management and WinRM protocol implementations.*
- **[MS-PSRP]:** *PowerShell Remoting Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-psrp/  
  *Defines packet formats and pipeline execution mechanisms for remote PowerShell sessions.*
- **[MS-NLMP]:** *NT LAN Manager (NTLM) Authentication Protocol*  
  URL: https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-nlmp/  
  *Cryptographic algorithms underlying NTLM challenge-response and Pass-the-Hash.*
- **Windows Local Administrator Password Solution (LAPS) Architecture:**  
  URL: https://learn.microsoft.com/en-us/windows-server/identity/laps/laps-overview  
  *Official technical guidance on Windows LAPS deployment, encryption, and ACL management.*

---

## 2. Tools & Open-Source Frameworks

- **Impacket (Fortra):**  
  GitHub: https://github.com/fortra/impacket  
  *Core suite for network protocol interaction (`psexec.py`, `wmiexec.py`, `smbexec.py`).*
- **Evil-WinRM:**  
  Author: CyberVaca  
  GitHub: https://github.com/Hackplayers/evil-winrm  
  *The ultimate WinRM post-exploitation shell for penetration testing.*
- **NetExec (NXC):**  
  GitHub: https://github.com/Pennyw0rth/NetExec  
  *The multi-protocol network exploitation tool succeeding CrackMapExec.*
- **ProxyChains-NG:**  
  GitHub: https://github.com/rofl0r/proxychains-ng  
  *Hook-based proxy redirector for Linux environments.*
- **OpenSSH:**  
  Website: https://www.openssh.com/  
  *Documentation on SSH dynamic SOCKS forwarding (`-D`) and local port forwarding (`-L`).*

---

## 3. MITRE ATT&CK Enterprise Matrix Mappings

| Technique ID | Technique Name | Description |
|---|---|---|
| **T1021.002** | Remote Services: SMB/Windows Admin Shares | Using SMB and administrative shares (`ADMIN$`, `C$`) with PsExec to execute services. |
| **T1021.006** | Remote Services: Windows Remote Management (WinRM) | Executing commands remotely over port 5985/5986 via PowerShell remoting and Evil-WinRM. |
| **T1550.002** | Use Alternate Authentication Material: Pass the Hash | Authenticating across network services using NTLM hashes without knowing the plaintext password. |
| **T1090.001** | Proxy: Internal Proxy | Utilizing compromised systems as local or dynamic proxies (SSH SOCKS) to redirect network traffic. |
| **T1572** | Protocol Tunneling | Tunneling arbitrary TCP protocols inside encrypted SSH tunnels. |
| **T1078.002** | Valid Accounts: Domain Accounts | Reusing compromised domain credentials across internal servers and workstations. |
| **T1543.003** | Create or Modify System Process: Windows Service | Creating a new service (Event ID 7045) to execute payloads under `NT AUTHORITY\SYSTEM`. |
