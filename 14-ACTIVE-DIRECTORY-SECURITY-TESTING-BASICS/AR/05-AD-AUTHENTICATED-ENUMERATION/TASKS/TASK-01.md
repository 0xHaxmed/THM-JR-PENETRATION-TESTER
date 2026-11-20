# Task 01: Introduction & Lab Network Architecture

---

## Stage A: Original Task Content & Educational Context

In the previous room, *AD: Basic Enumeration*, we explored how to discover domain controllers, interrogate anonymous/null SMB shares, query unauthenticated LDAP endpoints, perform Kerberos pre-authentication user enumeration via Kerbrute, and cycle RIDs across the SAM database.

In this room, **AD: Authenticated Enumeration**, we cross the threshold from an unauthenticated external observer to an authenticated internal threat actor or insider. Once valid domain user credentials are recovered—or harvested through attacks such as AS-REP Roasting—the attack surface expands exponentially. Every authenticated domain account possesses significant baseline read permissions across the entire Active Directory database by default.

### Lab Network Architecture & Target Infrastructure

The dedicated virtual laboratory environment resides in the `10.211.12.0/24` subnet:

| Host Role | Hostname / FQDN | IP Address | Operating System | Purpose / Services Exposed |
|---|---|---|---|---|
| **Domain Controller (DC)** | `DC.tryhackme.loc` | `10.211.12.10` | Windows Server 2019 Datacenter | Active Directory Domain Services (AD DS), DNS (53), Kerberos KDC (88), LDAP (389/636), SMB (445), RPC (135) |
| **Domain Workstation** | `WRK.tryhackme.loc` | `10.211.12.20` | Windows 10 Pro (Build 17763) | Domain-joined endpoint, OpenSSH Server (22), SMB (445), WinRM (5985) |
| **BloodHound Server** | `BH.tryhackme.loc` | `10.211.12.100` | Linux (Ubuntu Server) | BloodHound Community Edition (BloodHound-CE) web GUI & Neo4j backend (`http://10.211.12.100:8080`) |
| **Lab Gateway** | `gateway` | `10.211.12.1` | Linux Gateway | Routing and network isolation |
| **OpenVPN Concentrator** | `vpn` | `10.211.12.250` | OpenVPN Access Server | VPN endpoint using configuration `Jr-Pentester-AD-v01-BH.ovpn` |

### Educational Objectives

1. Verify routing and connectivity to the `10.211.12.0/24` lab subnet via the OpenVPN tunnel (`tun0`).
2. Understand the strategic transition from unauthenticated reconnaissance to authenticated domain querying.
3. Establish the operational methodology: Harvesting initial credentials via AS-REP Roasting $\rightarrow$ Native Living-off-the-Land manual enumeration $\rightarrow$ Automated graph reconnaissance via BloodHound $\rightarrow$ Programmatic querying via PowerShell (`ActiveDirectory` module & `PowerView`).

---

## Stage B: Independent Thinking & Operational Security (OpSec)

When operating in an enterprise Active Directory environment, crossing from unauthenticated to authenticated scanning changes the operational security posture:
- **Authentication Logs:** Every authenticated query creates event logs on the Domain Controller. Specifically, Kerberos Ticket Granting Service (TGS) requests log Event ID `4769`, and Kerberos Authentication Service (AS) requests log Event ID `4768`.
- **LDAP Query Profiling:** Microsoft Defender for Identity (formerly Azure ATP) profiles LDAP search queries. Issuing broad queries such as `(objectClass=*)` across the entire directory root generates anomaly alerts for newly active accounts.
- **OpSec Rule:** Verify your network route and test latency using passive or targeted ICMP probes before firing heavy automated enumeration scripts. Keep local tunnel interfaces clean.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
Ensure your OpenVPN connection is active and that your local kernel routing table directs traffic destined for `10.211.12.0/24` through your `tun0` interface.

### Tier 2: Command Syntax
Verify routing on Linux:
```bash
ip route show | grep 10.211.12
```
Test DC reachability:
```bash
ping -c 2 10.211.12.10
```

### Tier 3: Concrete Verification Line
If the ping fails, restart your OpenVPN client with:
```bash
sudo openvpn --config Jr-Pentester-AD-v01-BH.ovpn
```
and verify `tun0` receives an IP address in the `10.211.12.x` range.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Tunnel Interface Verification
Executing route verification on the attack machine:

```bash
user@attackbox:~$ ip -br a show tun0
tun0             UP             10.211.12.155/24 metric 50

user@attackbox:~$ ip route | grep 10.211.12
10.211.12.0/24 dev tun0 proto kernel scope link src 10.211.12.155
```

### 2. Network Reachability Verification
Probing the Domain Controller (`10.211.12.10`), Workstation (`10.211.12.20`), and BloodHound server (`10.211.12.100`):

```bash
user@attackbox:~$ fping -c 1 10.211.12.10 10.211.12.20 10.211.12.100
10.211.12.10  : [0], 64 bytes, 24.1 ms (avg, min/max = 24.1/24.1/24.1)
10.211.12.20  : [0], 64 bytes, 25.3 ms (avg, min/max = 25.3/25.3/25.3)
10.211.12.100 : [0], 64 bytes, 24.8 ms (avg, min/max = 24.8/24.8/24.8)
```

All three critical target machines respond reliably, confirming that network routing through the OpenVPN tunnel is completely established.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | I am ready to begin exploring authenticated enumeration. | `No answer needed` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

Verification is demonstrated by:
1. Active routing table entry for `10.211.12.0/24` pointing to the tunnel adapter.
2. Low-latency ICMP Echo Replies from Domain Controller `10.211.12.10` and target workstation `10.211.12.20`.
3. Read-readiness of the course module requirements.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Never proceed to domain-level credential attacks or authenticated LDAP queries without verifying tunnel encapsulation and route convergence. In dual-homed or complex enterprise penetration testing engagements, sending domain traffic over the wrong gateway can result in data leakage, unintended disclosure, or false negatives due to packet drops.
