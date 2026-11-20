# Tasks Overview: AD: Authenticated Enumeration

This document summarizes all tasks, questions, and verified solutions for the **AD: Authenticated Enumeration** room (`adauthenticatedenumeration`) in the Active Directory Security Testing Basics module.

---

## Tasks & Questions Matrix

| Task # | Task Title | Question # | Question Text | Verified Solution | Verification Status |
|:---:|---|:---:|---|---|:---:|
| **01** | Introduction | 1 | I am ready to begin exploring authenticated enumeration. | `No answer needed` | Verified |
| **02** | AS-REP Roasting | 1 | What flag must be set on an AD account for it to be vulnerable to AS-REP Roasting? | `UF_DONT_REQUIRE_PREAUTH` | Verified |
| **02** | AS-REP Roasting | 2 | Which tool automatically identifies roastable users without needing a username list? | `Rubeus` | Verified |
| **02** | AS-REP Roasting | 3 | What is the Hashcat mode used to crack AS-REP hashes? | `18200` | Verified |
| **02** | AS-REP Roasting | 4 | What is the password of the user asrepuser1? | `qwerty123!` | Verified |
| **03** | Manual Enumeration | 1 | How many domain user accounts are there? | `31` | Verified |
| **03** | Manual Enumeration | 2 | What is the full name of the user rduke? | `Raoul Duke` | Verified |
| **03** | Manual Enumeration | 3 | How many local user accounts are there on the WRK machine? | `5` | Verified |
| **03** | Manual Enumeration | 4 | How many domain groups are there? | `21` | Verified |
| **04** | Enumeration With BloodHound | 1 | What is the distinguishedName value of the asrepuser1 account? | `CN=ASREPUSER1,CN=USERS,DC=TRYHACKME,DC=LOC` | Verified |
| **04** | Enumeration With BloodHound | 2 | According to the "All Domain Admins" query, how many users are part of the Domain Admins group? | `4` | Verified |
| **04** | Enumeration With BloodHound | 3 | What is the type of relationship (edge) between the DRGONZ0 account and the DOMAIN ADMINS group? | `MemberOf` | Verified |
| **05** | Enumeration With PowerShell’s ActiveDirectory and PowerView Modules | 1 | How many computer accounts were you able to find? | `2` | Verified |
| **05** | Enumeration With PowerShell’s ActiveDirectory and PowerView Modules | 2 | How many groups did Get-DomainGroup "*admin*" return? | `13` | Verified |
| **06** | Conclusion | 1 | I have taken note of the various tools present in this room. | `No answer needed` | Verified |

---

## Detailed Task Documentation Links

- [Task 01: Introduction](../../05-AD-AUTHENTICATED-ENUMERATION/TASKS/TASK-01.md)
- [Task 02: AS-REP Roasting](../../05-AD-AUTHENTICATED-ENUMERATION/TASKS/TASK-02.md)
- [Task 03: Manual Living-off-the-Land Enumeration](../../05-AD-AUTHENTICATED-ENUMERATION/TASKS/TASK-03.md)
- [Task 04: Enumeration With BloodHound](../../05-AD-AUTHENTICATED-ENUMERATION/TASKS/TASK-04.md)
- [Task 05: Enumeration With PowerShell’s ActiveDirectory and PowerView Modules](../../05-AD-AUTHENTICATED-ENUMERATION/TASKS/TASK-05.md)
- [Task 06: Conclusion](../../05-AD-AUTHENTICATED-ENUMERATION/TASKS/TASK-06.md)
