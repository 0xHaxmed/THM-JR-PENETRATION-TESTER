# Official References & Standards: Windows Jump (Challenge)

## Technical References & Documentation

1. **Microsoft AutoLogon Security Considerations**
   * *Publisher:* Microsoft Learn
   * *Scope:* Documentation on configuring AutoLogon in the Windows Registry and the security risks of storing cleartext credentials in `HKLM\...\Winlogon`.
   * *URL:* https://learn.microsoft.com/en-us/troubleshoot/windows-server/user-profiles-and-logon/turn-on-automatic-logon

2. **Windows Service Security and Access Rights**
   * *Publisher:* Microsoft Learn
   * *Scope:* Service security descriptors, access rights (`SERVICE_CHANGE_CONFIG`, `SERVICE_START`), and binary execution rules.
   * *URL:* https://learn.microsoft.com/en-us/windows/win32/services/service-security-and-access-rights

---

## Cybersecurity Frameworks & Standards

3. **MITRE ATT&CK Enterprise Matrix (Windows)**
   * *Tactics & Techniques:*
     * TA0006: Credential Access
       - T1552.002: Credentials in Registry
       - T1021.002: SMB/Windows Admin Shares
     * TA0004: Privilege Escalation
       - T1543.003: Windows Service
       - T1053.005: Scheduled Task
     * TA0008: Lateral Movement
       - T1078.003: Local Accounts
   * *URL:* https://attack.mitre.org/

---

## TryHackMe Laboratory Resources

4. **TryHackMe Room: Windows Jump (Challenge)**
   * *Room Slug:* `windowsjump`
   * *Path:* Jr Penetration Tester
   * *Module:* Module 13 — Privilege Escalation
   * *URL:* https://tryhackme.com/room/windowsjump
