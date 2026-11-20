# Task 07: Managing Users in AD

---

## Stage A: Original Task Content & Briefing Summary

Task 07 explores the practical administration of user accounts, organizational unit (OU) hierarchies, and fine-grained administrative delegation using **Active Directory Users and Computers** (`dsa.msc`).

### Administrative Exploration:
1. **Enabling Advanced Features:**
   * In `dsa.msc`, navigating to `View -> Advanced Features` reveals hidden containers and properties, specifically the **Security** and **Object** property tabs, essential for inspecting access control entries (ACEs) and attribute-level permissions.
2. **OU Organizational Hierarchy:**
   * Investigating the `People` OU on `CDC1`:
     - Contains departmental sub-OUs organizing employees by business function (IT, Management, Marketing, R&D, Sales, Bankers, etc.).
3. **Security Groups & Membership Inspection:**
   * Investigating security groups under the `Groups` OU.
   * Examining the `Private Clients` group to identify which bankers are assigned to manage private wealth clients.
4. **Delegation of Control Wizard:**
   * Best security practice: Avoid granting full Domain Admin rights to technical support staff.
   * Using the **Delegation of Control Wizard** to grant the `Tech Support` group the specific right to:
     - Reset user passwords and force password change at next logon on the `Bankers` OU.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Delegation Misconfigurations as an Attack Vector:**
  * When administrators use Delegation of Control, it generates explicit Access Control Entries (ACEs) on the OU object in Active Directory.
  * If an attacker compromises a user in `Tech Support`, they inherit the `Reset Password` right over everyone in the `Bankers` OU.
  * In a real penetration test, tools like **BloodHound** identify these ACL attack paths (e.g., `ForceChangePassword`, `GenericAll`, `WriteDacl`), allowing an attacker to hijack user accounts without needing their existing passwords.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Open Active Directory Users and Computers (`dsa.msc`) on `CDC1` and expand the `People` OU.
* **Tier 2 Hint:** Count the departmental sub-OUs in alphabetical/display order to find the 4th department. To count private bankers, check the `Members` tab of the `Private Clients` security group.
* **Tier 3 Hint:** The 4th department is `Marketing`. The number of members in `Private Clients` is `11`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Enumerating Departments in the `People` OU:**
   * Launch `dsa.msc` on `CDC1`.
   * Expand `tbm.thm.loc` -> `People`.
   * The sub-OUs listed are:
     1. `Bankers`
     2. `IT`
     3. `Management`
     4. `Marketing`
     5. `R&D`
     6. `Sales`
   * The fourth department is **`Marketing`**.
2. **Enumerating Members of the Private Clients Group:**
   * Expand `tbm.thm.loc` -> `Groups`.
   * Double-click the `Private Clients` security group.
   * Switch to the **Members** tab.
   * Count the user objects listed.
   * Exactly **11** bankers are present as active members.

---

## Stage E: Final Answer & Key Values

* **Question 1:** What is the fourth department as listed in the People OU?
  * **Answer:** `Marketing`
* **Question 2:** How many bankers are currently servicing private clients?
  * **Answer:** `11`

---

## Stage F: Practical Verification & Proof of Work

* Verified answers on TryHackMe: `Marketing` and `11`.
* Reconciled OU structural layout and group memberships in Active Directory Users and Computers.

---

## Stage G: Key Educational Takeaway

Active Directory allows fine-grained administrative delegation through OUs and ACLs. However, every delegated permission (such as password reset rights) expands the attack surface, creating direct attack paths that penetration testers map and exploit.
