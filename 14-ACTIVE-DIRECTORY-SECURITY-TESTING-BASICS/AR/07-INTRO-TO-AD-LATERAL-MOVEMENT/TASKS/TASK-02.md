# Task 02: What is Lateral Movement? Core Concepts & Prerequisites

---

## Stage A: Original Task Content & Educational Context

Lateral movement is the phase of a cyber attack where an adversary expands their foothold across a network from an initial compromised endpoint toward target objectives (such as the Domain Controller, financial databases, intellectual property repositories, or cloud connectors).

### The Three Pillars of Lateral Movement
Lateral movement relies on three interdependent pillars:

```
                  THE THREE PILLARS OF LATERAL MOVEMENT
  ┌─────────────────────────┬─────────────────────────┬─────────────────────────┐
  │   1. REMOTE EXECUTION   │   2. CREDENTIAL REUSE   │       3. PIVOTING       │
  ├─────────────────────────┼─────────────────────────┼─────────────────────────┤
  │ • PsExec (SMB/SCM)      │ • Pass-the-Hash (PtH)   │ • SSH Dynamic SOCKS     │
  │ • Evil-WinRM (WinRM)    │ • Pass-the-Ticket (PtT) │ • Local Port Forwarding │
  │ • WMI / DCOM            │ • Overpass-the-Hash     │ • Chisel / Ligolo-ng    │
  │ • Scheduled Tasks       │ • Token Impersonation   │ • ProxyChains Routing   │
  └─────────────────────────┴─────────────────────────┴─────────────────────────┘
```

1. **Remote Execution:** The technical mechanism to execute commands or spawn interactive processes on remote hosts across the network (e.g., PsExec over SMB port 445, WinRM over HTTP port 5985/HTTPS 5986, WMI over port 135/dynamic RPC).
2. **Credential Reuse:** Reusing harvested credentials without requiring software exploitation:
   - **Pass-the-Hash (PtH):** Authenticating directly with an NTLM hash without knowing the plaintext password.
   - **Pass-the-Ticket (PtT):** Injecting harvested Kerberos TGT/TGS tickets into memory sessions.
   - **Overpass-the-Hash (Pass-the-Key):** Using an NTLM hash to request a Kerberos TGT from the KDC.
3. **Pivoting:** Routing network traffic through an intermediary compromised machine to reach firewalled or non-routable network segments.

### Prerequisites for Remote Execution
Remote management protocols in Windows are strictly access-controlled:
- To execute commands via **PsExec, WMI, or SMB**, an account must generally belong to the local **`Administrators`** group on the target host.
- To execute commands via **Windows Remote Management (WinRM)**, an account must belong to the local **`Administrators`** group OR the built-in **`Remote Management Users`** group.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### WinRM vs. PsExec from an OpSec Perspective
- **PsExec:**
  - Highly visible and noisy.
  - Connects to `ADMIN$` share, uploads an executable binary to disk (e.g., `C:\Windows\PSEXESVC.exe`), and registers a temporary Windows Service via the Service Control Manager (`svcctl`).
  - Generates **Event ID 7045** ("A service was installed in the system") in the System log and **Event ID 4697** in the Security log.
  - Spawns as `NT AUTHORITY\SYSTEM`.
- **WinRM (Windows Remote Management):**
  - Much stealthier.
  - Communicates over standard WS-Management (SOAP over HTTP/HTTPS).
  - Does **not** write binaries to disk and does **not** create Windows services.
  - Runs inside the security context of the authenticated user (e.g., `thm\jdoe`), logging standard network logon events (**Event ID 4624 Type 3**).

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Recall the famous offensive technique where an attacker passes an NTLM hash directly into the authentication handshake without cracking it.
- Look at built-in Windows security groups; which specific group grants non-admin users permission to access WinRM endpoints?

### Tier 2: Terminology & Group Names
- Hash authentication: The technique name is hyphenated ("Pass-the-Hash").
- Windows group: The group name contains three words: "Remote", "Management", "Users".

### Tier 3: Concrete Answers
- Question 1: `Pass-the-Hash`
- Question 2: `Remote Management Users`

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Pass-the-Hash Protocol Fundamentals
In Windows NTLM authentication, the client proves identity possession by computing an HMAC-MD5 challenge-response using the user's NTLM hash as the key:

$$\text{NTProofStr} = \text{HMAC-MD5}\left(\text{Key}=\text{NTLM}(\text{Password}), \; \text{Data}=\text{ServerChallenge} \parallel \text{ClientChallenge}\right)$$

Because the plaintext password is never input into the cryptographic calculation, possessing the 32-hexadecimal character NTLM hash is completely sufficient to authenticate. This technique is known as **Pass-the-Hash (PtH)**.

### 2. WinRM Access Rights: The `Remote Management Users` Group
Microsoft introduced the built-in local group `Remote Management Users` (SID `S-1-5-32-580`) in Windows Server 2012 / Windows 8. Members of this group are granted:
- Access to the WinRM HTTP/HTTPS listener.
- Permission to invoke WSMAN (Web Services Management) SOAP endpoints.
- Access to execute remote PowerShell sessions via `Enter-PSSession` or `Evil-WinRM`.

This allows system administrators to grant remote management capabilities to operators without giving them full local administrator privileges on the host.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What technique allows you to authenticate using an NTLM hash without knowing the plaintext password? | `Pass-the-Hash` | Verified & Confirmed |
| 2 | What Windows group, other than Administrators, permits WinRM access to a host? | `Remote Management Users` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Concept verification: Impacket tools (`psexec.py`, `wmiexec.py`, `smbexec.py`) support `-hashes :<NTLM_HASH>`, executing successful authentications without plaintext passwords.
2. Group membership verification:
   ```cmd
   net localgroup "Remote Management Users"
   ```
   Confirms members have authorized access to PowerShell remoting endpoints on port 5985.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** WinRM is the modern adversary's preferred remote execution vector when valid credentials are held. Unlike PsExec, WinRM drops no binaries to disk, creates no temporary services, and executes entirely in-memory within the user's security token.
