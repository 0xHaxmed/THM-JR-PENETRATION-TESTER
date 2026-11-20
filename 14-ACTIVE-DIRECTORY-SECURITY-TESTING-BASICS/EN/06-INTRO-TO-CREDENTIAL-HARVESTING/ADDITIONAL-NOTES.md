# Additional Technical Notes: Intro to Credential Harvesting

---

## 1. LSASS Architecture & Security Support Providers (SSPs)

The Local Security Authority Subsystem Service (`lsass.exe`) is a critical Windows user-mode process responsible for enforcing local security policies, user authentication, and generating access tokens. To support diverse authentication protocols, LSASS loads several **Security Support Providers (SSPs)** as Dynamic Link Libraries (DLLs):

| SSP Module | DLL Name | Credential Storage Behavior | Exploitation Vector |
|---|---|---|---|
| **MSV1_0** | `msv1_0.dll` | Manages local and domain NTLM authentication; caches NTLM hashes. | Dumps NTLM hashes for Pass-the-Hash attacks. |
| **Kerberos** | `kerberos.dll` | Manages Kerberos v5 tickets; stores TGTs, TGS tickets, and session keys in memory. | Extracts `.kirbi` tickets for Pass-the-Ticket or Overpass-the-Hash. |
| **WDigest** | `wdigest.dll` | Implements HTTP Digest authentication (RFC 2617). Historically cached plaintext passwords in memory. | By default disabled in Windows 8.1/Server 2012 R2, but can be forced via `UseLogonCredential=1` registry modification. |
| **CredSSP / TSPkg** | `tspkg.dll` | Used by Remote Desktop (RDP) Network Level Authentication (NLA); caches plaintext credentials temporarily. | Harvests RDP session credentials during active sessions. |

### Memory Dumping Mechanics (MiniDumpWriteDump)
When tools like Mimikatz or `procdump` interact with LSASS, they utilize the Win32 API function `MiniDumpWriteDump` from `comsvcs.dll` or `dbghelp.dll`:
```c
BOOL MiniDumpWriteDump(
  HANDLE                            hProcess,
  DWORD                             ProcessId,
  HANDLE                            hFile,
  MINIDUMP_TYPE                     DumpType, // MiniDumpWithFullMemory
  PMINIDUMP_EXCEPTION_INFORMATION   ExceptionParam,
  PMINIDUMP_USER_STREAM_INFORMATION UserStreamParam,
  PMINIDUMP_CALLBACK_INFORMATION    CallbackParam
);
```
**OpSec Signature:** Sysmon Event ID 10 alerts on `TargetImage: C:\Windows\System32\lsass.exe` with `GrantedAccess` including `0x1010` (`PROCESS_VM_READ | PROCESS_QUERY_INFORMATION`).

---

## 2. SAM & SYSTEM Hive BootKey Decryption Algorithm

Local user password hashes are stored in the `SAM` registry hive under `HKLM\SAM\SAM\Domains\Account\Users\<RID>`. The password hash is stored in the `V` value, but it is doubly encrypted:

1. **The BootKey (Syskey):** Stored across four keys in the `SYSTEM` hive (`HKLM\SYSTEM\CurrentControlSet\Control\Lsa\JD`, `GBG`, `Data`, `Skew1`). Each key stores a 4-byte fragment. The fragments are concatenated and scrambled to yield the 16-byte `BootKey`:
   $$\text{BootKey} = \text{Permute}(\text{JD} \parallel \text{GBG} \parallel \text{Data} \parallel \text{Skew1})$$
2. **The Hashed BootKey:** Used to decrypt the `F` value in `HKLM\SAM\SAM\Domains\Account` to recover the Domain Password Key.
3. **RID-Based Decryption:** For each user RID, the NTLM hash stored in `V` is decrypted using the derived key combined with the 4-byte user RID using DES or RC4/AES-128:
   $$\text{NTLM\_Hash} = \text{AES-Decrypt}(\text{Encrypted\_V}, \text{Key}(\text{BootKey}, \text{RID}))$$

Because the decryption key resides entirely in the `SYSTEM` hive, capturing both `SAM` and `SYSTEM` hives allows full offline hash extraction.

---

## 3. MS-Cache v2 (DCC2) Mathematical Specification

Windows caches domain credentials locally to enable logons when disconnected from the domain network. Domain Cached Credentials version 2 (DCC2 / MS-Cache v2) replaces legacy DCC1 (MD4-based) with a hardened key derivation function:

$$\text{DCC2} = \text{PBKDF2-HMAC-SHA1}\left( \text{Password}=\text{NTLM}(\text{pwd}), \; \text{Salt}=\text{lowercase}(\text{username}), \; \text{Iterations}=10240 \right)$$

### Key Properties:
- **Salt:** The username converted to lowercase UTF-16LE. Because the salt is unique per user, pre-computed rainbow tables cannot be used.
- **Iteration Count:** Enforces $10,240$ rounds of HMAC-SHA1.
- **Pass-the-Hash Impossibility:** Unlike NTLM hashes, DCC2 hashes **cannot** be used in Pass-the-Hash attacks. They can only be cracked offline into plaintext.

---

## 4. MS-DRSR (DRSUAPI) & DCSync Protocol Dissection

The DCSync attack exploits the legitimate Directory Replication Service Remote Protocol (MS-DRSR) over RPC interface UUID `e3514235-4b06-11d1-ab04-00c04fc2dcd2`.

### RPC Execution Flow:
1. **RPC Bind:** Attacker binds to the DRSUAPI RPC interface on port 135/dynamic RPC on the Domain Controller.
2. **`DsBindWithCred`:** Attacker authenticates using valid domain credentials possessing replication rights (`DS-Replication-Get-Changes-All`).
3. **`DsGetNCChanges` Request:** The attacker client sends an RPC request asking the Domain Controller for updates to a specific naming context (NC), targeting the user account (e.g., `krbtgt` or `Administrator`).
4. **Replication Response:** The Domain Controller formats the replication stream containing the directory object's attributes, including the user's NTLM hash (`unicodePwd`), Kerberos keys (AES-256, AES-128, RC4), and password history.

```
Attacker Client                                        Domain Controller (DC)
      |                                                          |
      | 1. Bind to DRSUAPI RPC (UUID: e3514235...)               |
      |--------------------------------------------------------->|
      |                                                          |
      | 2. DsBindWithCred(User="drgonzo")                        |
      |--------------------------------------------------------->|
      |<---------------------------------------------------------|
      |                                                          |
      | 3. DsGetNCChanges(Object="CN=Administrator,CN=Users...") |
      |--------------------------------------------------------->|
      |                                                          | [Verifies Get-Changes-All ACL]
      | 4. DsGetNCChanges Response (NTDS secrets, NTLM hash)     |
      |<---------------------------------------------------------|
```

---

## 5. Pass-the-Hash (PtH) Protocol Mechanics (MS-NLMP)

Pass-the-Hash works because of the core design of the Microsoft NTLM Authentication Protocol (MS-NLMP):

1. **Challenge Generation:** The server sends a random 8-byte challenge ($\text{ServerChallenge}$) to the client in the `Type 2` (Challenge) message.
2. **Response Calculation:** The client generates an 8-byte or 16-byte random client challenge ($\text{ClientChallenge}$). The client then computes:
   $$\text{NTProofStr} = \text{HMAC-MD5}\left(\text{Key}=\text{NTLM}(\text{pwd}), \; \text{Data}=\text{ServerChallenge} \parallel \text{ClientChallenge}\right)$$
   $$\text{Response} = \text{HMAC-MD5}\left(\text{Key}=\text{NTProofStr}, \; \text{Data}=\text{SessionBaseKey}\right)$$
3. **The Flaw:** The mathematical input to the HMAC function is solely the **NTLM hash** ($\text{NTLM}(\text{pwd})$). The plaintext password is never used in the cryptographic calculation.
4. Therefore, any tool that possesses the 32-character hexadecimal NTLM hash can complete the NTLM handshake without ever knowing the plaintext password.
