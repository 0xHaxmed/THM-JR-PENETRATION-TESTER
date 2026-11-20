# Task 06: Creating a New Domain

---

## Stage A: Original Task Content & Briefing Summary

Task 06 guides the learner through hands-on domain deployment: promoting a standalone Windows Server (`CDC1`) to become a Child Domain Controller for the new child domain `tbm.thm.loc` under parent domain `thm.loc`.

### Target Host & Credentials:
* **Host:** `CDC1` (`192.168.10.110`)
* **Initial Local Credentials:** `Administrator` : `zKGjt4iDckMQtT5TC8uZFwUL`
* **Parent DC:** `ROOTDC.THM.LOC` (`192.168.10.100`)
* **Parent Domain Credentials:** `THM\Administrator` : `learningadisfun1!`
* **New Child Domain:** `tbm.thm.loc` (NetBIOS: `TBM`)

### Hands-on Deployment Procedure:
1. Connect via RDP to `192.168.10.110` using initial credentials.
2. Review the automated deployment script located at `C:\install-domain.ps1`:
   ```powershell
   # Point DNS to the Parent Domain Controller
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.10.100"

   # Set new Local Administrator password
   $adminPassword = ConvertTo-SecureString "childdomainftw1!" -AsPlainText -Force
   Set-LocalUser -Name "Administrator" -Password $adminPassword

   # Authenticate against Parent Domain
   $parentAdminPassword = ConvertTo-SecureString "learningadisfun1!" -AsPlainText -Force
   $parentAdminCredentials = New-Object System.Management.Automation.PSCredential("THM\Administrator", $parentAdminPassword)

   # Promote Server to Child Domain Controller
   Install-ADDSDomain -NewDomainName "tbm" -ParentDomainName "thm.loc" -DomainMode "6" `
                      -Credential $parentAdminCredentials -InstallDns:$true -Force:$true

   # Force Restart to Finalize AD DS Setup
   Restart-Computer -Force
   ```
3. Execute the script within an elevated PowerShell console.
4. Allow the promotion process to run, configure AD DS partitions, and automatically reboot.
5. Reconnect to `192.168.10.110` using the newly established domain credentials:
   * **Domain Account:** `TBM.THM.LOC\Administrator`
   * **Password:** `childdomainftw1!`
6. Navigate to `http://192.168.10.250:5000` on the AttackBox and trigger **Verify TBM Domain Promotion**.
7. Retrieve the generated flag from `C:\flag1.txt` on `CDC1`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **DNS as the Foundation of Active Directory:** In Active Directory, DNS is not optional; it is fundamental. Domain Controllers rely on DNS Service Location (`SRV`) records (e.g. `_ldap._tcp.dc._msdcs.domain.com`) to allow endpoints and other DCs to locate authentication services. Notice that Step 1 in the promotion script redirected DNS to `192.168.10.100`. In a penetration test, failing to resolve the target DC's SRV records often causes Kerberos authentication or domain join commands to fail silently.
* **Child Domain Admin to Forest Takeover:** In security auditing, a child domain administrator (`TBM\Administrator`) is not an `Enterprise Admin`. However, because both domains share a forest, child domain compromise historically allows traversing the parent domain via SID History injection unless SID Filtering is strictly enforced.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Log in to `192.168.10.110` and run `C:\install-domain.ps1` as Administrator.
* **Tier 2 Hint:** After the machine reboots, ensure the AD DS services are running, then visit `http://192.168.10.250:5000` to trigger flag verification.
* **Tier 3 Hint:** Read the flag contents written to `C:\flag1.txt` on `CDC1`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **RDP into CDC1:**
   ```bash
   xfreerdp /u:Administrator /p:'zKGjt4iDckMQtT5TC8uZFwUL' /v:192.168.10.110 /dynamic-resolution
   ```
2. **Execute Domain Promotion:**
   In PowerShell (Run as Administrator):
   ```powershell
   powershell.exe -ExecutionPolicy Bypass -File C:\install-domain.ps1
   ```
3. **Verify Domain Promotion:**
   * After automatic restart, verify with `Get-ADDomain` or `nltest /dsgetdc:tbm.thm.loc`.
4. **Trigger Verification:**
   * Open browser to `http://192.168.10.250:5000`.
   * Click **Verify TBM Domain Promotion**.
   * On `CDC1`, inspect `C:\flag1.txt`:
     ```powershell
     Get-Content C:\flag1.txt
     ```
   * Flag output: `THM{caf78a39-1e8b-4ec2-8eaf-e38a42590d1b}`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Once you have promoted the server to a domain controller, navigate to the flag verification system (`http://192.168.10.250:5000`) and click **Verify TBM Domain Promotion** to receive a flag in `C:\flag1.txt` on CDC1. What is the flag?
  * **Answer:** `THM{caf78a39-1e8b-4ec2-8eaf-e38a42590d1b}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified flag on TryHackMe: `THM{caf78a39-1e8b-4ec2-8eaf-e38a42590d1b}`.
* Reconciled AD DS child domain creation log files and DNS delegation from `thm.loc`.

---

## Stage G: Key Educational Takeaway

Deploying an Active Directory child domain binds directory services to a shared forest root, sets up DNS replication, and creates automatic transitive Kerberos trust relationships. Understanding domain provisioning reveals the inter-service dependencies (DNS, RPC, Kerberos) vital for enterprise attacks and defenses.
