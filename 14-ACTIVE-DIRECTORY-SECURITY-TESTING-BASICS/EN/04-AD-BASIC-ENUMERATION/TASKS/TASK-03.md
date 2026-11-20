# Task 03: Network Enumeration With SMB

---

## Stage A: Original Task Content & Briefing Summary

Task 03 covers the methodology of enumerating Server Message Block (SMB) network shares without credentials (anonymous login / null session) to discover accessible data and identify sensitive files.

### 1. SMB Share Enumeration Primitives:
* Even when access to default administrative shares (`C$`, `ADMIN$`, `SYSVOL`) is restricted, administrators frequently misconfigure custom shares with anonymous read or write permissions (`Everyone` or `ANONYMOUS LOGON` assigned read/write DACLs).
* **Tools for SMB Share Enumeration:**
  * **Nmap Scripting Engine:**
    ```bash
    nmap -p 445 --script smb-enum-shares 10.211.11.10
    ```
  * **smbclient (Null Session Listing):**
    ```bash
    smbclient -L //10.211.11.10 -N
    ```
  * **smbmap (Share Permission Enumeration):**
    ```bash
    smbmap -H 10.211.11.10
    ```
* **Discovered Shares on DC `10.211.11.10`:**
  * `ADMIN$` — Administrative share (Access Denied)
  * `AnonShare` — Custom share (**READ, WRITE**)
  * `C$` — Default drive share (Access Denied)
  * `IPC$` — Inter-Process Communication (Null Session accessible)
  * `NETLOGON` — Logon scripts (Access Denied)
  * `SharedFiles` — Corporate share (**READ, WRITE**)
  * `SYSVOL` — Group Policy share (Access Denied)
  * `UserBackups` — Backup share (**READ, WRITE**)

### 2. Investigating Accessible Shares:
* Using `smbclient` with the `-N` (no password) flag:
  ```bash
  smbclient //10.211.11.10/SharedFiles -N
  ```
* Navigating directories, inspecting files, and downloading documents.
* Inspecting the accessible shares uncovers a hidden text file containing the task flag: `THM{88_SMB_88}`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Anonymous SMB Shares as Initial Vectors:**
  * Anonymous read access allows extracting sensitive documents, internal memos, backup archives, scripts containing hardcoded credentials, and network diagrams.
  * Anonymous write access allows planting malicious files (e.g. `@Shortcut.url` or malicious `.lnk` files) to trigger coercion attacks (as demonstrated in Room 03).
  * Always document share permissions (`READ, WRITE`) thoroughly in penetration testing reports.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `smbclient -L //10.211.11.10 -N` to list all available shares.
* **Tier 2 Hint:** Connect to each accessible share (`AnonShare`, `SharedFiles`, `UserBackups`) and list their contents.
* **Tier 3 Hint:** Search through the shares for text files. The flag format is `THM{88_SMB_88}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **List Shares on Target DC:**
   ```bash
   smbmap -H 10.211.11.10
   ```
   * Output:
     ```text
     Disk                                   Permissions     Comment
     ----                                   -----------     -------
     ADMIN$                                 NO ACCESS       Remote Admin
     AnonShare                              READ, WRITE     
     C$                                     NO ACCESS       Default share
     IPC$                                   NO ACCESS       Remote IPC
     NETLOGON                               NO ACCESS       Logon server share
     SharedFiles                            READ, WRITE     
     SYSVOL                                 NO ACCESS       Logon server share
     UserBackups                            READ, WRITE     
     ```

2. **Access Shares via `smbclient`:**
   * Explore `SharedFiles`:
     ```bash
     smbclient //10.211.11.10/SharedFiles -N
     smb: \> ls
     smb: \> get Mouse_and_Malware.txt
     smb: \> exit
     ```
   * Explore `AnonShare`:
     ```bash
     smbclient //10.211.11.10/AnonShare -N
     smb: \> ls
     smb: \> get flag.txt
     smb: \> exit
     ```
3. **Inspect Flag File:**
   ```bash
   cat flag.txt
   ```
   * Flag output: `THM{88_SMB_88}`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the flag hidden in one of the shares?
  * **Answer:** `THM{88_SMB_88}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified flag on TryHackMe: `THM{88_SMB_88}`.
* Reconciled SMB share permission matrices and anonymous null session access rights.

---

## Stage G: Key Educational Takeaway

Misconfigured SMB share permissions allow unauthenticated users to enumerate directory structures and read sensitive data. Restricting anonymous share access and auditing share DACLs is a fundamental Active Directory hardening requirement.
