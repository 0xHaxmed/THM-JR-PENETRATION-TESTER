# Task 03: OS Enumeration

---

## Stage A: Original Task Content & Briefing Summary

Task 03 focuses on gathering core operating system information. Understanding the underlying OS architecture, distribution, kernel version, installed software, and scheduled system maintenance tasks is the foundational step in identifying privilege escalation vectors.

### Key OS Enumeration Commands & Files:
1. `hostname`: Prints the system hostname, which often reveals the functional role of the server within the enterprise (e.g., `web-prod-01`, `db-backend`, `mail-relay`).
2. `uname -a`: Displays the complete system architecture, kernel name, network nodename, kernel release, kernel version, and hardware platform.
3. `/proc/version`: A virtual procfs file detailing the kernel compilation version, toolchain compiler (`gcc`), and linker.
4. `/etc/issue` and `/etc/os-release`: Standard distribution release files providing the friendly Linux OS release name and version.
5. `ps aux` and `ps axjf`: Lists running processes. `ps aux` displays user execution context, CPU/memory usage, and start times; `ps axjf` outputs an ASCII-art process execution tree, highlighting parent-child process relationships.
6. Scheduled Tasks (`cron`): Inspects `/etc/crontab`, `/etc/cron.*`, and `/var/spool/cron/crontabs/` for recurring automated jobs executed under privileged user accounts (`root`).
7. Package Managers (`dpkg -l` / `rpm -qa`): Queries installed packages and versions to cross-reference against known public CVE vulnerability databases.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Kernel Exploits as a Last Resort:** While discovering kernel release `6.8.0-1017-aws` allows searching for kernel vulnerabilities, kernel exploits in production testing are high-risk operations. They frequently trigger kernel panics, corrupt memory, or cause system restarts that drop existing shells. Always exhaust configuration-based vectors (cron jobs, sudo rights, file permissions) before attempting kernel-level exploitation.
* **Cron Job Discovery:** When inspecting `/etc/crontab`, pay close attention to jobs executing every 5 minutes (`*/5 * * * *`). If the script being executed or its containing directory has permissive write permissions, any low-privileged user can modify the script to gain root execution when cron fires.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `hostname` for Q1, `uname -r` for Q2, and `cat /etc/issue` or `lsb_release -a` for Q3.
* **Tier 2 Hint:** View `/etc/crontab` to find the script scheduled every 5 minutes. Query the package manager using `dpkg -l | grep apparmor` for Q5.
* **Tier 3 Hint:**
  * Q1 is `linux-enumeration`.
  * Q2 is `6.8.0-1017-aws`.
  * Q3 is `Ubuntu 24.04.1 LTS`.
  * Q4 is `/root/backup.sh`.
  * Q5 is `4.0.1really4.0.1-0ubuntu0.24.04.3`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Determining Hostname:
```bash
john@linux-enumeration:~$ hostname
linux-enumeration
```
* **Analysis:** The command outputs `linux-enumeration`.

### 2. Identifying Kernel Version:
```bash
john@linux-enumeration:~$ uname -r
6.8.0-1017-aws

john@linux-enumeration:~$ uname -a
Linux linux-enumeration 6.8.0-1017-aws #17-Ubuntu SMP PREEMPT_DYNAMIC Fri Sep 27 01:06:58 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
```
* **Analysis:** The exact kernel version string is `6.8.0-1017-aws`.

### 3. Identifying Ubuntu Distribution Release:
```bash
john@linux-enumeration:~$ cat /etc/issue
Ubuntu 24.04.1 LTS \n \l

john@linux-enumeration:~$ cat /etc/os-release
PRETTY_NAME="Ubuntu 24.04.1 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.1 LTS (Noble Numbat)"
```
* **Analysis:** The target is running `Ubuntu 24.04.1 LTS`.

### 4. Enumerating Scheduled Cron Jobs:
```bash
john@linux-enumeration:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }
47 6	* * 7	root	test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.weekly; }
52 6	1 * *	root	test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.monthly; }
*/5 *   * * *   root    /root/backup.sh
```
* **Analysis:** The line `*/5 * * * * root /root/backup.sh` indicates that `/root/backup.sh` is executed as the `root` user every 5 minutes. The full path is `/root/backup.sh`.

### 5. Querying AppArmor Package Version:
```bash
john@linux-enumeration:~$ dpkg -l | grep apparmor
ii  apparmor                             4.0.1really4.0.1-0ubuntu0.24.04.3   amd64        user-space parser utility for AppArmor
ii  libapparmor1:amd64                   4.0.1really4.0.1-0ubuntu0.24.04.3   amd64        changehat AppArmor library
```
* **Analysis:** The full package version string returned by `dpkg` is `4.0.1really4.0.1-0ubuntu0.24.04.3`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the hostname of the target host?
  * **Answer:** `linux-enumeration`
* **Question 2:** What is the Linux kernel version of the target host?
  * **Answer:** `6.8.0-1017-aws`
* **Question 3:** What version of Ubuntu is running on the host?
  * **Answer:** `Ubuntu 24.04.1 LTS`
* **Question 4:** What is the full path of the script run by root every 5 minutes?
  * **Answer:** `/root/backup.sh`
* **Question 5:** What is the full version of AppArmor?
  * **Answer:** `4.0.1really4.0.1-0ubuntu0.24.04.3`

---

## Stage F: Practical Verification & Proof of Work

* Executed `hostname`, `uname -r`, `cat /etc/issue`, `cat /etc/crontab`, and `dpkg -l | grep apparmor` on the live target VM.
* Confirmed exact string matches for all 5 questions.

---

## Stage G: Key Educational Takeaway

Operating system enumeration provides the structural skeleton of your privilege escalation strategy. Recognizing the kernel version guides exploit research, while enumerating system crontabs uncovers scheduled administrative automation that can serve as direct privilege escalation bridges.
