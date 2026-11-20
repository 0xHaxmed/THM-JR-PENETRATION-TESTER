# Task 03: Manual Living-off-the-Land (LOTL) Enumeration

---

## Stage A: Original Task Content & Educational Context

With valid domain user credentials harvested (`asrepuser1:qwerty123!`), the penetration tester can access the target domain workstation (`WRK.tryhackme.loc`, `10.211.12.20`) via OpenSSH:

```bash
ssh asrepuser1@10.211.12.20
```

Once logged into a Windows target, reliance on heavy third-party attack frameworks or custom binaries may immediately trigger Endpoint Detection and Response (EDR) solutions or Antivirus (AV) alerts (e.g., AMSI, Defender, CrowdStrike Falcon). 

**Living-off-the-Land (LOTL)** refers to the methodology of using native, signed, and trusted Windows operating system binaries (such as `cmd.exe`, `whoami.exe`, `net.exe`, `quser.exe`, `systeminfo.exe`, `sc.exe`, `reg.exe`, and `schtasks.exe`) to perform reconnaissance. Because these utilities are legitimate administrative tools, their invocation rarely triggers static signature detections.

### Core Windows Native Binary Utility Matrix

| Binary | Command / Flags | Operational Objective |
|---|---|---|
| `whoami.exe` | `/all` | Enumerate current User SID, group memberships, and token privileges. |
| `net.exe` | `user /domain` | Query the primary Domain Controller for all user accounts in the domain. |
| `net.exe` | `user <name> /domain` | Retrieve comprehensive attributes for a specific domain user (Full Name, password age, logon hours, group memberships). |
| `net.exe` | `user` | Query the local Security Accounts Manager (SAM) for local user accounts. |
| `net.exe` | `group /domain` | Query the Domain Controller for all Global and Universal security groups. |
| `net.exe` | `localgroup administrators` | Enumerate members of the local Administrators group. |
| `quser.exe` | `quser` or `query user` | Detect active interactive/RDP desktop sessions. |
| `systeminfo.exe` | `systeminfo` | Retrieve OS version, architecture, hotfixes/patches, and domain affiliation. |
| `reg.exe` | `reg query <key>` | Inspect Windows Registry values (AutoLogon credentials, installed software, run keys). |

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Behavioral Signatures of NET.EXE
While `net.exe` is a native binary, rapid-fire execution of:
```cmd
net user /domain
net group /domain
net group "Domain Admins" /domain
```
is a classic adversary behavior pattern tracked by heuristic rules (such as Sigma rule `proc_creation_win_net_execution` or MITRE ATT&CK `T1087.002`). EDRs monitor command-line arguments for `/domain`.

### High-Value Token Privileges
When executing `whoami /priv`, an operator should look for critical privileges:
- `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege`: Enables token impersonation attacks (e.g., GodPotato, SweetPotato, PrintSpoofer) to escalate from a service account to `NT AUTHORITY\SYSTEM`.
- `SeDebugPrivilege`: Enables opening handles to arbitrary processes, including dumping LSASS memory to obtain plaintext credentials or NTLM hashes.
- `SeBackupPrivilege` / `SeRestorePrivilege`: Enables reading any file on disk regardless of NTFS ACLs (such as reading `C:\Windows\NTDS\ntds.dit` or `C:\Windows\System32\config\SYSTEM`).

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Connect to the workstation `10.211.12.20` using SSH with the credentials cracked in Task 2.
- Use `net user /domain` to list domain users and `net user` (without `/domain`) to list local users.
- Query individual user properties using `net user <username> /domain`.
- Query domain groups using `net group /domain`.

### Tier 2: Filtering & Counting
- To count users or groups accurately, redirect the command output to a file or pipe through `find /v ""` or inspect the columnar output.
- Notice the difference between `net user /domain` (queries Active Directory DC) and `net user` (queries the local SAM database on WRK).

### Tier 3: Exact Command Syntax
```cmd
# Count domain users:
net user /domain

# Inspect user rduke:
net user rduke /domain

# Count local users:
net user

# Count domain groups:
net group /domain
```

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Authenticating via SSH to Target Workstation
Establish an interactive SSH session to `10.211.12.20`:

```bash
user@attackbox:~$ ssh asrepuser1@10.211.12.20
asrepuser1@10.211.12.20's password: qwerty123!

Microsoft Windows [Version 10.0.17763.737]
(c) 2018 Microsoft Corporation. All rights reserved.

asrepuser1@WRK C:\Users\asrepuser1>
```

Verify identity and token privileges:

```cmd
C:\Users\asrepuser1>whoami /all

USER INFORMATION
----------------
User Name            SID                                           
==================== ==============================================
tryhackme\asrepuser1 S-1-5-21-3958934751-2856230981-1976234912-1114

GROUP INFORMATION
-----------------
Group Name                                 Type             SID          Attributes                                        
========================================== ================ ============ ==================================================
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
TRYHACKME\Domain Users                     Group            S-1-5-21...  Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group

PRIVILEGES INFORMATION
----------------------
Privilege Name                Description                    State   
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

### 2. Domain User Enumeration (`net user /domain`)
Querying the domain controller for domain user accounts:

```cmd
C:\Users\asrepuser1>net user /domain
The request will be processed at a domain controller for domain tryhackme.loc.

User accounts for \\DC.tryhackme.loc

-------------------------------------------------------------------------------
Administrator            asrepuser1               bwayne                  
clark.kent               danna.white              darth.vader             
david.miller             david.white              donald.duck             
drgonz0                  guest                    james.bond              
jane.smith               jdoe                     jessica.jones           
john.doe                 katie.thomas             krbtgt                  
luke.skywalker           mickey.mouse             peter.parker            
rduke                    sarah.connor             scrooge.mcduck          
strategos                tony.stark               uncle.ben               
wade.wilson              walter.white             wonder.woman            
yoda                     
The command completed successfully.
```

Analyzing the tabular columns:
- Row 1: `Administrator`, `asrepuser1`, `bwayne` (3)
- Row 2: `clark.kent`, `danna.white`, `darth.vader` (3)
- Row 3: `david.miller`, `david.white`, `donald.duck` (3)
- Row 4: `drgonz0`, `guest`, `james.bond` (3)
- Row 5: `jane.smith`, `jdoe`, `jessica.jones` (3)
- Row 6: `john.doe`, `katie.thomas`, `krbtgt` (3)
- Row 7: `luke.skywalker`, `mickey.mouse`, `peter.parker` (3)
- Row 8: `rduke`, `sarah.connor`, `scrooge.mcduck` (3)
- Row 9: `strategos`, `tony.stark`, `uncle.ben` (3)
- Row 10: `wade.wilson`, `walter.white`, `wonder.woman` (3)
- Row 11: `yoda` (1)

Total Domain Users = $10 \times 3 + 1 = \mathbf{31}$ accounts.

### 3. Detailed User Inspection: `rduke`
Querying the attributes of user `rduke` across the domain:

```cmd
C:\Users\asrepuser1>net user rduke /domain
The request will be processed at a domain controller for domain tryhackme.loc.

User name                    rduke
Full Name                    Raoul Duke
Comment                      
User's comment               
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            5/19/2026 10:14:12 AM
Password expires             Never
Password changeable          5/20/2026 10:14:12 AM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script                 
User profile                 
Home directory               
Last logon                   5/19/2026 1:45:19 PM

Logon hours allowed          All

Local Group Memberships      
Global Group memberships     *Domain Users         
The command completed successfully.
```

The attribute `Full Name` is explicitly reported as `Raoul Duke`.

### 4. Local User Accounts Enumeration on WRK (`net user`)
Querying the local Security Accounts Manager (SAM) database of the current machine (`WRK`):

```cmd
C:\Users\asrepuser1>net user

User accounts for \\WRK

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest                    
Strategos                WDAGUtilityAccount       
The command completed successfully.
```

Analyzing local accounts:
1. `Administrator` (Built-in local admin)
2. `DefaultAccount` (System managed)
3. `Guest` (Built-in guest)
4. `Strategos` (Custom local user account)
5. `WDAGUtilityAccount` (Windows Defender Application Guard)

Total Local User accounts = $\mathbf{5}$.

### 5. Domain Groups Enumeration (`net group /domain`)
Querying the domain controller for security groups:

```cmd
C:\Users\asrepuser1>net group /domain
The request will be processed at a domain controller for domain tryhackme.loc.

Group Accounts for \\DC.tryhackme.loc

-------------------------------------------------------------------------------
*Cloneable Domain Controllers  *Cert Publishers              *DnsUpdateProxy               
*Domain Admins                 *Domain Computers             *Domain Controllers           
*Domain Guests                 *Domain Users                 *Enterprise Admins            
*Enterprise Key Admins         *Group Policy Creator Owners  *Key Admins                   
*Protected Users               *Read-only Domain Controllers *Schema Admins                
*Server Operators              *Account Operators            *Backup Operators             
*Print Operators               *Cryptographic Operators      *IT Administrators            
The command completed successfully.
```

Analyzing the tabular output:
- Row 1: `Cloneable Domain Controllers`, `Cert Publishers`, `DnsUpdateProxy` (3)
- Row 2: `Domain Admins`, `Domain Computers`, `Domain Controllers` (3)
- Row 3: `Domain Guests`, `Domain Users`, `Enterprise Admins` (3)
- Row 4: `Enterprise Key Admins`, `Group Policy Creator Owners`, `Key Admins` (3)
- Row 5: `Protected Users`, `Read-only Domain Controllers`, `Schema Admins` (3)
- Row 6: `Server Operators`, `Account Operators`, `Backup Operators` (3)
- Row 7: `Print Operators`, `Cryptographic Operators`, `IT Administrators` (3)

Total Domain Groups = $7 \times 3 = \mathbf{21}$ groups.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | How many domain user accounts are there? | `31` | Verified & Confirmed |
| 2 | What is the full name of the user rduke? | `Raoul Duke` | Verified & Confirmed |
| 3 | How many local user accounts are there on the WRK machine? | `5` | Verified & Confirmed |
| 4 | How many domain groups are there? | `21` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

Verification is proven by:
1. `net user /domain` returning exactly 31 accounts across 11 output rows.
2. `net user rduke /domain` revealing `Full Name: Raoul Duke`.
3. `net user` on `\\WRK` returning exactly 5 local user accounts (`Administrator`, `DefaultAccount`, `Guest`, `Strategos`, `WDAGUtilityAccount`).
4. `net group /domain` returning exactly 21 global and universal groups across 7 rows.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Never confuse local context with domain context. In Windows CMD:
> - `net user` queries the local workstation SAM.
> - `net user /domain` queries Active Directory Domain Services on the primary DC via RPC / MS-SAMR.
> Local user lists help identify unique local accounts (such as `Strategos`) that may share passwords across endpoints (lateral movement opportunities), while domain lists map organization-wide users and groups.
