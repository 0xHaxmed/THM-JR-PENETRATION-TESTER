# Task 04: Domain Enumeration

---

## Stage A: Original Task Content & Briefing Summary

Task 04 covers advanced unauthenticated Active Directory domain enumeration techniques across **LDAP Anonymous Binds**, **MSRPC Null Sessions** via `rpcclient`, **Relative Identifier (RID) Cycling**, and **Kerberos User Enumeration** with `kerbrute`.

### 1. Anonymous LDAP Queries (`ldapsearch`):
* Checking if the Domain Controller permits anonymous queries:
  ```bash
  ldapsearch -x -H ldap://10.211.11.10 -s base
  ```
* Querying all domain objects matching `(objectClass=person)`:
  ```bash
  ldapsearch -x -H ldap://10.211.11.10 -b "dc=tryhackme,dc=loc" "(objectClass=person)"
  ```
* Parsing output for user `rduke`:
  * `sAMAccountName`: `rduke`
  * `cn` / `displayName`: `Raoul Duke`
  * `memberOf`: `CN=Domain Users,CN=Users,DC=tryhackme,DC=loc`

### 2. MSRPC Null Sessions via `rpcclient`:
* Connecting over SMB Named Pipe `\PIPE\samr` anonymously:
  ```bash
  rpcclient -U "" 10.211.11.10 -N
  ```
* Commands within `rpcclient`:
  * `enumdomusers`: Lists all domain users along with their hexadecimal Relative Identifiers (RIDs).
  * `queryuser <RID>`: Returns detailed user attributes (Full Name, Home Directory, Logon Hours, Password Last Set, Group Memberships).

### 3. Understanding Relative Identifiers (RIDs) & RID Cycling:
* In Windows, every domain object has a Security Identifier (SID):
  $$\text{SID} = \text{S-1-5-21-DomainIdentifier-RID}$$
* Standard Built-In RIDs:
  * `500`: Administrator
  * `501`: Guest
  * `512`: Domain Admins
  * `513`: Domain Users
  * `514`: Domain Guests
* Custom user accounts, service accounts, and computers created by administrators start sequentially at RID `1000` and above.
* **RID Cycling via Bash Loop:**
  * To interrogate a specific decimal RID (e.g. `1634`), convert to hex or query directly:
    ```bash
    for i in $(seq 500 2000); do
        echo "queryuser $i" | rpcclient -U "" -N 10.211.11.10 2>/dev/null | grep -i "User Name"
    done
    ```
  * Querying RID `1634` (hex `0x662`) reveals username: **`katie.thomas`**.

### 4. Kerbrute Pre-Authentication Validation:
* Rapidly confirming discovered accounts without risking lockouts:
  ```bash
  ./kerbrute userenum --dc 10.211.11.10 -d tryhackme.loc users.txt
  ```
* Confirms 28 valid active domain users from the provided wordlist.

---

## Stage B: Independent Thinking & OpSec Considerations

* **RID Cycling as a Universal Discovery Primitive:**
  * Even when anonymous LDAP is disabled and SMB shares are blocked, if the IPC$ null session permits connecting to the `samr` (Security Account Manager Remote) RPC pipe, an attacker can iterate through RIDs from 500 to 10,000 to enumerate every user and group in the domain.
  * In modern environments, setting `RestrictAnonymous = 1` or `2` in the registry restricts anonymous callers from listing account names.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `ldapsearch` with `objectClass=person` to inspect user `rduke`.
* **Tier 2 Hint:** Use `rpcclient` to query user details for `rduke` and search for RID `1634`. Note that `1634` decimal equals `0x662` in hexadecimal.
* **Tier 3 Hint:** User `rduke` belongs to `Domain Users` and his full name is `Raoul Duke`. Querying RID `1634` yields `katie.thomas`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Query LDAP for User `rduke`:**
   ```bash
   ldapsearch -x -H ldap://10.211.11.10 -b "dc=tryhackme,dc=loc" "(sAMAccountName=rduke)"
   ```
   * Extract fields:
     * `cn: Raoul Duke`
     * `displayName: Raoul Duke`
     * `memberOf: CN=Domain Users,CN=Users,DC=tryhackme,DC=loc`
   * Group membership: **`Domain Users`**.
   * Full name: **`Raoul Duke`**.

2. **Interrogate RID 1634 via `rpcclient`:**
   * Launch `rpcclient`:
     ```bash
     rpcclient -U "" 10.211.11.10 -N
     ```
   * Execute `queryuser`:
     ```text
     rpcclient $> queryuser 1634
             User Name   :   katie.thomas
             Full Name   :   Katie Thomas
             Home Drive  :   
             Dir Drive   :   
             Profile Path:   
             Logon Script:   
             Description :   
             Workstations:   
             Comment     :   
             CB Password :   0
             Privs       :   0
             Account Flags:  0x00000010
     ```
   * Associated username: **`katie.thomas`**.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What group is the user rduke part of?
  * **Answer:** `Domain Users`
* **Question 2:** What is this user's full name?
  * **Answer:** `Raoul Duke`
* **Question 3:** Which username is associated with RID 1634?
  * **Answer:** `katie.thomas`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `Domain Users`, `Raoul Duke`, and `katie.thomas`.
* Validated LDAP search attributes and RPC samr user query responses.

---

## Stage G: Key Educational Takeaway

Unauthenticated domain enumeration leverages directory and RPC protocols to catalog accounts and group memberships. Understanding RID cycling and anonymous LDAP binds allows penetration testers to construct exhaustive domain user rosters from zero access.
