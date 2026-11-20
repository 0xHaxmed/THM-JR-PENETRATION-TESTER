# Tasks Overview & Solutions Matrix: Host & Server Configuration Reviews

## Room Information
- **Room Name:** Host & Server Configuration Reviews
- **Slug:** `hostserverconfigurationreviews`
- **Module:** Module 13 — Privilege Escalation
- **Path:** Jr Penetration Tester
- **Total Tasks:** 9
- **Total Questions:** 14
- **Platform URL:** https://tryhackme.com/room/hostserverconfigurationreviews

---

## Tasks Index

| Task # | Task Title | Questions Count | Solved / Verified | Link |
| :---: | :--- | :---: | :---: | :---: |
| **01** | Introduction | 1 | 1/1 | [TASK-01.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-01.md) |
| **02** | What are Configuration Reviews? | 1 | 1/1 | [TASK-02.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-02.md) |
| **03** | Industry Standards: CIS and STIGs | 1 | 1/1 | [TASK-03.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-03.md) |
| **04** | Automated Tools vs. Manual Reviews | 1 | 1/1 | [TASK-04.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-04.md) |
| **05** | Common Misconfigurations | 1 | 1/1 | [TASK-05.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-05.md) |
| **06** | The Enumeration Methodology | 1 | 1/1 | [TASK-06.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-06.md) |
| **07** | Evaluating Findings | 1 | 1/1 | [TASK-07.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-07.md) |
| **08** | Practical | 6 | 6/6 | [TASK-08.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-08.md) |
| **09** | Conclusion | 1 | 1/1 | [TASK-09.md](../../01-HOST-SERVER-CONFIGURATION-REVIEWS/TASKS/TASK-09.md) |

---

## Verified Solutions Matrix

| Task | Q# | Question Text | Verified Answer | Answer Type |
| :---: | :---: | :--- | :--- | :---: |
| **Task 01** | 1 | I understand the difference between vulnerability-based and configuration-based escalation. | `No answer needed` | Confirmation |
| **Task 02** | 1 | Will updating a Linux machine change its configuration to comply with the CIS benchmarks? (yea/nay) | `nay` | Boolean / Short Answer |
| **Task 03** | 1 | Which DISA STIG category will result in a complete compromise of the system? | `CAT I` | Standard Severity Identifier |
| **Task 04** | 1 | Which vulnerability scanner mentioned above can perform compliance checks? | `Nessus` | Tool Name |
| **Task 05** | 1 | What type of file permissions allow a user to execute a binary with the permissions of the file owner? | `SUID` | Linux Permission Mode |
| **Task 06** | 1 | What phase of the enumeration process focuses on understanding the system environment? | `situational awareness` | Methodology Phase |
| **Task 07** | 1 | I understand how to evaluate misconfigurations. | `No answer needed` | Confirmation |
| **Task 08** | 1 | What were the permissions for the /etc/shadow file on linux-web-01? | `0644` | Octal Permission String |
| **Task 08** | 2 | How many files were found with world-writable permissions on linux-web-01? | `3` | Integer Count |
| **Task 08** | 3 | What was the name of the file found with SUID permissions on linux-web-01? | `/opt/admin-tools/logviewer` | Absolute File Path |
| **Task 08** | 4 | What was the minimum password length configured on win-dc-01? | `8` | Numeric Value |
| **Task 08** | 5 | What was the registry value for the FilterAdministratorToken policy on win-dc-01? | `0x0` | Hexadecimal Registry Value |
| **Task 08** | 6 | How many checks passed successfully on linux-db-02? | `8` | Integer Count |
| **Task 09** | 1 | I am ready to move on to the next room! | `No answer needed` | Confirmation |
