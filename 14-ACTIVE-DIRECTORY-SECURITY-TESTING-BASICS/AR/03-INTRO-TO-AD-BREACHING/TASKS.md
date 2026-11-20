# Tasks Overview: Module 14 — Room 03: Intro to AD Breaching

---

## Room Metadata
* **Room Name:** Intro to AD Breaching
* **Canonical Slug:** `introductiontoactivedirectorybreaching` (formerly `introadbreaching`)
* **URL:** [TryHackMe Intro to AD Breaching](https://tryhackme.com/room/introductiontoactivedirectorybreaching)
* **Module:** Module 14 — Active Directory Security Testing Basics
* **Tasks Total:** 8 Tasks
* **Questions Total:** 12 Questions
* **Status:** 100% Completed & Verified

---

## Master Solutions Matrix

| Task # | Task Title | Question # | Question Text | Verified Solution / Key Value | Solution Type |
| :---: | :--- | :---: | :--- | :--- | :--- |
| **01** | Introduction | Q1 | I'm ready to breach AD! | `No answer needed` | Informational |
| **02** | Active Directory Breaches | Q1 | What is the first phase of any AD attack chain? | `Breaching` | Methodology Term |
| **02** | Active Directory Breaches | Q2 | What service, running on TCP port 88, can be abused to validate whether usernames exist in the domain? | `Kerberos` | Protocol Name |
| **03** | OSINT and Target Reconnaissance | Q1 | How many valid usernames did Kerbrute discover? | `42` | Integer Count |
| **03** | OSINT and Target Reconnaissance | Q2 | What is the organisation's username format? | `first.last` | Naming Convention |
| **04** | Credential Discovery | Q1 | What is the password for the svc.jenkins account found in the Git commit history? | `Jen5k1ns2025!` | Hardcoded Secret |
| **04** | Credential Discovery | Q2 | What default password was leaked in the Jenkins build logs? | `MegaCorp01!` | Leaked Password |
| **05** | Username Enumeration & Password Spraying | Q1 | How many accounts were cracked using the brute force attack? | `2` | Integer Count |
| **05** | Username Enumeration & Password Spraying | Q2 | Which is the first user account (alphabetically) that uses the default onboarding password? | `alice.moore` | Username |
| **06** | Coercion Attacks | Q1 | What is the Bind DN of the service account captured during the LDAP passback attack? | `CN=svc.ldap,OU=Service Accounts,DC=thm,DC=loc` | Distinguished Name |
| **06** | Coercion Attacks | Q2 | What is the plaintext password captured from the LDAP passback? | `Pr1ntBind2025!` | Plaintext Secret |
| **06** | Coercion Attacks | Q3 | What is the cracked password for sarah.jones obtained through file-based coercion? | `Trustno1` | Cracked Password |
| **07** | Mitigations | Q1 | What Group Policy setting can be configured to enforce NTLMv2 and refuse older LM and NTLM responses? | `Network Security: LAN Manager authentication level` | GPO Setting Name |
| **07** | Mitigations | Q2 | What port should be used instead of port 389 to ensure LDAP traffic is encrypted? | `636` | Port Number |
| **08** | Conclusion | Q1 | I have completed the Introduction to AD Breaching room! | `No answer needed` | Informational |

---

## Detailed Task Documentation Links
* [Task 01: Introduction](../../03-INTRO-TO-AD-BREACHING/TASKS/TASK-01.md)
* [Task 02: Active Directory Breaches](../../03-INTRO-TO-AD-BREACHING/TASKS/TASK-02.md)
* [Task 03: OSINT and Target Reconnaissance](../../03-INTRO-TO-AD-BREACHING/TASKS/TASK-03.md)
* [Task 04: Credential Discovery](../../03-INTRO-TO-AD-BREACHING/TASKS/TASK-04.md)
* [Task 05: Username Enumeration and Password Spraying](../../03-INTRO-TO-AD-BREACHING/TASKS/TASK-05.md)
* [Task 06: Coercion Attacks](../../03-INTRO-TO-AD-BREACHING/TASKS/TASK-06.md)
* [Task 07: Mitigations](../../03-INTRO-TO-AD-BREACHING/TASKS/TASK-07.md)
* [Task 08: Conclusion](../../03-INTRO-TO-AD-BREACHING/TASKS/TASK-08.md)
