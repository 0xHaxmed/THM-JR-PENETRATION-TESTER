# Task 08: Domain-Joining a Computer

---

## Stage A: Original Task Content & Briefing Summary

Task 08 focuses on the practical workflow of onboarding an independent standalone server (`Server1`) into the Active Directory domain hierarchy (`tbm.thm.loc`).

### Target Host & Credentials:
* **Host:** `Server1` (`192.168.10.51`)
* **Initial Local Credentials:** `Administrator` : `FwSA85KNn5R9pjxzZk7fmyjB`
* **Target Domain:** `tbm.thm.loc`
* **Domain Controller:** `CDC1` (`192.168.10.110`)
* **Domain Admin Account:** `TBM\Administrator` : `childdomainftw1!`

### Domain-Joining Automation Script (`C:\join-domain.ps1`):
* Inspecting the script on `Server1`:
  ```powershell
  # Configure primary DNS to resolve tbm.thm.loc through the Child Domain Controller
  Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.10.110"

  # Authenticate with Child Domain Administrator credentials
  $adminPassword = ConvertTo-SecureString "childdomainftw1!" -AsPlainText -Force
  $domainAdminCredentials = New-Object System.Management.Automation.PSCredential("TBM\Administrator", $adminPassword)

  # Join Server1 to tbm.thm.loc
  Add-Computer -DomainName "tbm.thm.loc" -Credential $domainAdminCredentials

  # Restart machine to complete domain integration
  Restart-Computer -Force
  ```

### Verification & Flag Retrieval:
* Once `Server1` reboots, reconnect using domain credentials:
  ```bash
  xfreerdp /u:Administrator /p:'childdomainftw1!' /d:TBM.THM.LOC /v:192.168.10.51 /dynamic-resolution
  ```
* Open the verification portal at `http://192.168.10.250:5000` and click **Verify Server1 Domain Joining**.
* Read the generated flag from `C:\flag2.txt` on `Server1`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **What Happens During a Domain Join Under the Hood?**
  1. *DNS SRV Query:* The client queries DNS for `_ldap._tcp.dc._msdcs.tbm.thm.loc` to discover the DC's IP.
  2. *Machine Account Creation:* A computer object named `SERVER1$` is instantiated in the `CN=Computers` container with a unique SID (`S-1-5-21-...-1105`) and random initial password.
  3. *Local Security Group Modification:* The domain security group `TBM\Domain Admins` is automatically injected into `Server1`'s local `Administrators` group.
* **Adversarial Implications:**
  * Anyone who compromises a `Domain Admin` account now possesses automatic, uninhibited local administrative control over `Server1` and every other domain-joined workstation or server.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Log in to `Server1` at `192.168.10.51` using the initial credentials and run `C:\join-domain.ps1`.
* **Tier 2 Hint:** After the system reboots, log in as `TBM\Administrator` and trigger the verification button on `http://192.168.10.250:5000`.
* **Tier 3 Hint:** Inspect the contents of `C:\flag2.txt` on `Server1`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **RDP to Server1:**
   ```bash
   xfreerdp /u:Administrator /p:'FwSA85KNn5R9pjxzZk7fmyjB' /v:192.168.10.51 /dynamic-resolution
   ```
2. **Execute Domain Join Script:**
   ```powershell
   powershell.exe -ExecutionPolicy Bypass -File C:\join-domain.ps1
   ```
3. **Trigger Flag Verification:**
   * In a browser, navigate to `http://192.168.10.250:5000`.
   * Click **Verify Server1 Domain Joining**.
4. **Retrieve Flag:**
   * On `Server1`, open PowerShell:
     ```powershell
     Get-Content C:\flag2.txt
     ```
   * Flag output: `THM{941c5c55-f17a-422f-b7c3-7bb2386f187e}`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Once the host has been joined to the domain, navigate to the flag verification system (`http://192.168.10.250:5000`) and click **Verify Server1 Domain Joining** to receive a flag in `C:\flag2.txt` on Server1. What is the flag?
  * **Answer:** `THM{941c5c55-f17a-422f-b7c3-7bb2386f187e}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified flag on TryHackMe: `THM{941c5c55-f17a-422f-b7c3-7bb2386f187e}`.
* Verified that `Server1` is domain-joined using `Get-WmiObject -Class Win32_ComputerSystem | Select Domain`.

---

## Stage G: Key Educational Takeaway

Joining a machine to an Active Directory domain establishes a mutual trust relationship between the endpoint and the KDC, creates a dedicated machine account (`SERVER1$`), and nests Domain Admins into local administrative groups.
