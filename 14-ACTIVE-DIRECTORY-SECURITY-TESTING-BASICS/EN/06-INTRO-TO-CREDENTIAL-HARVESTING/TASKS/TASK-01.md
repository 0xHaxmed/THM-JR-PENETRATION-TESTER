# Task 01: Introduction & Lab Network Architecture

---

## Stage A: Original Task Content & Educational Context

In Active Directory penetration testing, compromising an enterprise network does not always require finding an unpatched remote code execution vulnerability (e.g., MS17-010 or ZeroLogon). Often, once a local administrative foothold is established on a single client workstation, the primary attack path to full domain compromise is **Credential Harvesting**.

Windows operating systems are designed around the concept of Single Sign-On (SSO). To allow users to access network file shares, printers, web services, and remote desktops without constantly re-entering their passwords, Windows components natively cache, encrypt, and store credentials across several system repositories:
- **LSASS Memory:** Stores plaintext passwords, NTLM hashes, and Kerberos tickets for active sessions.
- **SAM & SYSTEM Registry Hives:** Stores local user password hashes.
- **LSA Secrets:** Caches domain credentials (Domain Cached Credentials - DCC2/MS-Cache v2) for offline logons and stores service account passwords.
- **DPAPI Credential Vaults:** Stores saved web credentials, RDP connections, and Wi-Fi profiles.
- **NTDS.dit (Domain Controller):** The central database holding every domain user, computer, and administrator hash.

### Lab Network Architecture & Target Subnet

The dedicated target network resides in the `10.220.10.0/24` subnet, routed through the OpenVPN client interface (`tun0`):

| Host Role | Hostname / FQDN | IP Address | Operating System | Purpose / Services Exposed |
|---|---|---|---|---|
| **Domain Controller (DC)** | `DC.tryhackme.loc` | `10.220.10.10` | Windows Server 2019 Datacenter | Domain Controller, AD DS, Kerberos (88), LDAP (389), SMB (445), DRSUAPI |
| **Domain Workstation (WRK)** | `WRK.tryhackme.loc` | `10.220.10.20` | Windows 10 Pro | Compromised endpoint with local admin access via RDP (3389) and SMB (445) |
| **Lab Gateway** | `gateway` | `10.220.10.1` | Linux Gateway | Routing and subnet isolation |
| **VPN Concentrator** | `vpn` | `10.220.10.250` | OpenVPN Access Server | VPN endpoint using configuration `Jr-Pentester-Intro-AD-Creds.ovpn` |

### Initial Compromised Foothold Credentials:
- **Target Host:** `10.220.10.20` (`WRK`)
- **Protocol:** RDP (`port 3389`) / SMB (`port 445`)
- **Username:** `Administrator` (Local Administrator)
- **Password:** `N3w34829DJdd?1`

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Privilege Boundaries and the Threat Model
When an adversary gains local administrator privileges on a Windows endpoint:
1. They have the authority to acquire `SeDebugPrivilege` and interact directly with system memory.
2. They can extract cached credentials belonging to **other users who have logged onto the machine**.
3. If an enterprise administrator or Domain Admin previously logged into this workstation (e.g., to troubleshoot an issue or install software), their credentials or hashes remain in memory or in the LSA cache.
4. **OpSec Rule:** Credential harvesting generates significant telemetry. Dumping LSASS memory triggers Event ID `10` in Sysmon (ProcessAccess to `lsass.exe` with `PROCESS_VM_READ` rights). Operators must know when to use in-memory dumping versus offline registry extraction to avoid triggering alarms.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
Verify network connectivity and routing to the target lab subnet `10.220.10.0/24` before launching attacks.

### Tier 2: Routing Inspection
On Linux, check that `tun0` has an IP in the `10.220.10.x` range:
```bash
ip route show | grep 10.220.10
```

### Tier 3: Concrete Verification Line
Ping the target workstation `10.220.10.20` and the Domain Controller `10.220.10.10`:
```bash
ping -c 2 10.220.10.20
```

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Tunnel Interface & Routing Verification
Executing network checks on the attack machine:

```bash
user@attackbox:~$ ip -br a show tun0
tun0             UP             10.220.10.142/24 metric 50

user@attackbox:~$ ip route | grep 10.220.10
10.220.10.0/24 dev tun0 proto kernel scope link src 10.220.10.142
```

### 2. Probing Lab Target Endpoints
Testing latency and reachability to the target hosts:

```bash
user@attackbox:~$ fping -c 1 10.220.10.10 10.220.10.20
10.220.10.10 : [0], 64 bytes, 22.4 ms (avg, min/max = 22.4/22.4/22.4)
10.220.10.20 : [0], 64 bytes, 21.8 ms (avg, min/max = 21.8/21.8/21.8)
```

Both the Domain Controller (`10.220.10.10`) and Workstation (`10.220.10.20`) respond promptly over the VPN tunnel.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | I am connected to the network | `No answer needed` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

Verification is proven by:
1. Active kernel route for `10.220.10.0/24` via `tun0`.
2. Verified ICMP reachability to `10.220.10.20` and `10.220.10.10`.
3. Validated RDP and SMB service availability on port 3389 and 445 on `10.220.10.20`.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Never overlook the power of local administrative footholds. In an Active Directory domain, a single compromised workstation with cached administrative credentials is often all that separates an attacker from total domain takeover.
