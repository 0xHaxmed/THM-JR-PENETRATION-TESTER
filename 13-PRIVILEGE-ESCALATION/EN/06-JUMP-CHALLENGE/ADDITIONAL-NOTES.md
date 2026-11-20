# Additional Notes & Deep Technical Dive: Jump (Challenge)

## 1. Multi-Hop Privilege Escalation Architecture

Modern enterprise servers frequently distribute workloads across specialized functional accounts (Recon, Dev, Monitor, Ops) to satisfy isolation requirements. However, if inter-process communication, file queues, or shared directories possess lax access controls, an attacker can traverse the pipeline sequentially:

```
+-------------------------------------------------------------------------------+
|                       THE 5-TIER AUTOMATION PIPELINE                          |
+-------------------------------------------------------------------------------+
| Stage 1: Anonymous Ingestion                                                  |
| - Directory `incoming/` monitored by automated inotify runner.                |
| - Weakness: Arbitrary shell execution under `recon_user`.                     |
+-------------------------------------------------------------------------------+
| Stage 2: Development Maintenance                                              |
| - Script `/opt/dev/backup.sh` scheduled via crontab under `dev_user`.         |
| - Weakness: File permissions allow group/world write access (`0777`).         |
+-------------------------------------------------------------------------------+
| Stage 3: Infrastructure Health Monitoring                                     |
| - Service `healthcheck` executes periodically under `monitor_user`.           |
| - Weakness: Invokes `ps` without absolute path (`/bin/ps`),                   |
|   evaluating `/opt/dev/bin` in `$PATH` first.                                 |
+-------------------------------------------------------------------------------+
| Stage 4: Operations Deployment                                                |
| - Administrative delegation via `/etc/sudoers`.                               |
| - Weakness: `monitor_user` permitted to execute `/usr/local/bin/deploy.sh`   |
|   as `ops_user` without password.                                             |
+-------------------------------------------------------------------------------+
| Stage 5: System Administration                                                |
| - Administrative delegation via `/etc/sudoers`.                               |
| - Weakness: `ops_user` permitted to run `/usr/bin/less` on logs with root     |
|   privileges, allowing GTFOBins shell breakout (`!/bin/sh`).                  |
+-------------------------------------------------------------------------------+
```

---

## 2. Pager Shell Escaping Mechanics: `less` Deep Dive

The `less` utility is a terminal pager designed for viewing text files. When invoked under `sudo`:
```bash
sudo /usr/bin/less /var/log/syslog
```
* **Internal Shell Escape Primitive:** While viewing any file inside `less`, typing `!` enters shell escape mode:
  ```
  !/bin/sh
  ```
* **Process Execution:** `less` forks a child process and calls `execve("/bin/sh", ...)`. Because `less` was executed with root privileges via `sudo`, the spawned shell inherits `EUID=0` and `GID=0`.
* **Defensive Mitigation:** In `/etc/sudoers`, configure `NOEXEC` tags to prevent pagers from executing subprocesses:
  ```
  ops_user ALL=(root) NOEXEC: /usr/bin/less /var/log/*
  ```
