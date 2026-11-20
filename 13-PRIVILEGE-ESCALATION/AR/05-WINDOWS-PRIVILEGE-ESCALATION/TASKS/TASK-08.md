# Task 08: Tools of the Trade

---

## Stage A: Original Task Content & Briefing Summary

Task 08 reviews industry-standard automated enumeration and privilege escalation discovery tools designed for Windows environments. While manual enumeration ensures precision, automated scanners rapidly surface common misconfigurations and missing patches across complex corporate systems.

### Core Windows Enumeration Tools:
1. **WinPEAS (Windows Privilege Escalation Awesome Script):**
   * Available as both a compiled `.exe` and a `.bat` script.
   * Scans system information, users, network configurations, services, stored credentials in registry/files, DLL hijacking opportunities, and unquoted paths with color-coded ANSI highlighting.
   * Command: `winpeas.exe > output.txt`
2. **PrivescCheck:**
   * A pure PowerShell-based enumeration script that operates entirely in memory without requiring external executable drops.
   * Audits services, scheduled tasks, registry settings, and user privileges.
   * Invocation syntax:
     ```powershell
     Set-ExecutionPolicy Bypass -Scope process -Force
     . .\PrivescCheck.ps1
     Invoke-PrivescCheck -Extended
     ```
3. **WES-NG (Windows Exploit Suggester - Next Generation):**
   * An attacker-side Python utility that compares a target machine's `systeminfo` output against an offline database of Microsoft security bulletins and CVEs to identify missing OS patches.
   * Workflow:
     ```cmd
     # On Target:
     systeminfo > systeminfo.txt
     # On Attacker Box:
     python3 wes.py systeminfo.txt
     ```
4. **Metasploit Post-Exploitation:**
   * Module `multi/recon/local_exploit_suggester` runs against existing Meterpreter sessions to identify candidate Windows kernel and service exploits.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Living off the Land vs Binary Drops:** On modern Windows endpoints monitored by Microsoft Defender or enterprise EDRs, dropping `winpeas.exe` will trigger an immediate signature block (often flagged as `HackTool:Win32/WinPEAS`). In contrast, running `PrivescCheck.ps1` in memory with an execution policy bypass or executing native PowerShell one-liners generates significantly lower friction.
* **WES-NG Stealth:** WES-NG operates completely offline on the attacker's machine. The only action required on the target host is running the native `systeminfo` command and reading the text output.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read through the tool summaries to understand the difference between WinPEAS, PrivescCheck, and WES-NG.
* **Tier 2 Hint:** Note that PrivescCheck runs natively within PowerShell without compiled binaries.
* **Tier 3 Hint:** Click the completion button to proceed.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

Automated Windows enumeration tools accelerate the discovery of complex misconfigurations like AlwaysInstallElevated, unquoted service paths, and stored credentials.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Click and continue learning!
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed task completion status on TryHackMe room `windowsprivesc20`.
* Reconciled operational methodologies for WinPEAS, PrivescCheck, and WES-NG.

---

## Stage G: Key Educational Takeaway

Deploying automated scanners in memory (such as PrivescCheck) or performing offline analysis with WES-NG maximizes reconnaissance speed while minimizing endpoint detection telemetry.
