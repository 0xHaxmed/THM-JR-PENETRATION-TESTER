# Task 08: Practical

---

## Stage A: Original Task Content & Briefing Summary

Task 08 presents an interactive simulated audit report reviewing three target enterprise servers evaluated against official CIS Benchmarks:

### Target Systems Under Review:
1. **`linux-web-01` (`10.10.45.12`)** — Ubuntu Linux 24.04 LTS (CIS Benchmark v1.0.0).
2. **`linux-db-02` (`10.10.45.20`)** — Ubuntu Linux 24.04 LTS (CIS Benchmark v1.0.0).
3. **`win-dc-01` (`10.10.45.50`)** — Windows Server 2022 (CIS Benchmark v3.0.0).

### Audit Findings Breakdown:

#### 1. Host: `linux-web-01`
* **Check 7.1.5:** *Ensure permissions on /etc/shadow are configured.*
  * Status: **FAIL**
  * Expected Permission: `0640` or more restrictive.
  * Actual Observed Permission: **`0644`** (World-readable; standard users can read root password hashes!).
* **Check 7.1.11:** *Ensure no world-writable files exist.*
  * Status: **FAIL**
  * Observed Count: **`3`** world-writable files identified across system paths.
* **Check 7.1.13:** *Ensure no rogue SUID binaries exist.*
  * Status: **FAIL**
  * Identified Rogue SUID Binary: **`/opt/admin-tools/logviewer`** (Custom binary with SUID bit set!).

#### 2. Host: `win-dc-01`
* **Check 1.1.4:** *Ensure 'Minimum password length' is set to '14 or more characters'.*
  * Status: **FAIL**
  * Configured Value: **`8`** characters.
* **Check 2.3.17.1:** *Ensure 'User Account Control: FilterAdministratorToken' is enabled.*
  * Status: **FAIL**
  * Observed Registry Value: **`0x0`** (Disabled).

#### 3. Host: `linux-db-02`
* System Status: Fully hardened database node.
* Total Passed Checks: **`8`** checks passed out of 8 audited (Zero failing checks).

---

## Stage B: Independent Thinking & OpSec Considerations

* **Exploitation Prioritization on `linux-web-01`:**
  * Finding 7.1.5 (`/etc/shadow` is `0644`) represents an immediate, zero-risk path to root credential theft.
  * Finding 7.1.13 (`/opt/admin-tools/logviewer` is SUID) represents an immediate binary execution vector if the binary executes commands, calls external utilities without full paths, or has buffer overflow flaws.
  * In a penetration test, `linux-web-01` offers multiple trivial privilege escalation paths.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Inspect the audit results for `linux-web-01`, `win-dc-01`, and `linux-db-02` in the interactive practical view.
* **Tier 2 Hint:** Look at check 7.1.5 for the octal permission on `/etc/shadow`. Look at check 7.1.13 for the full path of the SUID file.
* **Tier 3 Hint:** Q1 is `0644`. Q2 is `3`. Q3 is `/opt/admin-tools/logviewer`. Q4 is `8`. Q5 is `0x0`. Q6 is `8`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **`linux-web-01` `/etc/shadow` Permission:**
   Inspecting check 7.1.5:
   ```text
   Check 7.1.5: Ensure permissions on /etc/shadow are configured
   Expected: 0640 or more restrictive
   Observed: 0644
   ```
   Answer: `0644`

2. **`linux-web-01` World-Writable File Count:**
   Inspecting check 7.1.11:
   ```text
   Check 7.1.11: Ensure no world-writable files exist
   Found: 3 files with mode 0777
   ```
   Answer: `3`

3. **`linux-web-01` Unexpected SUID Binary Path:**
   Inspecting check 7.1.13:
   ```text
   Check 7.1.13: Rogue SUID Binaries
   Flagged Path: /opt/admin-tools/logviewer
   ```
   Answer: `/opt/admin-tools/logviewer`

4. **`win-dc-01` Minimum Password Length:**
   Inspecting check 1.1.4:
   ```text
   Check 1.1.4: Minimum Password Length
   Current Value: 8
   ```
   Answer: `8`

5. **`win-dc-01` FilterAdministratorToken Registry Value:**
   Inspecting check 2.3.17.1:
   ```text
   Check 2.3.17.1: FilterAdministratorToken
   Registry Setting: 0x0
   ```
   Answer: `0x0`

6. **`linux-db-02` Total Passed Checks:**
   Inspecting `linux-db-02`:
   ```text
   Passed: 8 / 8
   ```
   Answer: `8`

---

## Stage E: Final Answer & Key Values

* **Question 1:** On linux-web-01, what is the actual permission value observed on /etc/shadow in the failing check 7.1.5?
  * **Answer:** `0644`
* **Question 2:** On linux-web-01, how many world-writable files were detected by check 7.1.11?
  * **Answer:** `3`
* **Question 3:** On linux-web-01, what is the full path of the unexpected SUID binary found by check 7.1.13?
  * **Answer:** `/opt/admin-tools/logviewer`
* **Question 4:** On win-dc-01, what is the minimum password length currently configured, as reported by check 1.1.4?
  * **Answer:** `8`
* **Question 5:** On win-dc-01, what is the current value of the FilterAdministratorToken registry key, as reported by check 2.3.17.1?
  * **Answer:** `0x0`
* **Question 6:** How many total checks does linux-db-02 pass?
  * **Answer:** `8`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed all six answers against the completed TryHackMe account.
* Verified that reading compliance audit reports pinpoints exact host weaknesses.

---

## Stage G: Key Educational Takeaway

Auditing CIS Benchmark reports allows operators to quickly identify high-value target assets: `linux-web-01` is heavily misconfigured with world-readable shadow files (`0644`) and custom SUID binaries (`/opt/admin-tools/logviewer`), while `linux-db-02` is properly hardened.
