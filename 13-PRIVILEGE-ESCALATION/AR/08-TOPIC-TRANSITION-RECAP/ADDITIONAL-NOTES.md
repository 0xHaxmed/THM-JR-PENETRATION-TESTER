# Additional Notes: Module 13 Topic Transition Recap

---

## Technical Context & Deep Architectural Analysis

### 1. Comparative Privilege Matrix: Linux vs. Windows

| Dimension | Linux (POSIX Model) | Windows (NT Security Model) |
| :--- | :--- | :--- |
| **Highest Authority** | `root` (UID 0, GID 0) | `NT AUTHORITY\SYSTEM` (S-1-5-18) |
| **Delegation Framework** | Sudoers (`/etc/sudoers`) | User Account Control (UAC) / Group Policies (GPO) |
| **Access Control Model** | DAC (Permissions: rwx, SUID, SGID, Sticky) + POSIX ACLs | DACLs / SACLs via Security Descriptors |
| **Fine-Grained Controls** | Linux Capabilities (`setcap`, `getcap`) | Token Privileges (`whoami /priv`) |
| **Service Execution** | systemd unit files, SysV init scripts | Service Control Manager (`services.exe`, `sc.exe`) |
| **Password Storage** | `/etc/shadow` (Root-readable only) | SAM registry hive (`%SystemRoot%\System32\config\SAM`) |
| **Automated Tasks** | Cron jobs (`/etc/crontab`, `/var/spool/cron`) | Windows Task Scheduler (`schtasks.exe`) |

---

## Operational Lessons from the Jump Challenges

1. **Linux Jump Challenge Takeaways:**
   * In multi-tier Linux challenges, reverse engineering custom SUID binaries (`strings`, `ltrace`, `strace`, `objdump -d`) is indispensable.
   * If a binary calls an external system binary without its absolute path, manipulating the `$PATH` variable allows instant local privilege escalation without needing memory corruption exploits.

2. **Windows Jump Challenge Takeaways:**
   * Stored credentials frequently provide the pivot from a service account to an administrative account. Always inspect PowerShell history files (`ConsoleHost_history.txt`), registry autologon keys, and `cmdkey /list`.
   * Unquoted service paths with spaces remain one of the most reliable privilege escalation vectors on legacy Windows server environments.

---

## Defensive Engineering Checklist for Privilege Escalation Prevention

- [ ] Audit all `/etc/sudoers` files: remove `NOPASSWD: ALL` and avoid delegating interpreters or text editors.
- [ ] Enforce `Defaults secure_path` in sudoers to sanitize execution paths.
- [ ] Audit filesystem for unexpected SUID/SGID binaries and strip unnecessary flags (`find / -perm -4000 -exec chmod u-s {} +`).
- [ ] Restrict file permissions on `/etc/shadow` and `/etc/passwd`.
- [ ] Ensure all Windows service binary paths are enclosed in quotation marks (`"C:\Program Files\Service\daemon.exe"`).
- [ ] Review NTFS permissions on `C:\Program Files` and `C:\Program Files (x86)` to prevent standard users from writing executables.
- [ ] Disable `AlwaysInstallElevated` policy in Group Policy Objects across all domain workstations and servers.
- [ ] Restrict service accounts from possessing `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege` unless strictly required for IIS/SQL functions.
