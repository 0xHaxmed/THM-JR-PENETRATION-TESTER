# Task 09: Managing Computers in AD

---

## Stage A: Original Task Content & Briefing Summary

Task 09 covers the structural organization and architectural management of computer objects in Active Directory.

### Organizational Architecture for Computer Accounts:
* **The Default `Computers` Container:**
  * When any computer joins an Active Directory domain, its machine object is placed in the default `CN=Computers` container (`CN=Computers,DC=tbm,DC=thm,DC=loc`).
  * Crucially, `CN=Computers` is a generic system **container**, not an **Organizational Unit (OU)**. Group Policy Objects (GPOs) **cannot** be directly linked to the default `Computers` container.
* **Tiered OU Best Practices (Tiering Model):**
  * To implement least-privilege administrative access and role-specific GPOs, organizations create dedicated OUs:
    1. **Domain Controllers:** Pre-existing default OU housing the DCs.
    2. **Servers:** Infrastructure hosts running enterprise databases, web servers, and back-office applications.
    3. **Workstations:** End-user laptops, desktops, and clients.
* **Hands-on Task Objective:**
  * Organize all computer objects in `CDC1`:
    - Move all server systems into the `Servers` OU.
    - Move all workstation/laptop systems into the `Workstations` OU.
  * Count how many machine objects reside within the `Workstations` OU once organization is complete.

---

## Stage B: Independent Thinking & OpSec Considerations

* **Enterprise Tiering and Credential Hygiene:**
  * Microsoft's Enterprise Access Model (and legacy Red Forest Tier 0/1/2 design) mandates that privileged credentials from Tier 0 (Domain Controllers) or Tier 1 (Servers) must **never** log onto Tier 2 (Workstations).
  * If a Domain Admin logs onto a workstation in the `Workstations` OU, their Kerberos tickets and NTLM credentials are cached in that workstation's LSASS process memory. If an attacker has local administrative access to that single workstation, they dump LSASS and achieve instant domain dominance.
  * Organizing machines into distinct OUs allows enforcing these restrictions via GPO (e.g. "Deny log on through Remote Desktop Services" for privileged groups).

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Open Active Directory Users and Computers (`dsa.msc`) on `CDC1`.
* **Tier 2 Hint:** Move all workstation and desktop computers out of `CN=Computers` into the `Workstations` OU.
* **Tier 3 Hint:** Count the machine accounts in `Workstations`. Exactly `3` computers are located there.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. **Access Active Directory Users and Computers:**
   * Open `dsa.msc` on `CDC1` (`192.168.10.110`).
   * Expand `tbm.thm.loc` -> `Computers`.
2. **Move Computer Objects:**
   * Identify all endpoint workstations (e.g., `LAPTOP-01`, `PC-SALES-01`, `DESKTOP-HR`).
   * Drag or right-click -> **Move** -> select `Workstations` OU.
   * Move remaining servers to the `Servers` OU.
3. **Count Workstation Objects:**
   * Open the `Workstations` OU.
   * Total machine objects verified: **3**.

---

## Stage E: Final Answer & Key Values

* **Question 1:** After organising the available computers, how many ended up in the Workstations OU?
  * **Answer:** `3`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed verified answer on TryHackMe: `3`.
* Reconciled OU organizational hierarchy in Active Directory Users and Computers console.

---

## Stage G: Key Educational Takeaway

Separating computer objects into distinct OUs (Workstations vs. Servers) is essential for applying targeted Group Policy Objects and enforcing credential hygiene boundaries (Tiered Access Architecture).
