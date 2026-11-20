# Additional Notes & Deep Technical Dive: Windows Privilege Escalation

## 1. Windows Access Control Model & Token Architecture

The Windows operating system enforces authorization through Security Identifiers (SIDs) and Access Tokens generated during logon authentication:

```
+-----------------------------------------------------------------------+
|                    WINDOWS ACCESS TOKEN ANATOMY                       |
+-----------------------------------------------------------------------+
| User SID              | Identifies the account (e.g. S-1-5-21-...-1001) |
+-----------------------+-----------------------------------------------+
| Group SIDs            | Lists group memberships (e.g. S-1-5-32-544)   |
+-----------------------+-----------------------------------------------+
| Token Privileges      | Specific exemptions (e.g. SeBackupPrivilege)  |
+-----------------------+-----------------------------------------------+
| Integrity Level       | Untrusted, Low, Medium, High, SYSTEM          |
+-----------------------+-----------------------------------------------+
| Token Type            | Primary Token (process) vs Impersonation      |
+-----------------------------------------------------------------------+
```

```
[ Logon Authentication ] ──> LSASS generates Primary Access Token
                                       │
                                       v
               Process inherits Token with Integrity Level:
               - Standard User Process: Medium Integrity
               - Elevated Admin Process: High Integrity
               - Operating System Kernel / Service: SYSTEM Integrity
```

---

## 2. Service Control Manager (SCM) & Path Resolution

When the Service Control Manager starts a service, it invokes the underlying `CreateProcess` Windows API:

```
Service Definition:
BINARY_PATH_NAME: C:\Program Files\Vendor Software\Sub Dir\service.exe
```

```
+-----------------------------------------------------------------------+
|                 UNQUOTED SERVICE PATH RESOLUTION FLOW                 |
+-----------------------------------------------------------------------+
| SCM attempts to interpret spaces as argument delimiters:              |
|                                                                       |
| 1. Checks: `C:\Program.exe` [with arguments: Files\Vendor...]         |
|    - If exists & executable ──> Spawns `Program.exe` as SERVICE USER! |
|                                                                       |
| 2. Checks: `C:\Program Files\Vendor.exe` [with arguments: Software..] |
|    - If exists & executable ──> Spawns `Vendor.exe` as SERVICE USER!  |
|                                                                       |
| 3. Checks: `C:\Program Files\Vendor Software\Sub.exe` [with args...]  |
|                                                                       |
| 4. Finally executes:                                                  |
|    `C:\Program Files\Vendor Software\Sub Dir\service.exe`             |
+-----------------------------------------------------------------------+
```

### Defense:
Always enclose paths containing spaces in double quotes:
```cmd
sc config "MyService" binPath= "\"C:\Program Files\Vendor Software\service.exe\""
```

---

## 3. Windows Token Privileges (Priv2Admin Deep Dive)

Certain user privileges bypass standard DACL restrictions and can be converted into local administrative access:

| Privilege | Intended Administrative Purpose | Exploitation Pathway to Administrator / SYSTEM |
| :--- | :--- | :--- |
| **`SeBackupPrivilege`** | Allows backup software to read files bypassing read DACLs | Dump `SAM` and `SYSTEM` registry hives (`reg save hklm\sam`), extract NTLM hashes, Pass-the-Hash |
| **`SeRestorePrivilege`** | Allows restore software to overwrite any file | Overwrite service executables or DLLs in `C:\Windows\System32` |
| **`SeTakeOwnershipPrivilege`**| Allows administrators to seize ownership of orphaned files | Take ownership of `Utilman.exe` or services, grant `(F)` access, replace with `cmd.exe` |
| **`SeImpersonatePrivilege`** | Allows service accounts to impersonate connecting clients | Named pipe / RPC reflection (RogueWinRM, PrintSpoofer, GodPotato) to capture and impersonate SYSTEM token |

---

## 4. AlwaysInstallElevated Policy Mechanics

When `AlwaysInstallElevated` is enabled via Group Policy, the Windows Installer service (`msiexec.exe`) executes MSI installation scripts under the context of `NT AUTHORITY\SYSTEM`:

```
[ Unprivileged User ]
         │
         │ Executes: `msiexec /quiet /qn /i payload.msi`
         v
[ Windows Installer Service (msiexec.exe) ]
         │ (Detects AlwaysInstallElevated=1 in both HKLM and HKCU)
         │ Runs CustomAction embedded inside MSI with SYSTEM privileges
         v
[ SYSTEM Shell Spawned ]
```

---

## 5. Local Credential Storage Breakdown

```
Locations of Stored Plaintext / Cached Credentials:
├── Filesystem:
│    ├── C:\Unattend.xml (WDS install answer files)
│    ├── C:\Windows\Panther\Unattend.xml
│    ├── C:\inetpub\wwwroot\web.config (IIS connection strings)
│    └── %APPDATA%\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
├── Windows Credential Manager:
│    └── `cmdkey /list` (Usable directly via `runas /savecred`)
└── Windows Registry:
     ├── HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon (DefaultPassword)
     ├── HKCU\Software\SimonTatham\PuTTY\Sessions (Stored proxy passwords)
     └── HKLM\SAM & HKLM\SYSTEM (Dumping password hashes)
```
