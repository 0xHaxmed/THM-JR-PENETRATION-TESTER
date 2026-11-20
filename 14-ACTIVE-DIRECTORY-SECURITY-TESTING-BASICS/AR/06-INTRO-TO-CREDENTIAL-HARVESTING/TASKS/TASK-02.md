# Task 02: Windows & Active Directory Credential Stores

---

## Stage A: Original Task Content & Educational Context

To harvest credentials effectively, a penetration tester must understand the distinct repositories where Windows and Active Directory store sensitive authentication material. Each store serves a specific functional purpose, uses different encryption keys, and requires specific privileges to access.

### Comprehensive Matrix of Windows & AD Credential Repositories

| Repository | Scope / Location | Stored Credential Types | Access Requirements | Primary Tools & Extraction Commands |
|---|---|---|---|---|
| **LSASS Memory** | Host In-Memory (`lsass.exe`) | Plaintext passwords (WDigest/SSP), NTLM hashes, Kerberos TGT/TGS tickets | `SYSTEM` or `SeDebugPrivilege` (Local Admin) | `mimikatz`: `privilege::debug`, `sekurlsa::logonpasswords` |
| **SAM & SYSTEM Hives** | On-Disk (`%SystemRoot%\System32\config\`) | Local user NTLM hashes | Local Admin / `SYSTEM` or `SeBackupPrivilege` | `reg save HKLM\SAM SAM`, `reg save HKLM\SYSTEM SYSTEM`, `mimikatz`: `lsadump::sam` |
| **LSA Secrets** | Registry (`HKLM\SECURITY\Policy\Secrets`) | Domain Cached Credentials (DCC2 / MS-Cache v2), Service accounts, AutoLogon passwords | `SYSTEM` (requires token elevation) | `mimikatz`: `token::elevate`, `lsadump::cache`, `secretsdump.py` |
| **DPAPI Credential Vault** | User Profile (`%APPDATA%\Microsoft\Protect`) | Saved RDP credentials, Browser passwords, Wi-Fi keys, Windows Vault | User token or Local Admin + DPAPI MasterKey | `mimikatz`: `vault::list`, `vault::cred /export` |
| **NTDS.dit** | Domain Controller (`C:\Windows\NTDS\ntds.dit`) | Entire Active Directory database: all domain user/computer NTLM hashes & Kerberos keys | Domain Admin or Enterprise Admin on DC | `secretsdump.py -just-dc`, `mimikatz`: `lsadump::dcsync` |

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Understanding the Credential Storage Mechanics
1. **LSASS (Local Security Authority Subsystem Service):**
   - LSASS is responsible for enforcing security policy, handling user authentications, and storing active authentication tokens.
   - Because Windows avoids asking users for their password on every network share access, LSASS maintains active session keys, NTLM hashes, and Kerberos tickets in RAM as long as the user session is active.
   - Once a user logs out, their plaintext credentials and tickets are typically discarded, but their hash may remain cached in unmanaged heap memory until overwritten.
2. **NTDS.dit:**
   - Active Directory's database engine (Extensible Storage Engine / ESE / JET database) resides exclusively on Domain Controllers at `C:\Windows\NTDS\ntds.dit`.
   - The file is locked exclusively by the operating system while the DC is running. It cannot be copied directly with `copy` or `xcopy`. To extract it offline, attackers use Volume Shadow Copy (`vssadmin create shadow /for=C:`), `ntdsutil`, or online replication attacks like **DCSync** (MS-DRSR).
3. **DPAPI (Data Protection API):**
   - Windows DPAPI encrypts sensitive user data using keys derived from the user's password.
   - Applications like Google Chrome, Microsoft Edge, and Credential Manager store secrets in "Vaults".
   - Mimikatz provides the `vault::cred /export` command to enumerate and decrypt all credential blobs stored in the Windows Credential Manager.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Identify the core Windows user-mode process responsible for authentication and storing active tickets and hashes in RAM.
- Recall the canonical filename (with `.dit` extension) that holds the Active Directory database on Domain Controllers.
- Identify the Mimikatz module that deals with Windows Vaults and the specific action flag used to export credentials.

### Tier 2: Acronyms & File Extensions
- Process name: 5 letters, starts with `L`, ends with `S` (`LSASS`).
- AD database file: 4 letters before the dot, 3 letters after (`ntds.dit`).
- Mimikatz command: The module is `vault`, command is `cred`, and flag is `/export`.

### Tier 3: Concrete Answers
- Component: `LSASS`
- Database file: `ntds.dit`
- Mimikatz command: `vault::cred /export`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. LSASS Architecture & Functionality
LSASS (`C:\Windows\System32\lsass.exe`) is protected by Windows operating system access controls. In modern Windows systems, Protected Process Light (PPL) or Credential Guard (virtualization-based security / VBS) can be enabled to prevent non-system processes from reading LSASS memory. However, on standard enterprise endpoints without Credential Guard, any process running with `SeDebugPrivilege` under local administrator or `NT AUTHORITY\SYSTEM` context can open a handle to `lsass.exe` using `OpenProcess(PROCESS_VM_READ | PROCESS_QUERY_INFORMATION)` and dump its virtual memory space.

### 2. Active Directory Database: `NTDS.dit`
The file `ntds.dit` (NT Directory Services Directory Information Tree) contains three primary tables:
- **Schema Table:** Defines the classes and attributes permitted in the directory.
- **Link Table:** Stores relations (e.g., group memberships).
- **Data Table:** Stores all directory objects: users, computers, organizational units, and their attributes—including `unicodePwd` (NTLM password hash) and `dBCSPwd` (LM hash), which are additionally encrypted with the Domain Key (PEK - Password Encryption Key).

### 3. DPAPI & Windows Credential Vaults
Windows DPAPI uses two primary types of vaults:
- **Windows Credentials:** Stored in `%APPDATA%\Microsoft\Credentials`.
- **Web Credentials:** Stored in `%LOCALAPPDATA%\Microsoft\Vault\<GUID>`.

Mimikatz interacts with the Windows Vault API via its `vault` module:
- `vault::list`: Enumerates all available vaults on the local system.
- `vault::cred /export`: Queries the Windows Credential Vault API, acquires the DPAPI decryption keys, and exports decrypted plaintext usernames, URLs, and passwords.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | Which Windows component stores active NTLM and Kerberos credentials in memory? | `LSASS` | Verified & Confirmed |
| 2 | What file in the C:\Windows\NTDS\ directory contains the AD database? | `ntds.dit` | Verified & Confirmed |
| 3 | Which Mimikatz command exports DPAPI Vault credentials? | `vault::cred /export` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Process verification in Task Manager or PowerShell on `WRK`:
   ```powershell
   Get-Process lsass | Select-Object Id, ProcessName, Path
   ```
   *Output:* `lsass` running from `C:\Windows\System32\lsass.exe`.
2. Active Directory file check on Domain Controller:
   ```cmd
   dir C:\Windows\NTDS\ntds.dit
   ```
   *Output:* Confirms presence of `ntds.dit` JET database.
3. Mimikatz command syntax confirmation: Executing `mimikatz # vault::cred /export` decrypts credential blobs from the Credential Vault.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Windows credential stores exist in layers of permanence: LSASS is ephemeral (in-memory, lost upon reboot), SAM and LSA Secrets are local host on-disk stores, DPAPI binds secrets to user encryption keys, and NTDS.dit is the permanent enterprise-wide directory database. Knowing which store to target depends directly on the level of privilege acquired and whether the target is a workstation or a Domain Controller.
