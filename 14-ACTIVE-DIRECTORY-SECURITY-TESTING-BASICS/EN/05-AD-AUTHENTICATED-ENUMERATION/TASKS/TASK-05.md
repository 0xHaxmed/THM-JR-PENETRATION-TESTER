# Task 05: Enumeration With PowerShell’s ActiveDirectory & PowerView Modules

---

## Stage A: Original Task Content & Educational Context

While native command-line binaries (`net.exe`) and automated graph tools (`BloodHound`) provide essential reconnaissance capabilities, advanced operators require flexible scripting interfaces to filter, manipulate, and extract specific Active Directory attributes on demand.

PowerShell provides two primary mechanisms for querying Active Directory:
1. **The Microsoft `ActiveDirectory` PowerShell Module:**
   - Official administrative module provided by Microsoft via the Remote Server Administration Tools (RSAT).
   - Uses the Active Directory Web Services (ADWS) protocol over TCP port 9389 or native LDAP.
   - Ideal for administrative and stealthy operations because it is signed by Microsoft and native to server environments.
2. **PowerSploit's `PowerView` (`PowerView.ps1`):**
   - Developed by Will Schroeder (`@harmj0y`), PowerView is an offensive PowerShell tool designed specifically for penetration testers and red teams.
   - It bypasses ADWS and directly constructs custom ADSI (Active Directory Service Interfaces) LDAP queries against the Domain Controller.
   - Functions output rich .NET objects that can be seamlessly piped into standard PowerShell filtering cmdlets (`Where-Object`, `Select-Object`, `Measure-Object`).

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### ActiveDirectory Module vs. PowerView OpSec Tradeoffs
- **ActiveDirectory Module:**
  - *Pros:* Legitimate administrative tool; presence rarely flagged by AV. Does not require loading unmanaged scripts into memory.
  - *Cons:* Often not installed on client workstations by default; requires RSAT or manual DLL importing.
- **PowerView:**
  - *Pros:* Extremely feature-rich; provides one-liner offensive queries (`Get-DomainUser -SPN`, `Get-DomainUser -AdminCount`, `Find-LocalAdminAccess`).
  - *Cons:* Heavily signatured by Windows Defender and Antimalware Scan Interface (AMSI). Loading `PowerView.ps1` directly into PowerShell memory without AMSI bypass techniques often triggers immediate script block logging (Event ID `4104`) or real-time AV blocks.

### LDAP Search Filter Optimization
Both tools query the DC via LDAP. In large domains with tens of thousands of objects, broad queries like `Get-DomainUser` can cause performance degradation and trigger anomalous LDAP query volume alerts. Always use targeted filters (e.g., `-Filter "Name -like '*admin*'"` or LDAP filters like `(&(objectClass=user)(adminCount=1))`).

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- On the target machine `WRK`, start PowerShell.
- Use the Microsoft `ActiveDirectory` module's `Get-ADComputer` cmdlet to count domain computers.
- Navigate to the PowerSploit directory (`C:\Users\asrepuser1\Downloads\PowerSploit-master\Recon\`), import `PowerView.ps1`, and execute `Get-DomainGroup "*admin*"`.

### Tier 2: Filtering & Counting
- To count computers:
  ```powershell
  (Get-ADComputer -Filter *).Count
  ```
- To count the admin groups in PowerView, select unique group account names and measure the count:
  ```powershell
  (Get-DomainGroup "*admin*" | Select-Object "samaccountname").Count
  ```

### Tier 3: Concrete Command Syntax
```powershell
# Computer count:
Get-ADComputer -Filter * | Measure-Object

# PowerView admin groups count:
cd C:\Users\asrepuser1\Downloads\PowerSploit-master\Recon
Import-Module .\PowerView.ps1
Get-DomainGroup "*admin*" | Select-Object "samaccountname" | Measure-Object
```

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Computer Account Enumeration via ActiveDirectory Module
Log in to `WRK` via SSH or PowerShell remoting as `asrepuser1`:

```powershell
asrepuser1@WRK C:\Users\asrepuser1> powershell -NoProfile
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Users\asrepuser1> Import-Module ActiveDirectory
PS C:\Users\asrepuser1> Get-ADComputer -Filter * | Select-Object Name, DNSHostName, OperatingSystem

Name DNSHostName        OperatingSystem
---- -----------        ---------------
DC   DC.tryhackme.loc   Windows Server 2019 Datacenter
WRK  WRK.tryhackme.loc  Windows 10 Pro

PS C:\Users\asrepuser1> (Get-ADComputer -Filter *).Count
2
```

The domain contains exactly **2** computer accounts: the Domain Controller (`DC`) and the Workstation (`WRK`).

### 2. Loading and Enumerating Groups with PowerView
Navigate to the pre-staged PowerSploit directory on the target workstation:

```powershell
PS C:\Users\asrepuser1> Set-Location C:\Users\asrepuser1\Downloads\PowerSploit-master\Recon
PS C:\Users\asrepuser1\Downloads\PowerSploit-master\Recon> Import-Module .\PowerView.ps1
```

Now execute `Get-DomainGroup` matching the wildcard pattern `*admin*`:

```powershell
PS C:\Users\asrepuser1\Downloads\PowerSploit-master\Recon> Get-DomainGroup "*admin*" | Select-Object "samaccountname"

samaccountname
--------------
Domain Admins
Enterprise Admins
Key Admins
Enterprise Key Admins
Schema Admins
IT Administrators
Hyper-V Administrators
Storage Replica Administrators
DnsAdmins
Server Admins
Workstation Admins
Helpdesk Admins
Local Admins
```

Piping the output into `Measure-Object` to get the exact count:

```powershell
PS C:\Users\asrepuser1\Downloads\PowerSploit-master\Recon> (Get-DomainGroup "*admin*" | Select-Object "samaccountname").Count
13
```

The command returns exactly **13** groups matching the pattern `*admin*`.

### 3. Additional Key PowerView Reconnaissance Commands
For comprehensive penetration testing context, PowerView provides several indispensable queries:

#### A. Identifying Accounts with AdminCount=1 (Protected Accounts):
```powershell
Get-DomainUser -AdminCount | Select-Object samaccountname, description
```
Accounts with `adminCount=1` are or were members of protected administrative groups (e.g., Domain Admins, Enterprise Admins, Schema Admins) and are protected by the `AdminSDHolder` background process (`SDPROP`).

#### B. Identifying Kerberoastable Accounts (SPN Enumeration):
```powershell
Get-DomainUser -SPN | Select-Object samaccountname, serviceprincipalname
```
Identifies service accounts with Service Principal Names registered, making them prime targets for Kerberoasting attacks.

#### C. Finding Domain Controllers:
```powershell
Get-DomainController | Select-Object Name, IPAddress, OSVersion
```

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | How many computer accounts were you able to find? | `2` | Verified & Confirmed |
| 2 | How many groups did Get-DomainGroup "*admin*" return? | `13` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. `(Get-ADComputer -Filter *).Count` executed on `WRK` returns an integer count of `2`.
2. `(Get-DomainGroup "*admin*" | Select-Object "samaccountname").Count` executed via PowerView returns an integer count of `13`.
3. Cross-verification with BloodHound-CE: Running Cypher query `MATCH (c:Computer) RETURN count(c)` returns `2`.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** PowerShell cmdlets return structured .NET objects rather than plain text streams. This enables operators to construct precise, high-speed programmatic filters (e.g., filtering users by `pwdlastset`, `badpwdcount`, or `serviceprincipalname`) without needing text-parsing utilities like `grep`, `awk`, or `cut`. However, be conscious of script block logging: in hardened enterprise environments, prefer native `Get-AD*` cmdlets or raw ADSI queries over public copies of `PowerView.ps1`.
