# References & Authoritative Sources: AD: Basic Enumeration

---

## 1. Official TryHackMe Documentation
* [TryHackMe AD: Basic Enumeration](https://tryhackme.com/room/adbasicenumeration)
* [TryHackMe Junior Penetration Tester Learning Path](https://tryhackme.com/path/outline/jrpenetrationtester)
* [TryHackMe AD: Authenticated Enumeration](https://tryhackme.com/room/adauthenticatedenumeration)

---

## 2. Microsoft Technical Specifications & Guides
* **MS-SAMR: Security Account Manager Remote Protocol:**
  [https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-samr/](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-samr/)
* **MS-ADTS: Active Directory Technical Specification (LDAP Syntax & Filters):**
  [https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/)
* **Restricting Anonymous Network Access in Windows Server:**
  [https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/network-access-restrict-anonymous-access-to-named-pipes-and-shares](https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/network-access-restrict-anonymous-access-to-named-pipes-and-shares)

---

## 3. Authoritative Tooling & Research Repositories
* **Samba Client Suite (rpcclient, smbclient):**
  [https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html)
* **SMBMap by Shawn Evans:**
  [https://github.com/ShawnDEvans/smbmap](https://github.com/ShawnDEvans/smbmap) — A handy SMB enumeration tool.
* **NetExec (nxc) & CrackMapExec:**
  [https://github.com/Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec) — Swiss army knife for pentesting networks.
* **MITRE ATT&CK Matrix for Enterprise:**
  * [T1087.002 - Account Discovery: Domain Account](https://attack.mitre.org/techniques/T1087/002/)
  * [T1135 - Network Share Discovery](https://attack.mitre.org/techniques/T1135/)
  * [T1046 - Network Service Discovery](https://attack.mitre.org/techniques/T1046/)
  * [T1201 - Password Policy Discovery](https://attack.mitre.org/techniques/T1201/)
