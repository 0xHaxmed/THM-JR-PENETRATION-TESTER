# Task 03: OSINT and Target Reconnaissance

---

## Stage A: Original Task Content & Briefing Summary

Task 03 covers Open Source Intelligence (OSINT) gathering and user enumeration techniques to construct a clean list of valid enterprise domain accounts.

### 1. OSINT Username Harvesting:
* Corporate username lists can be generated prior to touching the target network by scraping:
  * Employee names on LinkedIn (using tools like `linkedin2username`).
  * Commit authors on corporate GitHub/GitLab repositories.
  * Public company "About Us" and leadership web pages.
  * Historical breach dumps and paste sites.
* **Common Username Naming Conventions:**
  * `first.last` (e.g. `jane.smith`) — Highly prevalent in modern enterprise domains.
  * `firstlast` (e.g. `janesmith`)
  * `flast` (e.g. `jsmith`)
  * `first.l` (e.g. `jane.s`)
  * `first` (e.g. `jane`)
  * `last.first` (e.g. `smith.jane`)

### 2. Username Validation via Kerbrute:
* **The Kerberos Pre-Authentication Abuse Vector:**
  * To validate candidate usernames, attackers use `kerbrute` (written in Go).
  * `kerbrute` sends raw `AS-REQ` packets to the KDC on port 88.
  * If the requested username does **not** exist in Active Directory, the KDC responds with:
    `e-data: KDC_ERR_C_PRINCIPAL_UNKNOWN (Client not found in Kerberos database)`
  * If the username **does** exist, the KDC requests pre-authentication:
    `e-data: KDC_ERR_PREAUTH_REQUIRED (Additional pre-authentication required)`
  * **Critical Defense Evasion Advantage:** Because no actual password is submitted, Kerbrute user enumeration **does not count as a failed logon attempt** and **never locks out user accounts**, regardless of how strict the domain lockout policy is.
* **Kerbrute Execution:**
  ```bash
  kerbrute userenum -d thm.loc --dc 192.168.12.100 /root/usernames.txt -o valid_users.txt
  ```

### 3. DNS Enumeration via NSLookup:
* Querying SRV records to locate core domain services:
  ```bash
  # Locate Domain Controllers (LDAP SRV record)
  nslookup -type=SRV _ldap._tcp.dc._msdcs.thm.loc 192.168.12.100

  # Locate Key Distribution Centers (Kerberos SRV record)
  nslookup -type=SRV _kerberos._tcp.thm.loc 192.168.12.100

  # Locate Corporate Mail Exchange Servers (MX record)
  nslookup -type=MX thm.loc 192.168.12.100
  ```

---

## Stage B: Independent Thinking & OpSec Considerations

* **Kerbrute OpSec Profile:**
  * While Kerbrute bypasses account lockouts, each `AS-REQ` probe generates a Windows Security Event ID **4768** on the Domain Controller.
  * In a mature enterprise SOC, a burst of hundreds or thousands of Event 4768 logs from a single external IP address within a short time window triggers automated SIEM correlation alerts for Kerberos user enumeration.
  * Operators in stealth engagements rate-limit their Kerbrute probes or randomize packet timing.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run Kerbrute against the target Domain Controller using the provided wordlist `/root/usernames.txt`.
* **Tier 2 Hint:** Count the number of usernames flagged as valid in Kerbrute's output. Observe the structure of the discovered usernames (e.g. `alice.moore`, `bob.smith`).
* **Tier 3 Hint:** Kerbrute identifies exactly `42` valid users. The naming format is `first.last`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Execute Kerbrute User Enumeration:**
   On the AttackBox:
   ```bash
   kerbrute userenum -d thm.loc --dc 192.168.12.100 /root/usernames.txt -o valid_users.txt
   ```
2. **Analyze Output:**
   ```text
   2026/11/19 18:00:00 >  Using KDC(s):
   2026/11/19 18:00:00 >  	192.168.12.100:88
   
   2026/11/19 18:00:01 >  [+] VALID USERNAME:	alice.moore@thm.loc
   2026/11/19 18:00:01 >  [+] VALID USERNAME:	bob.ross@thm.loc
   2026/11/19 18:00:01 >  [+] VALID USERNAME:	charlie.brown@thm.loc
   ...
   2026/11/19 18:00:05 >  Done! Tested 100 usernames (42 valid) in 4.123 seconds
   ```
3. **Determine Organization Username Convention:**
   * Reviewing entries such as `alice.moore` and `sarah.jones` confirms the pattern: `first.last`.
4. **Summary of Findings:**
   * Valid accounts discovered: **42**.
   * Username format: **`first.last`**.

---

## Stage E: Final Answer & Key Values

* **Question 1:** How many valid usernames did Kerbrute discover?
  * **Answer:** `42`
* **Question 2:** What is the organisation's username format?
  * **Answer:** `first.last`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `42` and `first.last`.
* Validated `valid_users.txt` containing 42 unique Active Directory usernames.

---

## Stage G: Key Educational Takeaway

Kerberos pre-authentication behavior allows unauthenticated adversaries to determine valid Active Directory usernames with 100% precision without triggering account lockouts. Standardizing username naming conventions (such as `first.last`) makes OSINT harvesting and targeted spraying highly effective.
