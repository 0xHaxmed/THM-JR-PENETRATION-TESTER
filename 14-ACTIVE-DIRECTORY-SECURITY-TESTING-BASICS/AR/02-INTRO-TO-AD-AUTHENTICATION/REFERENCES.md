# References & Authoritative Sources: Intro to AD Authentication

---

## 1. Official TryHackMe Documentation
* [TryHackMe Intro to AD Authentication](https://tryhackme.com/room/introtoactivedirectoryauthentication)
* [TryHackMe Junior Penetration Tester Learning Path](https://tryhackme.com/path/outline/jrpenetrationtester)
* [TryHackMe Compromising Active Directory Module](https://tryhackme.com/module/hacking-active-directory)

---

## 2. Microsoft Technical Specifications & Standards
* **MS-NLMP: NT LAN Manager (NTLM) Authentication Protocol:**
  [https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-nlmp/](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-nlmp/)
* **MS-KILE: Kerberos Protocol Extensions:**
  [https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-kile/](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-kile/)
* **RFC 4120 - The Kerberos Network Authentication Service (V5):**
  [https://www.ietf.org/rfc/rfc4120.txt](https://www.ietf.org/rfc/rfc4120.txt)
* **Microsoft Windows Security Auditing Documentation (Events 4624, 4625, 4768, 4769):**
  [https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/audit-logon-events](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/audit-logon-events)

---

## 3. Authoritative Tooling & Research
* **Impacket Suite (SecureAuth / Fortra):**
  [https://github.com/fortra/impacket](https://github.com/fortra/impacket) (`smbclient.py`, `getTGT.py`, `GetUserSPNs.py`, `ticketer.py`, `psexec.py`).
* **Hashcat - Advanced Password Recovery:**
  [https://hashcat.net/hashcat/](https://hashcat.net/hashcat/) (Modes 1000 NTLM, 5600 NetNTLMv2, 13100 Kerberos 5 TGS-REP, 18200 Kerberos 5 AS-REP).
* **The Kerberos Party by HarmJ0y (Will Schroeder):**
  [https://posts.specterops.io/kerberoasting-without-spns-92e8da0a0494](https://posts.specterops.io/kerberoasting-without-spns-92e8da0a0494)
* **MITRE ATT&CK Matrix for Enterprise:**
  * [T1550.002 - Use Alternate Authentication Material: Pass the Hash](https://attack.mitre.org/techniques/T1550/002/)
  * [T1558.003 - Steal or Forge Kerberos Tickets: Kerberoasting](https://attack.mitre.org/techniques/T1558/003/)
  * [T1558.001 - Steal or Forge Kerberos Tickets: Golden Ticket](https://attack.mitre.org/techniques/T1558/001/)
