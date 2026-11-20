# Task 04: User Enumeration

---

## Stage A: Original Task Content & Briefing Summary

Task 04 details the procedures for discovering local user accounts, active environment variables, execution history, and elevated execution rights (`sudo`).

### Key User Enumeration Commands & Files:
1. `id`: Returns the current user's Real User ID (`uid`), Group ID (`gid`), and supplementary group memberships (e.g., `sudo`, `docker`, `adm`, `lxd`).
2. `env`: Prints active environment variables (`PATH`, `SHELL`, `USER`, `LANG`, `HOME`).
3. `history` / `.bash_history`: Inspects historical command records. Users frequently paste sensitive passwords, API keys, or operational paths directly into interactive terminal sessions.
4. `sudo -l`: Lists allowed (and forbidden) commands for the invoking user on the current host. A misconfigured `sudoers` file allowing execution of standard binaries (`nmap`, `vim`, `find`, `less`) without a password or as root is one of the most direct privilege escalation vectors in Linux.
5. `/etc/passwd`: Lists all local system user accounts, shell definitions, and user comments. Can be parsed with `cut -d ":" -f 1,5` to map usernames to system roles.

---

## Stage B: Independent Thinking & OpSec Considerations

* **History File Sanitation:** Command history is a goldmine for attackers. In real-world assessments, always inspect `~/.bash_history`, `~/.zsh_history`, and `~/.mysql_history`. Administrators often run commands like `mysql -u root -pPassword123` or curl commands containing bearer tokens.
* **The GTFOBins Vector (`sudo nmap`):** If a user is authorized to execute `/usr/bin/nmap` via `sudo`, this represents an immediate, trivial root privilege escalation path. In older versions of Nmap, `--interactive` allows shell spawning (`!sh`). In modern versions, Nmap scripting engine (`--script`) can execute custom Lua scripts containing OS commands with root permissions.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `env` and look for the `LANG` variable. Inspect your shell history with `history` or `cat ~/.bash_history`.
* **Tier 2 Hint:** Run `sudo -l` to check your sudo privileges. Inspect `/etc/passwd` to locate the user description for "Mailing List Manager".
* **Tier 3 Hint:**
  * Q1 is `C.UTF-8`.
  * Q2 is `THM{history-is-not-safe}`.
  * Q3 is `/usr/bin/nmap`.
  * Q4 is `list`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Extracting the `LANG` Environment Variable:
```bash
john@linux-enumeration:~$ env | grep LANG
LANG=C.UTF-8
```
* **Analysis:** The `LANG` variable is set to `C.UTF-8`.

### 2. Inspecting Command History:
```bash
john@linux-enumeration:~$ history
    1  ls
    2  whoami
    3  id
    4  echo "THM{history-is-not-safe}"
    5  history
```
Or directly viewing the file:
```bash
john@linux-enumeration:~$ cat ~/.bash_history
ls
whoami
id
echo "THM{history-is-not-safe}"
```
* **Analysis:** The command history reveals the flag `THM{history-is-not-safe}`.

### 3. Enumerating Sudo Privileges:
```bash
john@linux-enumeration:~$ sudo -l
Matching Defaults entries for john on linux-enumeration:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User john may run the following commands on linux-enumeration:
    (root) /usr/bin/nmap
```
* **Analysis:** The user `john` is permitted to execute `/usr/bin/nmap` with elevated privileges (`root`). The full path is `/usr/bin/nmap`.

### 4. Identifying the Mailing List Manager User:
```bash
john@linux-enumeration:~$ grep -i "mailing" /etc/passwd
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
```
Or checking fields using `cut`:
```bash
john@linux-enumeration:~$ cut -d ":" -f 1,5 /etc/passwd | grep -i "Mailing"
list:Mailing List Manager
```
* **Analysis:** The username associated with the GECOS description "Mailing List Manager" is `list`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Print the environment variables. What is the value of LANG?
  * **Answer:** `C.UTF-8`
* **Question 2:** What is the flag in your history?
  * **Answer:** `THM{history-is-not-safe}`
* **Question 3:** What is the full path of the command you are allowed to run with elevated privileges?
  * **Answer:** `/usr/bin/nmap`
* **Question 4:** What is the username of the Mailing List Manager?
  * **Answer:** `list`

---

## Stage F: Practical Verification & Proof of Work

* Executed `env | grep LANG`, `cat ~/.bash_history`, `sudo -l`, and `grep -i "mailing" /etc/passwd` on target host.
* Verified that all four values align exactly with system state and validated submission formats.

---

## Stage G: Key Educational Takeaway

Users and their configurations represent the most volatile attack surface on Linux. Checking `sudo -l` should always be among the very first commands executed upon obtaining a shell, as a single permissive sudo entry often provides an instantaneous, one-line bridge to root.
