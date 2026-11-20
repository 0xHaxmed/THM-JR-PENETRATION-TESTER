# Tasks Overview: Module 14 — Room 02: Intro to AD Authentication

---

## Room Metadata
* **Room Name:** Intro to AD Authentication
* **Canonical Slug:** `introtoactivedirectoryauthentication`
* **URL:** [TryHackMe Intro to AD Authentication](https://tryhackme.com/room/introtoactivedirectoryauthentication)
* **Module:** Module 14 — Active Directory Security Testing Basics
* **Tasks Total:** 7 Tasks
* **Questions Total:** 13 Questions
* **Verification Flags:** 6 Verified Lab Flags (`SHARE1` to `SHARE6`)
* **Status:** 100% Completed & Verified

---

## Master Solutions Matrix

| Task # | Task Title | Question # | Question Text | Verified Solution / Key Value | Solution Type |
| :---: | :--- | :---: | :--- | :--- | :--- |
| **01** | Introduction | Q1 | I'm ready to learn about AD authentication! | `No answer needed` | Informational |
| **02** | Authentication in AD | Q1 | What is the process called that proves your identity? | `Authentication` | Technical Term |
| **02** | Authentication in AD | Q2 | What is the process called that determines what you are allowed to access? | `Authorisation` | Technical Term |
| **03** | NetNTLM Authentication | Q1 | In NetNTLM, does the client authenticate directly to the domain controller or to the service they want to access? | `Service` | Technical Concept |
| **03** | NetNTLM Authentication | Q2 | What is the name of the random value that the server sends to the client during NetNTLM authentication? | `Challenge` | Technical Term |
| **03** | NetNTLM Authentication | Q3 | What is the value of Flag 1 that you can recover from `SHARE1`? | `THM{5cbcc61a-3178-4220-88b4-367c1bbb48e7}` | Lab Flag |
| **04** | Kerberos Authentication | Q1 | What is the name of the ticket issued by the KDC that allows you to request service tickets? | `Ticket Granting Ticket` | Technical Term |
| **04** | Kerberos Authentication | Q2 | What special account's password hash is used to encrypt all TGTs? | `krbtgt` | Account Name |
| **04** | Kerberos Authentication | Q3 | What environment variable is used to specify the location of a Kerberos credential cache file on Linux? | `KRB5CCNAME` | Environment Variable |
| **04** | Kerberos Authentication | Q4 | What is the value of Flag 2 that you can recover from `SHARE2` using Kerberos authentication? | `THM{0d3f818a-427a-425c-a451-55e43b83e876}` | Lab Flag |
| **05** | Weaknesses in AD Authentication | Q1 | What is the value of Flag 3 that you can recover from `SHARE3` by cracking the hash? | `THM{0eef8df3-c8ea-41ad-acd7-ae0479b2badf}` | Lab Flag |
| **05** | Weaknesses in AD Authentication | Q2 | What is the value of Flag 4 that you can recover from `SHARE4` by performing a Pass-the-Hash attack? | `THM{284c6735-b7c1-4221-b072-abf30a54eeda}` | Lab Flag |
| **05** | Weaknesses in AD Authentication | Q3 | What is the value of Flag 5 that you can recover from `SHARE5` by authenticating using the credentials recovered through the Kerberoast attack? | `THM{5b57e69d-7089-4282-ba04-c72de9bfdb38}` | Lab Flag |
| **05** | Weaknesses in AD Authentication | Q4 | What is the value of Flag 6 that you can recover from `SHARE6` by creating a Golden Ticket? | `THM{eac75729-86ea-4bab-98de-1c5ce3552f67}` | Lab Flag |
| **06** | Detections & Mitigations | Q1 | Which Event ID is logged when a Kerberos TGT is requested? | `4768` | Event ID |
| **06** | Detections & Mitigations | Q2 | In a Pass-the-Hash attack detected via Event ID 4624, what value appears in the Authentication Package field? | `NTLM` | Telemetry Value |
| **07** | Conclusion | Q1 | I understand AD authentication and am ready to dive deeper into NTLM attacks! | `No answer needed` | Informational |

---

## Detailed Task Documentation Links
* [Task 01: Introduction](../../02-INTRO-TO-AD-AUTHENTICATION/TASKS/TASK-01.md)
* [Task 02: Authentication in AD](../../02-INTRO-TO-AD-AUTHENTICATION/TASKS/TASK-02.md)
* [Task 03: NetNTLM Authentication](../../02-INTRO-TO-AD-AUTHENTICATION/TASKS/TASK-03.md)
* [Task 04: Kerberos Authentication](../../02-INTRO-TO-AD-AUTHENTICATION/TASKS/TASK-04.md)
* [Task 05: Weaknesses in AD Authentication](../../02-INTRO-TO-AD-AUTHENTICATION/TASKS/TASK-05.md)
* [Task 06: Detections & Mitigations](../../02-INTRO-TO-AD-AUTHENTICATION/TASKS/TASK-06.md)
* [Task 07: Conclusion](../../02-INTRO-TO-AD-AUTHENTICATION/TASKS/TASK-07.md)
