# Additional Technical Notes: AD: Authenticated Enumeration

---

## 1. Low-Level Kerberos Protocol Mechanics: AS-REP Roasting vs. Kerberoasting

To understand authenticated reconnaissance and credential extraction in Active Directory, an operator must differentiate between the two primary offline Kerberos ticket extraction vectors:

| Feature / Metric | AS-REP Roasting | Kerberoasting |
|---|---|---|
| **Kerberos Packet** | `AS-REP` (Authentication Service Response) | `TGS-REP` (Ticket Granting Service Response) |
| **Vulnerable Condition** | Account has `UF_DONT_REQUIRE_PREAUTH` enabled (`0x00400000`). | Account has a Service Principal Name (`servicePrincipalName`) registered. |
| **Credentials Required to Launch** | **None** (Only valid username required). | **Valid Domain User Credentials** (Required to request a TGT and subsequent TGS). |
| **Target Key Used for Encryption** | The **requesting user's** password hash. | The **target service account's** password hash. |
| **Hashcat Mode** | Mode **`18200`** (`Kerberos 5, etype 23, AS-REP`) | Mode **`13100`** (`Kerberos 5, etype 23, TGS-REP`) |
| **Encrypted Component** | `enc-part` of the `AS-REP` packet. | `enc-part` of the `Ticket` within the `TGS-REP` packet. |

### ASN.1 Protocol Dissection: AS-REQ without Pre-Authentication
Under RFC 4120, an `AS-REQ` normally contains:
```asn1
AS-REQ ::= [APPLICATION 10] SEQUENCE {
    pvno            [0] INTEGER (5),
    msg-type        [1] INTEGER (10),
    padata          [2] SEQUENCE OF PA-DATA OPTIONAL, -- PA-ENC-TIMESTAMP (type 2)
    req-body        [3] KDC-REQ-BODY
}
```
When `UF_DONT_REQUIRE_PREAUTH` is set, the client sends an `AS-REQ` omitting `padata` or sending an empty padata sequence. The KDC detects that pre-authentication is not required and issues an `AS-REP`:
```asn1
AS-REP ::= [APPLICATION 11] SEQUENCE {
    pvno            [0] INTEGER (5),
    msg-type        [1] INTEGER (11),
    padata          [2] SEQUENCE OF PA-DATA OPTIONAL,
    crealm          [3] Realm,
    cname           [4] PrincipalName,
    ticket          [5] Ticket,
    enc-part        [6] EncryptedData -- ENCRYPTED WITH USER'S NT HASH (RC4-HMAC)
}
```
The `enc-part` contains the encrypted session key and nonce. Because the attacker knows the plaintext nonce and can derive the key from trial passwords, they can compute:

$$\text{HMAC-MD5}(\text{Key}, \text{Enc-Part}) \stackrel{?}{=} \text{Checksum}$$

allowing rapid offline verification at rates exceeding hundreds of millions of guesses per second on modern GPUs.

---

## 2. Active Directory UserAccountControl (UAC) Flag Mathematics

The `userAccountControl` attribute is a 32-bit bitmask stored as an integer in LDAP. Common flags include:

| Hexadecimal Flag | Decimal Value | Meaning |
|---|---|---|
| `0x0001` | 1 | `SCRIPT` (Logon script executed) |
| `0x0002` | 2 | `ACCOUNTDISABLE` (Account is disabled) |
| `0x0020` | 32 | `PASSWD_NOTREQD` (No password required) |
| `0x0200` | 512 | `NORMAL_ACCOUNT` (Standard domain user) |
| `0x10000` | 65536 | `DONT_EXPIRE_PASSWORD` (Password never expires) |
| `0x400000` | 4194304 | `DONT_REQ_PREAUTH` (`UF_DONT_REQUIRE_PREAUTH`) |

### Bitwise LDAP Search Filter
To search for all users with `DONT_REQ_PREAUTH` enabled, Active Directory supports bitwise LDAP filter matching rules using the Object Identifier (OID) `1.2.840.113556.1.4.803` (`LDAP_MATCHING_RULE_BIT_AND`):

```ldap
(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))
```
This filter allows an authenticated user or automated tool to query the Domain Controller and return **every single roastable account** in a single LDAP query.

---

## 3. BloodHound Graph Theory & Custom Cypher Cheat Sheet

BloodHound models Active Directory as a directed property graph $G = (V, E)$, where $V$ is the set of vertices (Nodes) and $E$ is the set of directed relationships (Edges).

### Essential Cypher Queries for Advanced Enumeration

#### 1. Shortest Path from Compromised User to Domain Admins:
```cypher
MATCH (u:User {name:'ASREPUSER1@TRYHACKME.LOC'})
MATCH (g:Group {name:'DOMAIN ADMINS@TRYHACKME.LOC'})
MATCH p=shortestPath((u)-[*1..]->(g))
RETURN p
```

#### 2. Find All Kerberoastable Users:
```cypher
MATCH (u:User)
WHERE u.hasspn = true
RETURN u.name, u.serviceprincipalnames
```

#### 3. Find Users with DCSync Rights:
DCSync rights correspond to having `GetChanges` and `GetChangesAll` extended rights on the Domain object:
```cypher
MATCH (u:User)-[r:GetChangesAll]->(d:Domain)
RETURN u.name
```

#### 4. Find Unconstrained Delegation Systems:
```cypher
MATCH (c:Computer {unconstraineddelegation: true})
RETURN c.name
```

---

## 4. Windows Token Privileges & Weaponization Matrix

When inspecting `whoami /priv`, several privileges present direct privilege escalation paths:

| Token Privilege | Standard Context | Exploitation Vector | Impact |
|---|---|---|---|
| `SeImpersonatePrivilege` | Service Accounts (`NETWORK SERVICE`, `LOCAL SERVICE`, IIS AppPools) | Named Pipe Impersonation via Potato family (`GodPotato`, `PrintSpoofer`, `SweetPotato`). | Escalate directly to `NT AUTHORITY\SYSTEM`. |
| `SeDebugPrivilege` | Local Administrators | Process handle opening via `OpenProcess(PROCESS_ALL_ACCESS)` $\rightarrow$ Dump `lsass.exe` using MiniDumpWriteDump. | Steal domain admin hashes/plaintext passwords from memory. |
| `SeBackupPrivilege` | Backup Operators | Bypass NTFS file system permissions via `FILE_FLAG_BACKUP_SEMANTICS`. | Read `ntds.dit`, `SAM`, `SYSTEM` hive, and arbitrary sensitive files. |
| `SeRestorePrivilege` | Backup Operators | Write arbitrary files to protected system directories. | Overwrite service binaries or DLL hijacking to execute code as SYSTEM. |
| `SeTakeOwnershipPrivilege` | Administrators | Take ownership of any securable object via `WRITE_OWNER` permission. | Modify DACLs to grant Full Control. |
