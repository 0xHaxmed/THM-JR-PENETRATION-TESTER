# Task 05: Username Enumeration and Password Spraying

---

## Stage A: Original Task Content & Briefing Summary

Task 05 covers the offensive methodology and technical execution of **Password Spraying**—one of the most effective and pervasive techniques for compromising enterprise Active Directory domains without locking out user accounts.

### 1. Brute-Forcing vs. Password Spraying:
* **Traditional Brute-Forcing:** Testing hundreds or thousands of candidate passwords against a single user account (`1 User : N Passwords`).
  * *Result:* Quickly exceeds the enterprise account lockout threshold (e.g. 5 failed attempts), locking the account, causing a denial-of-service, and triggering high-severity SOC alerts.
* **Password Spraying:** Testing a single high-probability password across hundreds of different user accounts (`N Users : 1 Password`).
  * *Result:* Each user account only receives a single failed logon attempt, completely evading account lockout thresholds.

### 2. Querying the Domain Lockout Policy:
* Before initiating any password spray, the penetration tester must enumerate the domain's password and lockout policy using `NetExec` (`nxc`):
  ```bash
  nxc smb 192.168.12.100 -u 'svc.jenkins' -p 'Jen5k1ns2025!' --pass-pol
  ```
* **Lockout Policy Parameters:**
  * *Account Lockout Threshold:* 5 attempts.
  * *Lockout Observation Window:* 30 minutes.
  * *Lockout Duration:* 30 minutes.
* **Operational Rule:** An attacker can safely test up to 4 passwords per account every 30 minutes without locking any user out.

### 3. Executing the Spray Campaign:
* Using the clean list of 42 usernames discovered via Kerbrute (`clean_users.txt`) and the default onboarding password recovered from Jenkins (`MegaCorp01!`):
  ```bash
  nxc smb 192.168.12.100 -u clean_users.txt -p 'MegaCorp01!' --continue-on-success
  ```
* **Analyzing NetExec Output Flags:**
  * `[+]`: Successful authentication (valid domain credentials).
  * `[-] STATUS_LOGON_FAILURE`: Incorrect password.
  * `[-] STATUS_ACCOUNT_DISABLED`: Account disabled by administrators.
  * `[-] STATUS_ACCOUNT_LOCKED_OUT`: Account currently locked.
  * `(Pwn3d!)`: User possesses local administrative rights on the target host.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Password Spraying OpSec:**
  * Never spray without first enumerating the lockout policy. If the threshold is 3, spraying 3 passwords in a row will lock accounts on the third attempt.
  * Rotate through common enterprise passwords tied to seasonality (e.g. `Winter2026!`, `Company2026!`, `Welcome123!`).
  * Account lockouts disrupt business operations and violate Rules of Engagement (RoE).

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run NetExec against `192.168.12.100` using the 42 usernames from Task 03 and the password `MegaCorp01!`.
* **Tier 2 Hint:** Count how many lines in NetExec's output display the green `[+]` success indicator.
* **Tier 3 Hint:** Exactly `2` accounts succeed. The first account alphabetically is `alice.moore`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Format Usernames into a Clean List:**
   ```bash
   grep "VALID USERNAME" valid_users.txt | awk '{print $NF}' | sed 's/@thm.loc//' > clean_users.txt
   wc -l clean_users.txt
   ```
   * 42 lines verified.

2. **Execute Password Spray via NetExec:**
   ```bash
   nxc smb 192.168.12.100 -u clean_users.txt -p 'MegaCorp01!' --continue-on-success
   ```
3. **Parse Results:**
   ```text
   SMB  192.168.12.100  445  ROOTDC  [+] thm.loc\alice.moore:MegaCorp01!
   SMB  192.168.12.100  445  ROOTDC  [-] thm.loc\bob.ross:MegaCorp01! STATUS_LOGON_FAILURE
   ...
   SMB  192.168.12.100  445  ROOTDC  [+] thm.loc\zoe.williams:MegaCorp01!
   ...
   ```
4. **Findings:**
   * Total accounts successfully compromised: **2** (`alice.moore` and `zoe.williams`).
   * First user account alphabetically: **`alice.moore`**.

---

## Stage E: Final Answer & Key Values

* **Question 1:** How many accounts were cracked using the brute force attack?
  * **Answer:** `2`
* **Question 2:** Which is the first user account (alphabetically) that uses the default onboarding password?
  * **Answer:** `alice.moore`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `2` and `alice.moore`.
* Validated successful SMB authentication for both compromised domain users.

---

## Stage G: Key Educational Takeaway

Password spraying is the premier technique for breaching enterprise Active Directory domains because it circumvents account lockout policies by testing high-probability passwords horizontally across the entire user population.
