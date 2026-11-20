# Official References & Standards: Windows Privilege Escalation

## Microsoft Technical Documentation

1. **Microsoft Privilege Constants Reference**
   * *Publisher:* Microsoft Learn
   * *Scope:* Specifications for user rights, token privileges (`SeBackupPrivilege`, `SeTakeOwnershipPrivilege`, `SeImpersonatePrivilege`), and security checks.
   * *URL:* https://learn.microsoft.com/en-us/windows/win32/secauthz/privilege-constants

2. **Service Control Manager Architecture**
   * *Publisher:* Microsoft Learn
   * *Scope:* Technical details on Windows service installation, startup configuration, binary path resolution, and security descriptors.
   * *URL:* https://learn.microsoft.com/en-us/windows/win32/services/service-control-manager

3. **AlwaysInstallElevated Policy Documentation**
   * *Publisher:* Microsoft Learn
   * *Scope:* Group Policy settings for elevated Windows Installer package execution.
   * *URL:* https://learn.microsoft.com/en-us/windows/win32/msi/alwaysinstallelevated

---

## Offensive Security Research & Exploit Projects

4. **Priv2Admin: Windows Privileges to Administrator Mapping**
   * *Author:* itm4n
   * *Scope:* Comprehensive reference mapping Windows user token privileges directly to local administrative execution techniques.
   * *Repository:* https://github.com/gtworek/Priv2Admin

5. **Sysinternals Suite: AccessChk**
   * *Author:* Mark Russinovich / Microsoft Sysinternals
   * *Scope:* Command-line utility to query permissions on files, directories, registry keys, and Windows services.
   * *URL:* https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk

6. **PayloadsAllTheThings: Windows Privilege Escalation**
   * *Maintainer:* Swissky & Contributors
   * *Scope:* Curated cheat sheet covering unquoted paths, service permissions, AlwaysInstallElevated, and token impersonation.
   * *Repository:* https://github.com/swisskyrepo/PayloadsAllTheThings

---

## Cybersecurity Frameworks & Standards

7. **MITRE ATT&CK Enterprise Matrix: Privilege Escalation (Windows)**
   * *Tactics & Techniques:*
     * TA0004: Privilege Escalation
       - T1543.003: Windows Service
       - T1574.009: Path Interception by Unquoted Path
       - T1134: Access Token Manipulation
       - T1053.005: Scheduled Task
     * TA0006: Credential Access
       - T1552.001: Credentials in Files
       - T1552.002: Credentials in Registry
       - T1003.002: Security Account Manager (SAM)
   * *URL:* https://attack.mitre.org/

---

## TryHackMe Laboratory Resources

8. **TryHackMe Room: Windows Privilege Escalation**
   * *Room Slug:* `windowsprivesc20`
   * *Path:* Jr Penetration Tester
   * *Module:* Module 13 — Privilege Escalation
   * *URL:* https://tryhackme.com/room/windowsprivesc20
