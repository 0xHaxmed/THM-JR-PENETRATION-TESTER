# Task 05: Categories of Misconfiguration

---

## Stage A: Original Task Content & Briefing Summary

Host misconfigurations that lead to privilege escalation fall into six fundamental technical categories:

### 1. User and Group Configurations:
* **Excessive Group Memberships:** Adding standard users to privileged groups (e.g., the `docker` group on Linux allows trivial root escalation by mounting `/` into a root container; the `wheel` or `sudo` groups on Linux grant full root privileges).
* **Service Accounts:** Running background daemons under root or `LocalSystem` when a restricted service account would suffice.
* **Default / Unused Accounts:** Failure to disable built-in default accounts or enforce strong account lockout policies.

### 2. File and Directory Permissions:
* **Linux SUID / SGID Binaries:** The **Set User ID (SUID)** permission bit (octal `4000`, represented as `-rwsr-xr-x`) causes an executable to run with the privileges of the file's **owner** (typically `root`) rather than the executing user. If an SUID binary can be coerced into executing sub-shells (e.g. GTFOBins vectors like `find -exec`, `vim`, or custom binaries), the user gains immediate root access.
* **World-Writable Files:** Sensitive scripts or executables executed by root with write permissions granted to everyone (`0777`).
* **World-Readable Sensitive Files:** Configuration files, SSH private keys (`id_rsa`), or `/etc/shadow` readable by standard users.
* **Windows Insecure ACLs:** Low-privilege users possessing `FILE_ALL_ACCESS` or `FILE_WRITE_DATA` permissions on directories in the system `PATH` or `C:\Program Files`.

### 3. Service Configurations:
* **Insecure Service Binaries:** Services where an attacker can replace the underlying `.exe` or script.
* **Windows Unquoted Service Paths:** Service binary paths containing spaces without quotation marks (e.g. `C:\Program Files\My App\service.exe`). Windows sequentially searches for `C:\Program.exe`, `C:\Program Files\My.exe`, before reaching the legitimate binary. An attacker with write permissions to `C:\` can place a malicious `Program.exe` to achieve SYSTEM privilege.

### 4. Scheduled Tasks and Cron Jobs:
* **Privileged Scheduled Execution:** Root cron jobs or Windows Scheduled Tasks running scripts that unprivileged users can modify.
* **Wildcard Injection:** Using wildcards in privileged cron jobs (e.g. `tar cf /backup/archive.tar *` executed as root) can be abused using command-line argument injection flags (`--checkpoint=1`, `--checkpoint-action=exec=sh shell.sh`).

### 5. Credential Storage:
* **Linux Credential Trails:** Passwords stored in plaintext in `~/.bash_history`, environment variables, web application database connection strings (`wp-config.php`, `.env`), or readable configuration files.
* **Windows Credential Stores:** Saved credentials in Windows Credential Manager (`cmdkey /list`), `runas /savecred`, unattended setup answer files (`Unattend.xml`, `sysprep.xml`), and cleartext registry autologon keys.

### 6. Network Configuration:
* **Localhost-Only Services (127.0.0.1):** High-privilege administrative interfaces or databases bound to loopback interfaces that become accessible once a local foothold is established.
* **Overly Broad Bindings (0.0.0.0):** Exposing internal management services without authentication.

---

## Stage B: Independent Thinking & OpSec Considerations

* **The SUID Security Trade-Off:** The SUID mechanism was designed legitimately to allow unprivileged users to perform necessary privileged tasks (e.g. `passwd` needs to write to `/etc/shadow`, `ping` needs raw sockets). However, applying SUID to general-purpose scripting interpreters (Python, Bash, Perl) or utilities with shell escape features (`vi`, `find`, `nmap`) completely destroys system security boundaries.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Review the Linux special permission bits discussed in the briefing.
* **Tier 2 Hint:** What is the 4-letter acronym for Set User ID?
* **Tier 3 Hint:** The permission bit is `SUID`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Linux SUID Permission Analysis:**
   In Linux permissions:
   * **SUID (Set User ID):** Indicated by an `s` in the user execute bit (`-rwsr-xr-x`). When executed, the operating system kernel sets the process Effective User ID (EUID) to that of the file owner (`root`).
   * **SGID (Set Group ID):** Indicated by an `s` in the group execute bit (`-rwxr-sr-x`). The process runs with the Effective Group ID (EGID) of the file group.
   * **Sticky Bit:** Indicated by a `t` in the other execute bit (`drwxrwxrwt`). Prevents non-owners from deleting files in shared directories like `/tmp`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What special permission bit on Linux causes an executable to run with the privileges of the file's owner?
  * **Answer:** `SUID`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed `SUID` against the completed TryHackMe account.
* Verified that SUID binaries execute under the EUID of the file owner.

---

## Stage G: Key Educational Takeaway

The SUID permission bit causes executables to run under the owner's privilege context, making SUID binaries one of the most prolific and reliable Linux privilege escalation vectors when coupled with GTFOBins techniques.
