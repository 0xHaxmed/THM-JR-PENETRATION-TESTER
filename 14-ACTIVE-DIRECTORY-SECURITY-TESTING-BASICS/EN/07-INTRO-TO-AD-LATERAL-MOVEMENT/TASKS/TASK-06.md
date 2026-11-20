# Task 06: Mitigations & Defensive Engineering

---

## Stage A: Original Task Content & Educational Context

In this task, we analyze the defensive countermeasures and engineering strategies required to prevent, detect, and mitigate the lateral movement techniques demonstrated throughout this room:
1. **Preventing Local Credential Reuse:** Eliminating static, shared passwords across local administrator accounts.
2. **Hardening Protocols & Cryptography:** Disabling insecure protocols (NTLM, SMBv1), enforcing SMB Signing, and isolating LSASS memory via Credential Guard.
3. **Network Architecture & Tiering:** Implementing strict network segmentation and Microsoft's Enterprise Access Model (Tiered Administration).
4. **Endpoint Auditing & SIEM Detection:** Monitoring high-fidelity Event IDs for remote execution artifacts (PsExec services, WinRM sessions, explicit credential usage).

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Deep Dive into Windows LAPS (Local Administrator Password Solution)
The primary vulnerability enabling rapid lateral movement in Task 4 was the reuse of the local Administrator password across `WRK1` and `SERVER1`.
- **What is Windows LAPS?** A native Windows feature (modern Windows LAPS is built directly into Windows 11, Windows 10 post-2023 updates, and Windows Server 2019/2022/2025).
- **How it Works:**
  - An agent on each domain-joined computer periodically generates a cryptographically strong, random password for the designated local administrator account.
  - The password is encrypted and stored as a secure attribute in Active Directory (`msLAPS-Password` or legacy `ms-Mcs-AdmPwd`) or Microsoft Entra ID.
  - Granular Access Control Lists (ACLs) dictate which helpdesk or IT roles are permitted to read the password.
  - Because **every single machine has a completely unique password**, harvesting the local admin hash on Host A provides **zero access** to Host B.

### Detection Engineering & Event Logging Matrix

| Event ID | Log Provider | Event Title | Forensic Significance for Lateral Movement |
|---|---|---|---|
| **7045** | System | *A service was installed in the system* | **Primary indicator of PsExec.** Logs Service Name, Image File Path (`C:\Windows\...`), and Service Type (`user mode service`). |
| **4697** | Security | *A service was installed in the system* | Security log equivalent of 7045 (enabled via "Audit Security System Extension"). |
| **4624** | Security | *An account was successfully logged on* | Logon Type `3` indicates a Network logon (SMB, WinRM, RPC). |
| **4648** | Security | *A logon was attempted using explicit credentials* | Triggered when a process supplies alternate credentials (e.g., `runas /netonly`, Mimikatz `sekurlsa::pth`, Overpass-the-Hash). |
| **4688** | Security | *A new process has been created* | Enables command-line auditing (requires "Include command line in process creation events"). |
| **Sysmon 1** | Microsoft-Windows-Sysmon | *Process Creation* | Captures parent-child process anomalies (e.g., `services.exe` spawning an unknown `.exe` in `C:\Windows\`). |

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Identify the Microsoft solution designed to eliminate identical local administrator passwords across domain-joined computers.
- Recall the specific System log Event ID that alerts administrators whenever a new service is registered.

### Tier 2: Technology Names & Event Numbers
- Built-in feature: Two words: "Windows" followed by the acronym for Local Administrator Password Solution ("LAPS").
- Event ID: A 4-digit number starting with `70` (`7045`).

### Tier 3: Concrete Answers
- Question 1: `Windows LAPS`
- Question 2: `7045`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Windows LAPS Implementation Architecture
When Windows LAPS is deployed:
1. GPO configures password complexity, length (minimum 14 characters recommended), and rotation age (default: 30 days).
2. The endpoint evaluates the expiration timer. When expired, the endpoint calls the CryptGenRandom API to generate a new password and calls `NetUserSetInfo` to update the local SAM.
3. The endpoint updates Active Directory over an encrypted LDAP connection, encrypting the secret with the public key of the domain controller.
4. If an attacker dumps the SAM database of a compromised client, the recovered hash works **only on that specific client machine**.

### 2. Identifying PsExec via Event ID 7045
When `psexec.py` ran in Task 3 and Task 4, the Windows Event Viewer on `WRK1` and `SERVER1` immediately generated Event ID 7045:

```xml
<Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event">
  <System>
    <Provider Name="Service Control Manager" Guid="{555908d1-a6d7-4695-8e1e-26931b2012f4}" />
    <EventID>7045</EventID>
    <Channel>System</Channel>
  </System>
  <EventData>
    <Data Name="ServiceName">ZcMo</Data>
    <Data Name="ImagePath">%SystemRoot%\kKjQbvqG.exe</Data>
    <Data Name="ServiceType">user mode service</Data>
    <Data Name="StartType">demand start</Data>
    <Data Name="AccountName">LocalSystem</Data>
  </EventData>
</Event>
```

Security Operations Centers (SOCs) configure SIEM detection rules alerting on:
$$\text{EventID} = 7045 \quad \land \quad \text{ImagePath} \text{ matches } \texttt{\%SystemRoot\%\\[a-zA-Z0-9]\{8\}\.exe}$$
which instantly flags Impacket-based service installations.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What built-in Windows feature automatically generates unique local admin passwords for each domain-joined machine? | `Windows LAPS` | Verified & Confirmed |
| 2 | What Windows Event ID in the System log indicates a new service has been installed? | `7045` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. `Windows LAPS` feature verification: Documented in official Microsoft Learn architectures for local credential defense.
2. Event ID 7045 verification: Confirmed via PowerShell querying the System log on `WRK1`:
   ```powershell
   Get-WinEvent -FilterHashtable @{LogName='System'; Id=7045} | Select-Object -First 1
   ```

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Lateral movement defense is built on breaking the assumption of trust between peers. Workstations should never communicate directly with other workstations over SMB, WinRM, or RDP (East-West traffic filtering). Enforcing Windows LAPS and alerting on Event ID 7045 eliminates the vast majority of automated credential reuse and service-based remote execution attacks.
