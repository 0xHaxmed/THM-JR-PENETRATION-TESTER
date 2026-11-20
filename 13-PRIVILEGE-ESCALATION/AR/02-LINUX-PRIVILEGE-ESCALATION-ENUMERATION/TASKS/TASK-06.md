# Task 06: File Enumeration

---

## Stage A: Original Task Content & Briefing Summary

Task 06 explores filesystem exploration techniques using command-line filters, focusing on locating hidden files, readable sensitive configurations, world-writable directories, and SUID/SGID binaries.

### Key File Enumeration Techniques & `find` Syntax:
1. `ls -la`: Displays all files including dotfiles (hidden files prefixed with `.`).
2. Search by filename:
   * `find . -name flag1.txt`
   * `find /home -name flag1.txt`
   * Case-insensitive matching: `find / -iname "*tryhackme*"`
3. Search by type:
   * Directories: `find / -type d -name config 2>/dev/null`
   * Regular files: `find / -type f -name "*.conf" 2>/dev/null`
4. Search by permissions:
   * World-writable files/directories: `find / -writable -type d 2>/dev/null` or `find / -perm -222 -type d 2>/dev/null`
   * Exact permission 0777: `find / -type f -perm 0777 2>/dev/null`
   * Executable files: `find / -perm -a=x 2>/dev/null`
   * SUID binaries (Set Owner User ID): `find / -perm -u=s -type f 2>/dev/null`
5. Search by size & modification time:
   * Large files: `find / -size +50M 2>/dev/null`
   * Recently modified files: `find / -mtime -10 2>/dev/null`
   * Modified in the last 60 minutes: `find / -cmin -60 2>/dev/null`
6. Standard Error Redirection (`2>/dev/null`): Prevents "Permission denied" errors from cluttering the terminal output when searching across root-restricted directories.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Handling Noise with `2>/dev/null`:** Searching across `/` as an unprivileged user triggers hundreds of permission errors on directories like `/root`, `/proc`, and `/etc/polkit-1`. Appending `2>/dev/null` redirects stderr file descriptor 2 to the null device, returning only accessible, valid results.
* **Hidden Files (`.`):** Files beginning with a dot (`.secret.txt`, `.bash_history`, `.ssh/id_rsa`) do not appear in standard `ls` output. Always run `ls -la` in user home directories (`/home/*`) and shared temporary directories (`/tmp`, `/var/tmp`, `/dev/shm`).

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `ls -la` inside `/home/john` to find the hidden secret file.
* **Tier 2 Hint:** Use `find / -iname "*tryhackme*" 2>/dev/null` to locate the file with TryHackMe in its name.
* **Tier 3 Hint:**
  * Q1 is `THM{not-so-hidden}`.
  * Q2 is `THM{found-the-flag}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Locating Hidden File in Home Directory:
```bash
john@linux-enumeration:~$ cd /home/john
john@linux-enumeration:~$ ls -la
total 36
drwxr-x--- 4 john john 4096 Oct 22 14:15 .
drwxr-xr-x 3 root root 4096 Oct 22 13:40 ..
-rw------- 1 john john  220 Mar 31  2024 .bash_logout
-rw------- 1 john john  114 Oct 22 14:12 .bash_history
-rw-r--r-- 1 john john 3771 Mar 31  2024 .bashrc
drwx------ 2 john john 4096 Oct 22 13:41 .cache
-rw-r--r-- 1 john john  807 Mar 31  2024 .profile
-rw-r--r-- 1 john john   20 Oct 22 14:15 .secret.txt
drwx------ 2 john john 4096 Oct 22 13:41 .ssh

john@linux-enumeration:~$ cat .secret.txt
THM{not-so-hidden}
```
* **Analysis:** The hidden file `.secret.txt` contains the flag `THM{not-so-hidden}`.

### 2. Searching Filesystem for "TryHackMe" in Filename:
```bash
john@linux-enumeration:~$ find / -iname "*tryhackme*" 2>/dev/null
/opt/TryHackMe.txt

john@linux-enumeration:~$ cat /opt/TryHackMe.txt
THM{found-the-flag}
```
* **Analysis:** The file `/opt/TryHackMe.txt` was located in `/opt` and contains the flag `THM{found-the-flag}`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What are the contents of the secret file in your home folder?
  * **Answer:** `THM{not-so-hidden}`
* **Question 2:** Find a file that has TryHackMe in its name. What is its content?
  * **Answer:** `THM{found-the-flag}`

---

## Stage F: Practical Verification & Proof of Work

* Executed `cat /home/john/.secret.txt` and `cat /opt/TryHackMe.txt` on the live target VM.
* Confirmed exact flag string outputs and completed task verification.

---

## Stage G: Key Educational Takeaway

Filesystem enumeration with `find` and targeted `grep` regexes allows an ethical hacker to sift through tens of thousands of operating system files in seconds, instantly isolating configuration files, developer leftovers, hidden secrets, and rogue SUID binaries.
