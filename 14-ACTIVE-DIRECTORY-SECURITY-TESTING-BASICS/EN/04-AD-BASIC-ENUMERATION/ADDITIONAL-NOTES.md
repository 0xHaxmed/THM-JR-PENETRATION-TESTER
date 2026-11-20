# Additional Technical Notes: AD: Basic Enumeration

---

## 1. Deep Dive: MSRPC Named Pipes & The SAMR Interface

Active Directory environments expose Remote Procedure Call (RPC) interfaces over SMB named pipes (operating over TCP port 445 via `\\<IP>\IPC$`).

### The SAMR & LSARPC Interfaces:
1. **Security Account Manager Remote (SAMR) Protocol:**
   * UUID: `12345778-1234-abcd-ef00-0123456789ac` (Pipe: `\PIPE\samr`).
   * Designed to administer local and domain account databases.
   * When an unauthenticated client opens an anonymous connection (`IPC$`), standard operations like `SamrEnumerateUsersInDomain` or `SamrQueryInformationUser` can be invoked unless explicit registry restrictions are enforced (`RestrictAnonymous = 1` or `2` in `HKLM\SYSTEM\CurrentControlSet\Control\Lsa`).
2. **Relative Identifier (RID) Cycling Mechanics:**
   * In Windows security descriptors, every user, group, and machine object has a unique SID ending in a 32-bit integer: the Relative Identifier (RID).
   * Because RIDs are allocated sequentially (starting at 1000 for custom domain objects), an attacker simply iterates through integers (e.g., 1000 to 5000), issuing `SamrQueryInformationUser` to resolve every RID to its corresponding username, comment, and account flags.

### Hexadecimal vs. Decimal RID Translation:
* `rpcclient` command `enumdomusers` returns RIDs in hexadecimal format:
  * Example: `user:[katie.thomas] rid:[0x662]`
* Converting `0x662` to decimal:
  $$0\text{x}662 = (6 \times 16^2) + (6 \times 16^1) + (2 \times 16^0) = 1536 + 96 + 2 = 1634$$
* Therefore, RID `1634` corresponds directly to `katie.thomas`.

---

## 2. Anonymous LDAP Bind Protocol Internals

### LDAP Search Scope & Filter Architecture:
When interacting with Active Directory LDAP via `ldapsearch`:
* `-x`: Use Simple Authentication (no SASL).
* `-s base`: Base level search (reads root DSE attributes to discover domain naming contexts).
* `-b "dc=tryhackme,dc=loc"`: Defines the search base DN.
* Filters:
  * `(objectClass=person)`: Returns users, contacts, and computer objects.
  * `(objectCategory=user)`: Restricts results strictly to user accounts.
  * `(sAMAccountName=*)`: Ensures accounts have valid NetBIOS logon names.

### The RootDSE Discovery:
Querying the base object (`-s base -b ""`) discloses:
* `defaultNamingContext`: The primary domain partition (e.g. `DC=tryhackme,DC=loc`).
* `dnsHostName`: Fully qualified domain controller name (`DC.tryhackme.loc`).
* `forestFunctionality`: Forest functional level (e.g. `7` = Windows Server 2016/2019).
* `domainFunctionality`: Domain functional level.

---

## 3. Password Policy Attribute Matrix

When dumping domain password policies via NetExec or `rpcclient` (`getdompwinfo`):

| Policy Parameter | Windows AD Attribute | Security Implication |
| :--- | :--- | :--- |
| **Minimum Password Length** | `minPwdLength` | If $< 12$, vulnerable to dictionary attacks and offline brute forcing |
| **Password History Length** | `pwdHistoryLength` | Number of previous passwords remembered; prevents immediate reuse |
| **Password Complexity** | `pwdProperties` | Bit 0: requires 3 of 4 character categories (Upper, Lower, Digit, Special) |
| **Lockout Threshold** | `lockoutThreshold` | Number of failed attempts before account locks (`0` = no lockout) |
| **Lockout Duration** | `lockoutDuration` | Duration an account remains locked (`0` = requires administrator unlock) |
| **Lockout Observation Window** | `lockoutObservationWindow` | Time sliding window after which the failed attempt counter resets |
