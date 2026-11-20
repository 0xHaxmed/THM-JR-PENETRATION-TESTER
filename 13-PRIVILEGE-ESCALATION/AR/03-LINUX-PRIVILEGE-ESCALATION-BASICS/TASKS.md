# Tasks Overview & Solutions Matrix: Linux Privilege Escalation: Basics

## Room Information
- **Room Name:** Linux Privilege Escalation: Basics
- **Slug:** `linprivbasics`
- **Module:** Module 13 — Privilege Escalation
- **Path:** Jr Penetration Tester
- **Total Tasks:** 8
- **Total Questions:** 15
- **Platform URL:** https://tryhackme.com/room/linprivbasics

---

## Tasks Index

| Task # | Task Title | Questions Count | Solved / Verified | Link |
| :---: | :--- | :---: | :---: | :---: |
| **01** | Introduction | 1 | 1/1 | [TASK-01.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/03-LINUX-PRIVILEGE-ESCALATION-BASICS/TASKS/TASK-01.md) |
| **02** | Privilege Escalation: Sudo | 2 | 2/2 | [TASK-02.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/03-LINUX-PRIVILEGE-ESCALATION-BASICS/TASKS/TASK-02.md) |
| **03** | Privilege Escalation: SUID | 2 | 2/2 | [TASK-03.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/03-LINUX-PRIVILEGE-ESCALATION-BASICS/TASKS/TASK-03.md) |
| **04** | Privilege Escalation: PATH | 3 | 3/3 | [TASK-04.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/03-LINUX-PRIVILEGE-ESCALATION-BASICS/TASKS/TASK-04.md) |
| **05** | Privilege Escalation: Capabilities | 3 | 3/3 | [TASK-05.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/03-LINUX-PRIVILEGE-ESCALATION-BASICS/TASKS/TASK-05.md) |
| **06** | Privilege Escalation: Cron Jobs | 2 | 2/2 | [TASK-06.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/03-LINUX-PRIVILEGE-ESCALATION-BASICS/TASKS/TASK-06.md) |
| **07** | Privilege Escalation: NFS | 2 | 2/2 | [TASK-07.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/03-LINUX-PRIVILEGE-ESCALATION-BASICS/TASKS/TASK-07.md) |
| **08** | Conclusion | 1 | 1/1 | [TASK-08.md](file:///e:/THM-JR-PENETRATION-TESTER/13-PRIVILEGE-ESCALATION/03-LINUX-PRIVILEGE-ESCALATION-BASICS/TASKS/TASK-08.md) |

---

## Verified Solutions Matrix

| Task | Q# | Question Text | Verified Answer | Answer Type |
| :---: | :---: | :--- | :--- | :---: |
| **Task 01** | 1 | Ready to escalate privileges! | `No answer needed` | Confirmation |
| **Task 02** | 1 | What is the full path of the program that john can run with sudo? | `/usr/bin/nano` | Absolute Binary Path |
| **Task 02** | 2 | What are the contents of /root/flag.txt? | `THM{SUDO-pwned-priv-esc}` | Flag String |
| **Task 03** | 1 | What is the full path of the binary in /usr/bin/ that is vulnerable to a SUID exploitation? | `/usr/bin/vim.basic` | Absolute Binary Path |
| **Task 03** | 2 | What are the contents of the flag found in /root/? | `THM{root-by-SUID-vulns}` | Flag String |
| **Task 04** | 1 | Find a custom SUID binary on the target host, using the techniques you learned before. What is the full path of this binary? | `/opt/path/mywhoami` | Absolute Binary Path |
| **Task 04** | 2 | Run the strings command on the SUID binary. What binary does it call from path? | `whoami` | System Command String |
| **Task 04** | 3 | Exploit this behavior as you've seen in the example scenario, to hijack the PATH and gain root privileges. What are the contents of /root/flag.txt? | `THM{PATH-and-SUID-leadtoroot}` | Flag String |
| **Task 05** | 1 | How many binaries have set capabilities? | `6` | Integer Count |
| **Task 05** | 2 | What is the full path of the binary that can be used to gain root through its capabilities? | `/usr/bin/python3.12` | Absolute Binary Path |
| **Task 05** | 3 | What are the contents of /root/flag.txt? | `THM{caps_getting_r00T}` | Flag String |
| **Task 06** | 1 | What is the full path of the cron job that can be exploited? | `/etc/cron.d/cleanup` | Absolute File Path |
| **Task 06** | 2 | What are the contents of /root/flag.txt? | `THM{g0t-r00t-from-cr0n}` | Flag String |
| **Task 07** | 1 | What is the full path of the mountable share? | `/opt/nfs` | Absolute Directory Path |
| **Task 07** | 2 | What are the contents of /root/flag.txt? | `THM{exports-r00T-nfs}` | Flag String |
| **Task 08** | 1 | Done with the basics! | `No answer needed` | Confirmation |
