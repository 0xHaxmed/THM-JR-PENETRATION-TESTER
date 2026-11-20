# Additional Technical Notes: Module 14 Recap — Active Directory Security Testing Basics

---

## 1. Cryptographic Analysis: NT Hash vs. Net-NTLMv2 Challenge-Response

The distinction between the stored NT hash and the on-the-wire Net-NTLMv2 challenge-response is the cornerstone of Windows credential exploitation:

```
                      NT HASH vs. Net-NTLMv2 ARCHITECTURE
  ┌───────────────────────────────┬───────────────────────────────┐
  │         NT / NTLM HASH        │        Net-NTLMv2 HASH        │
  ├───────────────────────────────┼───────────────────────────────┤
  │ Algorithm:                    │ Algorithm:                    │
  │ MD4(UTF-16LE(Password))       │ HMAC-MD5(NT_Hash, User||Realm)│
  │                               │ iterated with Server/Client   │
  │                               │ Challenges and Nonces.        │
  ├───────────────────────────────┼───────────────────────────────┤
  │ Where It Resides:             │ Where It Resides:             │
  │ • SAM Registry Hive           │ • Transmitted over network    │
  │ • LSASS Process Memory        │   wire during SMB/HTTP/RPC    │
  │ • NTDS.dit Database           │   authentication handshakes.  │
  ├───────────────────────────────┼───────────────────────────────┤
  │ Attack Primitives:            │ Attack Primitives:            │
  │ • Pass-the-Hash (PtH)         │ • Offline dictionary cracking │
  │ • Overpass-the-Hash (PtK)     │   (Hashcat Mode 5600)         │
  │ • Silver Ticket Generation    │ • NTLM Relay (ntlmrelayx.py)  │
  │ • Golden Ticket (krbtgt hash) │ • CANNOT BE PASSED (PtH fails)│
  └───────────────────────────────┴───────────────────────────────┘
```

### The Net-NTLMv2 Mathematical Protocol Flow:
1. **Type 1 (Negotiate):** Client connects to server announcing supported NTLM capabilities.
2. **Type 2 (Challenge):** Server generates a random 8-byte nonce ($\text{ServerChallenge}$) and sends it to the client.
3. **Type 3 (Authenticate):** The client derives:
   $$\text{ResponseKeyNT} = \text{HMAC-MD5}\left(\text{Key}=\text{NT\_Hash}, \; \text{Data}=\text{Uppercase}(\text{Username}) \parallel \text{Uppercase}(\text{Domain})\right)$$
   $$\text{NTProofStr} = \text{HMAC-MD5}\left(\text{Key}=\text{ResponseKeyNT}, \; \text{Data}=\text{ServerChallenge} \parallel \text{ClientChallenge}\right)$$
   The client transmits $\text{NTProofStr}$ along with the variable-length client blob.
4. **Why PtH Fails on Net-NTLMv2:** Because $\text{ServerChallenge}$ is chosen dynamically by the target server on each connection, an intercepted Net-NTLMv2 response cannot be replayed or passed to a different target; the mathematical verification will fail.

---

## 2. Git Architecture & Secret Leakage Remediation

In Task 4 of *Intro to AD Breaching*, credentials were recovered from historical Git repository commits.

### The Immutable Object Model:
- A Git repository consists of content-addressed objects stored in `.git/objects/`:
  - **Blobs:** Store raw file contents.
  - **Trees:** Store directory structures and file metadata pointing to blobs.
  - **Commits:** Point to a top-level tree and one or more parent commit hashes.
- Modifying a file in commit $C_2$ does not alter commit $C_1$. Commit $C_1$ remains stored in the object database as long as it is reachable from refs, tags, or reflogs.

### Enterprise Secret Remediation Protocol:
```bash
# 1. Rotate the exposed credential immediately in production.
# 2. Rewrite history using git-filter-repo (Python-based standard):
pip install git-filter-repo
git filter-repo --replace-text <(echo 'regex:Password123!==>REDACTED')

# 3. Force push to all remote branches and tags:
git push origin --force --all
git push origin --force --tags

# 4. Expire and purge reflogs:
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

---

## 3. Active Directory Protocol Reference: LDAP vs. Kerberos vs. SMB

Understanding which protocol handles which domain service is vital for network traffic analysis and red team tool selection:

| Protocol | Default Port(s) | Architectural Function in Active Directory | Common Abuse Vectors |
|---|---|---|---|
| **LDAP / LDAPS** | TCP 389 / 636 | Directory querying, object attribute search, group memberships, schema inspection, user account control enumeration. | Unauthenticated LDAP reconnaissance, LDAP Passback, BloodHound data collection. |
| **Kerberos v5** | TCP/UDP 88 | Centralized domain authentication service (KDC), ticket granting (AS-REQ/AS-REP, TGS-REQ/TGS-REP). | AS-REP Roasting, Kerberoasting, Kerbrute user enumeration, S4U delegation abuse, Golden/Silver tickets. |
| **SMB** | TCP 445 | Network file sharing, named pipe inter-process communication (IPC$), Group Policy distribution (SYSVOL/NETLOGON). | Null/Guest share enumeration, PsExec remote execution, NTLM relaying. |
| **RPC / MS-SAMR** | TCP 135 + dynamic | Remote procedure calls for account management, security descriptor inspection, service control. | RID Cycling / RID Brute-forcing, Service creation via SCM. |
| **WinRM / WSMAN** | TCP 5985 / 5986 | Web Services-Management protocol for PowerShell remote administration. | Remote command execution, credential spraying. |

---

## 4. Hashcat Kerberos Modes Comprehensive Reference

| Hashcat Mode | Protocol Name | Encrypted Component | Target Account Type |
|---|---|---|---|
| **`18200`** | Kerberos 5, etype 23, AS-REP | Encrypted timestamp/session key in `AS-REP` | Domain user with `UF_DONT_REQUIRE_PREAUTH` enabled. |
| **`13100`** | Kerberos 5, etype 23, TGS-REP | Encrypted service ticket in `TGS-REP` | Service accounts with Service Principal Names (`servicePrincipalName`). |
| **`7500`** | Kerberos 5, etype 23, AS-REQ Pre-Auth | Encrypted timestamp in `AS-REQ` padata | Standard domain user authenticating over the network. |
| **`19600`** | Kerberos 5, etype 17, TGS-REP (AES-128) | Service ticket encrypted with AES-128 | Hardened service accounts using modern encryption. |
| **`19700`** | Kerberos 5, etype 18, TGS-REP (AES-256) | Service ticket encrypted with AES-256 | High-security enterprise service accounts. |
| **`1000`** | NTLM | Raw MD4 hash of UTF-16LE password | Local SAM database, NTDS.dit, LSASS memory dumps. |
| **`5600`** | Net-NTLMv2 | HMAC-MD5 challenge-response artifact | Hashes captured over the wire via Responder. |

---

## 5. Relative Identifier (RID) Taxonomy (MS-DTYP)

Active Directory SIDs follow the format $\text{S-1-5-21}-\langle\text{Domain ID}\rangle-\langle\text{RID}\rangle$. Well-known RIDs are architecturally reserved by Microsoft:

| Relative Identifier (RID) | Target Principal / Group | Security Context / Role |
|---|---|---|
| **500** | `Administrator` | Built-in domain/local root administrative account. |
| **501** | `Guest` | Built-in guest account (disabled by default). |
| **502** | `krbtgt` | Key Distribution Center service account (signs TGTs). |
| **512** | `Domain Admins` | Global group with full administrative control over the domain. |
| **513** | `Domain Users` | Global group containing all standard domain accounts. |
| **514** | `Domain Guests` | Global group for guest accounts. |
| **515** | `Domain Computers` | Global group containing all member workstations and servers. |
| **516** | `Domain Controllers` | Global group containing all Domain Controller computer objects. |
| **517** | `Cert Publishers` | Members can publish digital certificates to Active Directory. |
| **518** | `Schema Admins` | Can modify the Active Directory schema definition. |
| **519** | `Enterprise Admins` | Full administrative control across the entire forest. |
| **$\ge$ 1000** | User-Defined Objects | Custom user accounts, service accounts, and security groups. |

---

## 6. Linux Kerberos Credential Cache Architecture (`KRB5CCNAME`)

In Linux environments (MIT Kerberos v5 / Heimdal), Kerberos credentials are saved in a binary cache file (`ccache` format).

### Environment Variable Syntax:
```bash
# Point to a specific ticket cache file:
export KRB5CCNAME=/tmp/administrator.ccache

# Point to in-memory kernel keyring (Linux keyutils):
export KRB5CCNAME=KEYRING:persistent:1000
```

### Inspecting and Using the Credential Cache:
```bash
# Inspect cached tickets, client/server principals, validity, and encryption types:
klist

# Execute Pass-the-Ticket using Impacket:
psexec.py -k -no-pass thm.loc/Administrator@DC.thm.loc
secretsdump.py -k -no-pass thm.loc/Administrator@DC.thm.loc
```
Because the `-k` flag instructs Impacket to inspect the `KRB5CCNAME` cache, the authentication occurs completely via Kerberos without sending NTLM hashes or plaintext passwords across the network.
