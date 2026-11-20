# Task 10: Group Policies

---

## Stage A: Original Task Content & Briefing Summary

Task 10 examines the mechanics and operational deployment of **Group Policy Objects (GPOs)** via the Group Policy Management Console (`gpmc.msc`).

### 1. Group Policy Architecture:
* **GPO Structure:**
  * Group Policies enable administrators to centrally enforce security baselines, software installations, script executions, and registry settings across all computers and users in an OU or domain.
  * Every GPO splits into two main sections:
    1. **Computer Configuration:** Applies to machine objects during startup/boot, regardless of who logs in.
    2. **User Configuration:** Applies to user objects during logon.
* **GPO Distribution & SYSVOL Replication:**
  * GPOs are stored on Domain Controllers and distributed to client endpoints via the **`SYSVOL`** network share (`\\<domain>\SYSVOL\<domain>\Policies\{GUID}`).
  * `SYSVOL` replicates across all DCs using Distributed File System Replication (DFSR).
  * Because `SYSVOL` is world-readable by any authenticated domain user, insecure configurations (like legacy `cpassword` XML files in Group Policy Preferences) historically leaked encrypted passwords that could be trivially decrypted.
* **Refresh Intervals & Forcing Updates:**
  * By default, client endpoints query the DC and refresh group policies every 90 to 120 minutes.
  * Administrators and penetration testers can force an immediate refresh on an endpoint using:
    ```cmd
    gpupdate /force
    ```

### 2. Practical Hands-On Exercise:
* Create a GPO linked to the `Servers` OU on `CDC1`.
* Policy Path: `Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Restricted Groups`.
* Configure `Restricted Groups`: Add the domain group `Product Admins` into the local `Administrators` group.
* Verify policy application on `Server1` (`192.168.10.51`) by authenticating with user `tbm.thm.loc\terry.fox` (`nACFhAsbA1Xv`) who belongs to `Product Admins`.
* Trigger **Verify Servers OU GPO** on `http://192.168.10.250:5000` to retrieve the flag from `C:\flag3.txt` on `Server1`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **GPO Abuse in Adversarial Attacks (SharpGPOAbuse):**
  * In Active Directory security assessments, write permissions over a GPO (`GenericAll` or `WriteProperty`) represent one of the most powerful privilege escalation vectors.
  * If an attacker has write access to a GPO linked to an OU containing domain controllers or servers, they can inject an immediate scheduled task (running as `NT AUTHORITY\SYSTEM`), add their account to local Administrators, or deploy a reverse shell across every machine in the OU.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the section describing how GPO templates and scripts are replicated across DCs and read by client machines.
* **Tier 2 Hint:** What is the 6-letter name of the replicated share located at `C:\Windows\SYSVOL\sysvol`?
* **Tier 3 Hint:** The network share name is `sysvol`. The flag is retrieved from `C:\flag3.txt` on `Server1`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Identifying the Distribution Share:**
   * Group Policy Object templates, scripts, and policies are stored in and replicated via the **`SYSVOL`** share.
2. **Deploying the GPO via GPMC:**
   * On `CDC1`, open Group Policy Management (`gpmc.msc`).
   * Right-click `Servers` OU -> **Create a GPO in this domain, and Link it here...** -> Name: `Servers Admin Policy`.
   * Right-click the newly created GPO -> **Edit**.
   * Navigate to: `Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Restricted Groups`.
   * Right-click -> **Add Group...** -> Browse and select `Product Admins`.
   * Under "This group is a member of:", click **Add...** -> type `Administrators`.
   * Click **Apply** and close the editor.
3. **Applying and Verifying the Policy on Server1:**
   * On `Server1`, open an administrative command prompt:
     ```cmd
     gpupdate /force
     ```
   * Verify group membership:
     ```cmd
     net localgroup Administrators
     ```
   * Confirm that `TBM\Product Admins` is now listed.
4. **Trigger Verification:**
   * Open browser to `http://192.168.10.250:5000`.
   * Click **Verify Servers OU GPO**.
   * On `Server1`, read `C:\flag3.txt`:
     ```powershell
     Get-Content C:\flag3.txt
     ```
   * Flag output: `THM{2d8fa7ec-c925-425d-bbd3-ba02f03219f5}`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the name of the network share used to distribute GPOs to domain machines?
  * **Answer:** `sysvol`
* **Question 2:** Once you have applied the GPO, navigate to the flag verification system (`http://192.168.10.250:5000`) and click **Verify Servers OU GPO** to receive a flag in `C:\flag3.txt` on Server1. What is the flag?
  * **Answer:** `THM{2d8fa7ec-c925-425d-bbd3-ba02f03219f5}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `sysvol` and `THM{2d8fa7ec-c925-425d-bbd3-ba02f03219f5}`.
* Reconciled GPO application logs and Restricted Groups configuration on `Server1`.

---

## Stage G: Key Educational Takeaway

Group Policy Objects provide centralized configuration enforcement distributed via the `SYSVOL` share. In security auditing, GPO permissions represent high-impact attack paths that allow mass code execution across entire fleets of domain-joined systems.
