# Task 01: Introduction

---

## Stage A: Original Task Content & Briefing Summary

Task 01 introduces the essential methodology of **Unauthenticated Active Directory Enumeration**. In modern internal penetration testing, red teaming, and network assessments, an operator is often connected to an internal network segment (via a rogue drop-device, compromised Wi-Fi, or guest network) without any valid domain credentials.

### Operational Scope & Objectives:
* When starting from an unauthenticated black-box position, an attacker must map the environment from the outside in:
  1. Discover live hosts and identify the Active Directory Domain Controller (`DC`).
  2. Enumerate accessible network services (SMB, Kerberos, LDAP, RPC).
  3. Identify anonymous access misconfigurations (SMB null sessions, guest shares, anonymous LDAP binds).
  4. Perform user enumeration via MSRPC RID cycling and Kerberos pre-authentication (`Kerbrute`).
  5. Interrogate the domain password and lockout policy.
  6. Execute a disciplined password spraying attack to compromise initial domain credentials without locking out accounts.

### Target Environment & Network Routing:
* Target Subnet: `10.211.11.0/24`
* Domain Name: `tryhackme.loc`
* Domain Controller: `10.211.11.10` (`DC.tryhackme.loc`)
* Member Workstation: `10.211.11.20` (`WRK.tryhackme.loc`)
* Connectivity: AttackBox or OpenVPN configuration (`Jr-Pentester-AD-v01.ovpn`).
* Verifying routes: Ensure `ip route` routes `10.211.11.0/24` through interface `tun0`.

---

## Stage B: Independent Thinking & OpSec Considerations

* **The Silent Pre-Credential Phase:**
  * Unauthenticated enumeration is a critical reconnaissance phase. Interrogating SMB shares, LDAP directories, and RPC endpoints anonymously leaves distinct event logs (Event ID 4624 Type 3 logons with `ANONYMOUS LOGON` / `NT AUTHORITY\ANONYMOUS LOGON`).
  * In modern hardened environments, null sessions on SMB and anonymous LDAP binds are restricted by default (since Windows Server 2008). Discovering them in an enterprise assessment immediately points to legacy compatibility settings or misconfigured server baselines.

---

## Stage C: Guided Hints (Tiers 1–3)

* **Tier 1 Hint:** Read the introduction and verify connectivity to the `10.211.11.0/24` subnet.
* **Tier 2 Hint:** Verify that your VPN adapter (`tun0`) has an established route to the target network.
* **Tier 3 Hint:** Click the completion button to proceed to Task 02.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

1. Establish VPN tunnel using the downloaded configuration:
   ```bash
   sudo openvpn --config Jr-Pentester-AD-v01.ovpn
   ```
2. Verify routing table entry for the lab subnet:
   ```bash
   ip route | grep "10.211.11.0/24"
   ```
3. Test reachability to the target subnet:
   ```bash
   ping -c 2 10.211.11.10
   ```

---

## Stage E: Final Answer & Key Values

* **Question 1:** I am ready to begin exploring unauthenticated enumeration.
  * **Answer:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work

* Verified task completion on TryHackMe.
* Network routing and tunnel parameters confirmed.

---

## Stage G: Key Educational Takeaway

Unauthenticated Active Directory enumeration is the reconnaissance discipline that bridges zero-access network access to verified domain user credentials through the methodical discovery of exposed services, null sessions, and legacy misconfigurations.
