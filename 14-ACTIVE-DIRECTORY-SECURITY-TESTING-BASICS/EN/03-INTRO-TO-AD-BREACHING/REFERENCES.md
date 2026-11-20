# References & Authoritative Sources: Intro to AD Breaching

---

## 1. Official TryHackMe Documentation
* [TryHackMe Intro to AD Breaching](https://tryhackme.com/room/introductiontoactivedirectorybreaching)
* [TryHackMe Junior Penetration Tester Learning Path](https://tryhackme.com/path/outline/jrpenetrationtester)
* [TryHackMe Compromising Active Directory Module](https://tryhackme.com/module/hacking-active-directory)

---

## 2. Microsoft Technical Specifications & Guides
* **Account Lockout Policy Settings:**
  [https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/account-lockout-policy](https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/account-lockout-policy)
* **Network Security: LAN Manager Authentication Level:**
  [https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/network-security-lan-manager-authentication-level](https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/network-security-lan-manager-authentication-level)
* **LDAP over SSL (LDAPS) Certificate Requirements:**
  [https://learn.microsoft.com/en-us/troubleshoot/windows-server/identity/enable-ldap-over-ssl-3rd-certification-authority](https://learn.microsoft.com/en-us/troubleshoot/windows-server/identity/enable-ldap-over-ssl-3rd-certification-authority)

---

## 3. Authoritative Tooling & Research Repositories
* **Kerbrute by Ronnie Flathers (ropnop):**
  [https://github.com/ropnop/kerbrute](https://github.com/ropnop/kerbrute) — A tool to quickly bruteforce and enumerate valid Active Directory accounts via Kerberos pre-authentication.
* **NetExec (nxc):**
  [https://github.com/Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec) — The network execution and password spraying suite for active directory assessment.
* **Responder by Laurent Gaffié (SpiderLabs):**
  [https://github.com/SpiderLabs/Responder](https://github.com/SpiderLabs/Responder) — LLMNR, NBT-NS and MDNS poisoner and authentication capture engine.
* **MITRE ATT&CK Matrix for Enterprise:**
  * [T1589.002 - Gather Victim Identity Information: Email Addresses / Usernames](https://attack.mitre.org/techniques/T1589/002/)
  * [T1110.003 - Brute Force: Password Spraying](https://attack.mitre.org/techniques/T1110/003/)
  * [T1552.001 - Unsecured Credentials: Credentials in Files](https://attack.mitre.org/techniques/T1552/001/)
  * [T1187 - Forced Authentication (Coercion)](https://attack.mitre.org/techniques/T1187/)
