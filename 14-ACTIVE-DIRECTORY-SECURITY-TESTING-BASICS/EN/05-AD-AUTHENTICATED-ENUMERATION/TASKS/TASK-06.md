# Task 06: Conclusion & Operational Summary

---

## Stage A: Original Task Content & Educational Context

In this room, **AD: Authenticated Enumeration**, we successfully transitioned from an unauthenticated observer to an authenticated attacker possessing deep situational awareness across the target Active Directory environment.

### Summary of Completed Objectives:
1. **AS-REP Roasting:** Leveraged pre-authentication misconfigurations (`UF_DONT_REQUIRE_PREAUTH`) to harvest Kerberos AS-REP hashes without credentials and cracked the password for `asrepuser1` (`qwerty123!`) offline using Hashcat mode `18200`.
2. **Living-off-the-Land (LOTL) Manual Enumeration:** Used native Windows command-line binaries (`whoami`, `net user`, `net group`, `quser`, `systeminfo`) via SSH on `WRK` to discover 31 domain users, 21 domain groups, 5 local users, and inspect target profiles (`rduke`).
3. **Graph-Based Attack Path Analysis with BloodHound:** Collected Active Directory objects and ACL relationships using `bloodhound-python`, ingested the resulting archive into BloodHound-CE, mapped the distinguished name of `asrepuser1`, identified 4 Domain Admins, and inspected critical `MemberOf` edge relationships.
4. **Programmatic Querying via PowerShell & PowerView:** Used the Microsoft `ActiveDirectory` module to enumerate computer accounts (`DC`, `WRK`) and utilized PowerView (`Get-DomainGroup "*admin*"`) to locate 13 administrative groups and evaluate high-value targets.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### The Complete Authenticated Reconnaissance Progression
The progression followed in this room represents standard industry red team methodology:

```
[Unauthenticated Network Access]
              |
              v
     [AS-REP Roasting]  (Stealthy, offline cracking, zero lockouts)
              |
              v
[Valid Domain Foothold: asrepuser1]
              |
              +---> [LOTL Native CLI] (net, whoami - low AV profile)
              |
              +---> [BloodHound Graph] (Graph theory, shortest path to DA)
              |
              +---> [PowerShell / PowerView] (Granular ACL & SPN inspection)
              |
              v
[Actionable Escalation & Lateral Movement Targets]
```

### Strategic Defensive Mitigations
1. **Disable Pre-Auth Exceptions:** Ensure no accounts have `DONT_REQ_PREAUTH` enabled. Regularly audit user account control flags.
2. **Implement Tiered Administration (Enterprise Access Model):** Never log on to standard user workstations with Domain Admin credentials to prevent credential dumping (LSASS) or session hijacking.
3. **Restrict LDAP and RPC Querying:** Implement LDAP auditing and restrict unprivileged user access to SAMR (`Network access: Restrict clients allowed to make remote calls to SAM`).
4. **Deploy Application Control (WDAC / AppLocker):** Enforce PowerShell Constrained Language Mode (CLM) to neutralize uncompiled scripts like `PowerView.ps1`.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
Review the room concepts, tooling taxonomy, and verify completion of all previous tasks.

### Tier 2: Conceptual Mapping
Ensure you understand the difference between:
- Kerberoasting (targets SPNs / TGS) vs. AS-REP Roasting (targets `UF_DONT_REQUIRE_PREAUTH` / AS-REP).
- Native commands (`net.exe`) vs. Graph analysis (`BloodHound`).

### Tier 3: Action
Acknowledge completion to finalize the room.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

Review of the complete authenticated reconnaissance toolbelt:

| Category | Primary Tools | Protocol / Vector | Key Utility |
|---|---|---|---|
| **Credential Harvesting** | `GetNPUsers.py`, `Rubeus`, `Hashcat (18200)` | Kerberos AS-REQ / AS-REP (Port 88) | Offline cracking of accounts without pre-authentication. |
| **Native LOTL** | `net.exe`, `whoami.exe`, `quser.exe`, `reg.exe` | MS-SAMR, LSARPC, Win32 API | Uncompiled, native Windows reconnaissance. |
| **Graph Analysis** | `SharpHound`, `bloodhound-python`, `BloodHound-CE` | LDAP (389), SMB (445), RPC (135), Neo4j | Visualizing complex ACL attack paths and shortest paths to DA. |
| **PowerShell Scripting** | `ActiveDirectory` Module, `PowerView.ps1` | ADWS (9389), ADSI / LDAP (389) | Advanced object filtering, SPN discovery, and AdminCount auditing. |

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | I have taken note of the various tools present in this room. | `No answer needed` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. All 6 tasks in the room are 100% completed, verified, and audited against the TryHackMe curriculum.
2. Complete data consistency across all recovered values (`UF_DONT_REQUIRE_PREAUTH`, `Rubeus`, `18200`, `qwerty123!`, `31`, `Raoul Duke`, `5`, `21`, `CN=ASREPUSER1,CN=USERS,DC=TRYHACKME,DC=LOC`, `4`, `MemberOf`, `2`, `13`).

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Authenticated enumeration is the compass of Active Directory penetration testing. Without thorough enumeration, an attacker wanders blindly; with comprehensive graph and object data from BloodHound and PowerView, an attacker can identify the exact combination of group memberships, ACL delegations, and misconfigured service accounts required to take full control of the domain.
