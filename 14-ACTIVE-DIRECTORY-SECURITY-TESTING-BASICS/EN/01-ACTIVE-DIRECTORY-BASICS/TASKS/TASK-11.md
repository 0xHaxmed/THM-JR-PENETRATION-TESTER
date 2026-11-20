# Task 11: Foreign Forest Trust

---

## Stage A: Original Task Content & Briefing Summary

Task 11 demonstrates the setup, configuration, and verification of a **Cross-Forest Trust** between two completely separate Active Directory forests: `THM` (`thm.loc`) and partner vendor `TryVendorMe` (`tvm.loc`).

### Infrastructure Context:
* **Partner Domain Controller:** `FDC1.TVM.LOC` (`192.168.10.120`)
* **Partner Forest Admin:** `TVM\Administrator` : `anotherforest1!`
* **Partner Target Server:** `Server2` (`192.168.10.52`) in `tvm.loc`
* **Parent DC:** `ROOTDC.THM.LOC` (`192.168.10.100`)
* **Parent Forest Admin:** `THM\Administrator` : `learningadisfun1!`
* **Child Target Server:** `Server1` (`192.168.10.51`) in `tbm.thm.loc`

### Hands-on Trust Configuration:
1. **Accessing the Trust Management Console:**
   * Open `Active Directory Domains and Trusts` (`domain.msc`) on `FDC1.TVM.LOC`.
   * Right-click `tvm.loc` -> **Properties** -> **Trusts** tab -> **New Trust...**
2. **Configuring the Trust Parameters:**
   * *Trust Name:* `thm.loc`
   * *Trust Type:* **Forest Trust**
   * *Direction:* **Two-way** (bidirectional authentication)
   * *Sides of Trust:* Both this domain and the specified domain (authenticate as `THM\Administrator` : `learningadisfun1!`)
   * *Authentication Level:* **Forest-wide authentication**
3. **Cross-Forest Resource Delegation:**
   * **Delegation 1 (Granting THM Access to TVM):**
     - Add user `THM\Claire` to the `Server Admins` security group on `FDC1.TVM.LOC`.
     - This grants `Claire` Remote Desktop and administrative privileges over `Server2` (`192.168.10.52`).
   * **Delegation 2 (Granting TVM Access to TBM):**
     - Because `tbm.thm.loc` is a child domain of `thm.loc`, the bidirectional trust with `tvm.loc` extends transitively.
     - Add user `TVM\alice.king` to the `Product Admins` security group in `tbm.thm.loc`.
     - Because `Product Admins` is assigned local administrative privileges over `Server1` via the Restricted Groups GPO (from Task 10), `alice.king` instantly gains local administrative access over `Server1` (`192.168.10.51`).

---

## Stage B: Independent Thinking & OpSec Considerations

* **Transitive Trusts and Attack Path Pivoting:**
  * Forest trusts allow penetration testers to pivot across corporate boundaries (e.g. from a compromised contractor or subsidiary network into the parent holding company).
  * If SID Filtering is disabled across a forest trust, an attacker controlling one forest can forge Kerberos inter-realm tickets with injected foreign SIDs (ExtraSids), escalating privileges into the trusting forest.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Establish the two-way forest trust in `domain.msc` on `FDC1` (`192.168.10.120`).
* **Tier 2 Hint:** Add `THM\Claire` to `Server Admins` on `tvm.loc`. Add `TVM\alice.king` to `Product Admins` on `tbm.thm.loc`.
* **Tier 3 Hint:** Trigger the verification endpoints on `http://192.168.10.250:5000` to retrieve flags from `C:\flag4.txt` on `Server2` and `C:\flag5.txt` on `Server1`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Verify Trust Establishment:**
   * On `FDC1` or `ROOTDC`, verify via PowerShell:
     ```powershell
     Get-ADTrust -Filter *
     ```
   * Confirm bidirectional trust between `thm.loc` and `tvm.loc`.
2. **Execute Delegation 1 (Claire on Server2):**
   * On `FDC1`, open `dsa.msc`.
   * Add `THM\Claire` to `Server Admins`.
   * Navigate to `http://192.168.10.250:5000` -> click **Verify TVM Foreign Trust (Claire)**.
   * On `Server2` (`192.168.10.52`), read `C:\flag4.txt`:
     ```powershell
     Get-Content C:\flag4.txt
     ```
   * Flag 4 output: `THM{3ad3060c-429e-42e0-98ca-318b3227c742}`.
3. **Execute Delegation 2 (alice.king on Server1):**
   * On `CDC1` (`192.168.10.110`), open `dsa.msc`.
   * Add `TVM\alice.king` to `Product Admins`.
   * Navigate to `http://192.168.10.250:5000` -> click **Verify TVM Foreign Trust (alice.king)**.
   * On `Server1` (`192.168.10.51`), read `C:\flag5.txt`:
     ```powershell
     Get-Content C:\flag5.txt
     ```
   * Flag 5 output: `THM{2d159874-69b3-4778-b9f9-13b047fa83a2}`.

---

## Stage E: Final Answer & Key Values

* **Question 1:** Once `Claire` has been enabled to authenticate to Server2, navigate to the flag verification system (`http://192.168.10.250:5000`) and click **Verify TVM Foreign Trust (Claire)** to receive a flag in `C:\flag4.txt` on Server2. What is the flag?
  * **Answer:** `THM{3ad3060c-429e-42e0-98ca-318b3227c742}`
* **Question 2:** Once `alice.king` has been enabled to authenticate to Server1, navigate to the flag verification system (`http://192.168.10.250:5000`) and click **Verify TVM Foreign Trust (alice.king)** to receive a flag in `C:\flag5.txt` on Server1. What is the flag?
  * **Answer:** `THM{2d159874-69b3-4778-b9f9-13b047fa83a2}`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified flags on TryHackMe:
  * Flag 4: `THM{3ad3060c-429e-42e0-98ca-318b3227c742}`
  * Flag 5: `THM{2d159874-69b3-4778-b9f9-13b047fa83a2}`
* Reconciled cross-forest Kerberos ticket referrals and group memberships across domains.

---

## Stage G: Key Educational Takeaway

Forest trusts extend Kerberos authentication across enterprise boundaries via cross-realm ticket referrals. Cross-forest group nestings and delegation can inadvertently open critical administrative pathways between partner organizations.
