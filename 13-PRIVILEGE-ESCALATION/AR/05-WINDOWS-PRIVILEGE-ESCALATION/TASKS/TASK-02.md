# Task 02: Windows Privilege Escalation

---

## Stage A: Original Task Content & Briefing Summary

Task 02 establishes the foundational user hierarchy and account architecture in Windows operating systems. Privilege escalation is formally defined as leveraging access as User A to gain access to the resources and authority of User B (typically an administrative or higher-privileged context).

### Windows User Classification & Account Types:
1. **Administrators Group (`BUILTIN\Administrators`):**
   * Members of this local group possess full control over the operating system. They can install programs, modify services, manage hardware drivers, create users, alter security policies, and access all files on the filesystem.
2. **Standard Users (`BUILTIN\Users`):**
   * Can perform daily computational tasks and run applications. Permissions are strictly confined to their own user profile (`C:\Users\<username>`). They cannot alter system-wide configurations, modify program files, or write to sensitive system directories (`C:\Windows`, `C:\Program Files`).
3. **SYSTEM (`NT AUTHORITY\SYSTEM` / `LocalSystem`):**
   * The core internal operating system account. **The SYSTEM account possesses higher privileges than the Administrator user.** It owns and runs core kernel-mode drivers and background OS services, possessing total, unconstrained control over all local hardware, memory, and filesystem resources.
4. **Local Service (`NT AUTHORITY\LOCAL SERVICE`):**
   * A minimal-privilege service account used by background services that only require local system access. Connects anonymously to network shares without domain machine credentials.
5. **Network Service (`NT AUTHORITY\NETWORK SERVICE`):**
   * A minimal-privilege service account used by services that require network interaction. Authenticates to external domain network resources using the computer account credentials (`<COMPUTERNAME>$`).

---

## Stage B: Independent Thinking & OpSec Considerations

* **Administrator vs. SYSTEM:** While members of the `Administrators` group can elevate their processes to SYSTEM through various techniques (such as installing services or kernel drivers), standard administrative tokens operate at `High` integrity, whereas `LocalSystem` operates at `SYSTEM` integrity. Certain protected operating system files and memory segments (such as LSASS memory) are strictly accessible only by SYSTEM or processes holding specialized debug privileges.
* **UAC Token Splitting:** On modern Windows systems with User Account Control (UAC) enabled, administrative accounts log in with a split token: a filtered standard user token for regular tasks, and an elevated token requiring explicit approval for administrative operations.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read through the account definitions in the task briefing.
* **Tier 2 Hint:** Identify which group possesses rights to change system configurations. Compare the privilege level of SYSTEM vs Administrator.
* **Tier 3 Hint:**
  * Q1 is `Administrators`.
  * Q2 is `aye`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Identifying the Administrative Group:
In Windows security architecture, the `Administrators` group is granted administrative rights over the local machine via security identifiers (SID: `S-1-5-32-544`).
* Command verification:
  ```cmd
  net localgroup Administrators
  ```
* **Answer to Q1:** `Administrators`

### 2. Comparing SYSTEM vs. Administrator:
The `NT AUTHORITY\SYSTEM` account operates at the kernel/OS layer and is not subject to User Account Control (UAC) prompts or standard administrative restrictions. It inherently holds almost all token privileges enabled by default.
* **Answer to Q2:** `aye`

---

## Stage E: Final Answer & Key Values

* **Question 1:** Users that can change system configurations are part of which group?
  * **Answer:** `Administrators`
* **Question 2:** The SYSTEM account has more privileges than the Administrator user (aye/nay)
  * **Answer:** `aye`

---

## Stage F: Practical Verification & Proof of Work

* Confirmed Windows user definitions and group mappings.
* Reconciled verified answers on live TryHackMe account.

---

## Stage G: Key Educational Takeaway

Understanding the distinction between standard users, administrators, and `NT AUTHORITY\SYSTEM` defines the target objectives of Windows privilege escalation. While administrative access provides control, obtaining a SYSTEM context is the gold standard for full persistence, token manipulation, and unrestricted access.
