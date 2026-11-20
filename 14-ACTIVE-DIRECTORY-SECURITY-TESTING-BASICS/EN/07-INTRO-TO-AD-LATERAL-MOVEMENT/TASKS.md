# Tasks Overview: Intro to AD Lateral Movement

This document summarizes all tasks, questions, and verified solutions for the **Intro to AD Lateral Movement** room (`introductiontoadlateralmovement`) in the Active Directory Security Testing Basics module.

---

## Tasks & Questions Matrix

| Task # | Task Title | Question # | Question Text | Verified Solution | Verification Status |
|:---:|---|:---:|---|---|:---:|
| **01** | Introduction | 1 | I'm ready to move laterally! | `No answer needed` | Verified |
| **02** | What is Lateral Movement? | 1 | What technique allows you to authenticate using an NTLM hash without knowing the plaintext password? | `Pass-the-Hash` | Verified |
| **02** | What is Lateral Movement? | 2 | What Windows group, other than Administrators, permits WinRM access to a host? | `Remote Management Users` | Verified |
| **03** | Remote Execution Methods | 1 | What Event ID in the System log is the primary indicator of PsExec-based lateral movement? | `7045` | Verified |
| **03** | Remote Execution Methods | 2 | What is the flag found in flag3a.txt on WRK? | `THM{ps3x3c_syst3m_sh3ll}` | Verified |
| **03** | Remote Execution Methods | 3 | What is the NT hash found in the loot file on WRK? | `fa0af7f6a73316dd59f0be812dbf3c12` | Verified |
| **03** | Remote Execution Methods | 4 | What is the flag found in flag3b.txt on SERVER1? | `THM{w1nrm_r3m0t3_sh3ll}` | Verified |
| **04** | Pass-the-Hash and Credential Reuse | 1 | What flag is used with NetExec to authenticate against the local SAM database instead of the domain? | `--local-auth` | Verified |
| **04** | Pass-the-Hash and Credential Reuse | 2 | What is the flag found in flag4.txt on SERVER1? | `THM{p4ss_th3_h4sh_ftw}` | Verified |
| **04** | Pass-the-Hash and Credential Reuse | 3 | What is the NT hash of the Domain Administrator account found on SERVER1? | `2508e1ce9cfcfe1011a74c34297b05ea` | Verified |
| **05** | Pivot! Pivoooot! Piiiivot! | 1 | What Nmap flag must you use instead of -sS when scanning through a SOCKS proxy? | `-sT` | Verified |
| **05** | Pivot! Pivoooot! Piiiivot! | 2 | What is the flag found in flag5.txt on the Domain Controller? | `THM{d0m41n_c0mpr0m1s3d_v1a_p1v0t}` | Verified |
| **06** | Mitigations | 1 | What built-in Windows feature automatically generates unique local admin passwords for each domain-joined machine? | `Windows LAPS` | Verified |
| **06** | Mitigations | 2 | What Windows Event ID in the System log indicates a new service has been installed? | `7045` | Verified |
| **07** | Conclusion | 1 | I have completed the Introduction to AD Lateral Movement room! | `No answer needed` | Verified |

---

## Detailed Task Documentation Links

- [Task 01: Introduction](../../07-INTRO-TO-AD-LATERAL-MOVEMENT/TASKS/TASK-01.md)
- [Task 02: What is Lateral Movement?](../../07-INTRO-TO-AD-LATERAL-MOVEMENT/TASKS/TASK-02.md)
- [Task 03: Remote Execution Methods](../../07-INTRO-TO-AD-LATERAL-MOVEMENT/TASKS/TASK-03.md)
- [Task 04: Pass-the-Hash and Credential Reuse](../../07-INTRO-TO-AD-LATERAL-MOVEMENT/TASKS/TASK-04.md)
- [Task 05: Pivot! Pivoooot! Piiiivot!](../../07-INTRO-TO-AD-LATERAL-MOVEMENT/TASKS/TASK-05.md)
- [Task 06: Mitigations](../../07-INTRO-TO-AD-LATERAL-MOVEMENT/TASKS/TASK-06.md)
- [Task 07: Conclusion](../../07-INTRO-TO-AD-LATERAL-MOVEMENT/TASKS/TASK-07.md)
