# Tasks Overview & Solutions Matrix: Windows Privilege Escalation

## Room Information
- **Room Name:** Windows Privilege Escalation
- **Slug:** `windowsprivesc20`
- **Module:** Module 13 — Privilege Escalation
- **Path:** Jr Penetration Tester
- **Total Tasks:** 9
- **Total Questions:** 15
- **Platform URL:** https://tryhackme.com/room/windowsprivesc20

---

## Tasks Index

| Task # | Task Title | Questions Count | Solved / Verified | Link |
| :---: | :--- | :---: | :---: | :---: |
| **01** | Introduction | 1 | 1/1 | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-01.md) |
| **02** | Windows Privilege Escalation | 2 | 2/2 | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-02.md) |
| **03** | Harvesting Passwords from Usual Spots | 4 | 4/4 | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-03.md) |
| **04** | Other Quick Wins | 1 | 1/1 | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-04.md) |
| **05** | Abusing Service Misconfigurations | 3 | 3/3 | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-05.md) |
| **06** | Abusing dangerous privileges | 1 | 1/1 | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-06.md) |
| **07** | Abusing vulnerable software | 1 | 1/1 | [TASK-07.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-07.md) |
| **08** | Tools of the Trade | 1 | 1/1 | [TASK-08.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-08.md) |
| **09** | Conclusion | 1 | 1/1 | [TASK-09.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/05-WINDOWS-PRIVILEGE-ESCALATION/TASKS/TASK-09.md) |

---

## Verified Solutions Matrix

| Task | Q# | Question Text | Verified Answer | Answer Type |
| :---: | :---: | :--- | :--- | :---: |
| **Task 01** | 1 | Click and continue learning! | `No answer needed` | Confirmation |
| **Task 02** | 1 | Users that can change system configurations are part of which group? | `Administrators` | Group Name |
| **Task 02** | 2 | The SYSTEM account has more privileges than the Administrator user (aye/nay) | `aye` | Affirmative String |
| **Task 03** | 1 | A password for the julia.jones user has been left on the Powershell history. What is the password? | `ZuperCkretPa5z` | Cleartext Password |
| **Task 03** | 2 | A web server is running on the remote host. Find any interesting password on web.config files associated with IIS. What is the password of the db_admin user? | `098n0x35skjD3` | Cleartext Password |
| **Task 03** | 3 | There is a saved password on your Windows credentials. Using cmdkey and runas, spawn a shell for mike.katz and retrieve the flag from his desktop. | `THM{WHAT_IS_MY_PASSWORD}` | Flag String |
| **Task 03** | 4 | Retrieve the saved password stored in the saved PuTTY session under your profile. What is the password for the thom.smith user? | `CoolPass2021` | Cleartext Password |
| **Task 04** | 1 | What is the taskusr1 flag? | `THM{TASK_COMPLETED}` | Flag String |
| **Task 05** | 1 | Get the flag on svcusr1's desktop. | `THM{AT_YOUR_SERVICE}` | Flag String |
| **Task 05** | 2 | Get the flag on svcusr2's desktop. | `THM{QUOTES_EVERYWHERE}` | Flag String |
| **Task 05** | 3 | Get the flag on the Administrator's desktop. | `THM{INSECURE_SVC_CONFIG}` | Flag String |
| **Task 06** | 1 | Get the flag on the Administrator's desktop. | `THM{SEFLAGPRIVILEGE}` | Flag String |
| **Task 07** | 1 | Get the flag on the Administrator's desktop. | `THM{EZ_DLL_PROXY_4ME}` | Flag String |
| **Task 08** | 1 | Click and continue learning! | `No answer needed` | Confirmation |
| **Task 09** | 1 | Click and continue learning! | `No answer needed` | Confirmation |
