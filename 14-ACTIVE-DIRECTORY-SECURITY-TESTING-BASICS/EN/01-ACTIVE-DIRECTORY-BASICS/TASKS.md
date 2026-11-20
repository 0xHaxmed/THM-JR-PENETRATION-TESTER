# Tasks Overview: Module 14 — Room 01: Active Directory Basics

---

## Room Metadata
* **Room Name:** Active Directory Basics
* **Canonical Slug:** `winadbasicsrevamp` (formerly `activedirectorybasics`)
* **URL:** [TryHackMe Active Directory Basics](https://tryhackme.com/room/winadbasicsrevamp)
* **Module:** Module 14 — Active Directory Security Testing Basics
* **Tasks Total:** 12 Tasks
* **Questions Total:** 15 Questions
* **Verification Flags:** 5 Verified Lab Flags
* **Status:** 100% Completed & Verified

---

## Master Solutions Matrix

| Task # | Task Title | Question # | Question Text | Verified Solution / Key Value | Solution Type |
| :---: | :--- | :---: | :--- | :--- | :--- |
| **01** | Introduction | Q1 | I am ready to learn about Active Directory! | `No answer needed` | Informational |
| **02** | Windows Domains | Q1 | What is the name of the server that runs Active Directory services? | `Domain Controller` | Technical Term |
| **03** | Active Directory | Q1 | Which group normally administers all computers and resources in a domain? | `Domain Admins` | Security Principal |
| **03** | Active Directory | Q2 | What would be the name of the machine account associated with a machine named TOM-PC? | `TOM-PC$` | Technical Term |
| **04** | Authentication Methods | Q1 | When referring to Kerberos, what type of ticket allows us to request further tickets known as TGS? | `Ticket Granting Ticket` | Technical Term |
| **04** | Authentication Methods | Q2 | When using NetNTLM, is a user's password transmitted over the network at any point? (yay/nay) | `nay` | Boolean / String |
| **05** | Trees, Forests and Trusts | Q1 | What is a group of Windows domains that share the same namespace called? | `Tree` | Technical Term |
| **06** | Creating a New Domain | Q1 | Verify TBM Domain Promotion flag (`C:\flag1.txt` on CDC1) | `THM{caf78a39-1e8b-4ec2-8eaf-e38a42590d1b}` | Lab Flag |
| **07** | Managing Users in AD | Q1 | What is the fourth department as listed in the People OU? | `Marketing` | Object Name |
| **07** | Managing Users in AD | Q2 | How many bankers are currently servicing private clients? | `11` | Integer Count |
| **08** | Domain-Joining a Computer | Q1 | Verify Server1 Domain Joining flag (`C:\flag2.txt` on Server1) | `THM{941c5c55-f17a-422f-b7c3-7bb2386f187e}` | Lab Flag |
| **09** | Managing Computers in AD | Q1 | After organising the available computers, how many ended up in the Workstations OU? | `3` | Integer Count |
| **10** | Group Policies | Q1 | What is the name of the network share used to distribute GPOs to domain machines? | `sysvol` | Share Name |
| **10** | Group Policies | Q2 | Verify Servers OU GPO flag (`C:\flag3.txt` on Server1) | `THM{2d8fa7ec-c925-425d-bbd3-ba02f03219f5}` | Lab Flag |
| **11** | Foreign Forest Trust | Q1 | Verify TVM Foreign Trust (Claire) flag (`C:\flag4.txt` on Server2) | `THM{3ad3060c-429e-42e0-98ca-318b3227c742}` | Lab Flag |
| **11** | Foreign Forest Trust | Q2 | Verify TVM Foreign Trust (alice.king) flag (`C:\flag5.txt` on Server1) | `THM{2d159874-69b3-4778-b9f9-13b047fa83a2}` | Lab Flag |
| **12** | Conclusion | Q1 | I have successfully completed the room! | `No answer needed` | Informational |

---

## Detailed Task Documentation Links
* [Task 01: Introduction](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-01.md)
* [Task 02: Windows Domains](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-02.md)
* [Task 03: Active Directory](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-03.md)
* [Task 04: Authentication Methods](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-04.md)
* [Task 05: Trees, Forests and Trusts](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-05.md)
* [Task 06: Creating a New Domain](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-06.md)
* [Task 07: Managing Users in AD](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-07.md)
* [Task 08: Domain-Joining a Computer](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-08.md)
* [Task 09: Managing Computers in AD](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-09.md)
* [Task 10: Group Policies](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-10.md)
* [Task 11: Foreign Forest Trust](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-11.md)
* [Task 12: Conclusion](../../01-ACTIVE-DIRECTORY-BASICS/TASKS/TASK-12.md)
