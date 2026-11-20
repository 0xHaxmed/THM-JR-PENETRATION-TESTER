# Task 01: Introduction & Lab Network Architecture

---

## Stage A: Original Task Content & Educational Context

In Active Directory environments, initial access (whether obtained through phishing, external web vulnerability exploitation, or password spraying) rarely lands directly on a high-value asset such as a Domain Controller, database cluster, or executive workstation. Instead, initial compromise typically occurs on an unprivileged perimeter machine or a low-value user workstation.

**Lateral Movement** refers to the techniques and methodologies adversaries use to extend their control from an initial compromised endpoint across other systems on the network. The attacker repeatedly cycles through a fundamental progression:
$$\text{Compromise Host} \longrightarrow \text{Harvest Local Credentials/Hashes} \longrightarrow \text{Move Laterally to Adjacent Systems} \longrightarrow \text{Escalate Privileges}$$

### Lab Network Architecture & Target Infrastructure

The dedicated lab environment simulates a realistic segmented corporate network (`thm.loc`):

| Host Role | Hostname / FQDN | IP Address | Operating System | Purpose / Services Exposed |
|---|---|---|---|---|
| **Pivot WebServer** | `git.thm.loc` | `192.168.13.71` | Linux (Ubuntu Server) | Compromised Linux web server hosting internal Gitea (port 80) and OpenSSH (port 22). Serves as our primary pivot point. |
| **Workstation (WRK)** | `WRK1.thm.loc` | `192.168.13.61` | Windows 10 (Build 17763) | Domain-joined client machine. SMB (445), WinRM (5985). User `jdoe` has local Administrator access. |
| **Member Server (SERVER1)** | `SERVER1.thm.loc` | `192.168.13.51` | Windows Server 2019 | Domain member server. SMB (445), WinRM (5985). User `jdoe` belongs to `Remote Management Users`. |
| **Domain Controller (ROOTDC)** | `RDC1.thm.loc` (`thm.loc`) | `192.168.13.100` | Windows Server 2019 | Primary Domain Controller. Filtered from direct external access; requires SSH pivoting. |
| **Lab Gateway** | `gateway` | `192.168.13.1` | Linux Gateway | Routing and firewall boundary. |

### Initial Compromised Foothold Credentials:
- **Pivot Host:** `192.168.13.71:22` (SSH)
- **Username:** `jdoe`
- **Password:** `Summer2026!`
- **Domain:** `thm.loc`

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Network Segmentation and Routing Reality
In modern enterprise architectures, Domain Controllers and Tier-0 infrastructure are isolated on dedicated management VLANs. Direct IP routing from external VPN connections is blocked by network firewalls.
- **Pivoting Necessity:** In this lab, attempting to connect directly to the Domain Controller (`192.168.13.100`) or the internal Gitea service (`192.168.13.71:80`) from the AttackBox will result in connection timeouts (`EHOSTUNREACH` or `ETIMEDOUT`).
- **OpSec Rule:** Use compromised internal dual-homed hosts as pivot gateways via encrypted SSH dynamic tunnels (`ssh -D`) or local port forwarders (`ssh -L`) rather than attempting noisy port scanning against firewalled gateways.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
Verify your OpenVPN tunnel connection using configuration `ad-introlatmov.ovpn` and test reachability to the initial pivot host `192.168.13.71`.

### Tier 2: DNS & Hosts Configuration
Add target domain mappings to `/etc/hosts` or point DNS resolution to the Domain Controller:
```bash
echo "192.168.13.100 thm.loc" | sudo tee -a /etc/hosts
echo "192.168.13.71 git.thm.loc" | sudo tee -a /etc/hosts
```

### Tier 3: Concrete Verification Line
Test SSH connectivity to the pivot host:
```bash
ssh -o StrictHostKeyChecking=no jdoe@192.168.13.71
```

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Tunnel Interface Verification
Checking network configuration on the attack machine:

```bash
user@attackbox:~$ ip -br a show tun0
tun0             UP             192.168.13.180/24 metric 50

user@attackbox:~$ ip route | grep 192.168.13
192.168.13.0/24 dev tun0 proto kernel scope link src 192.168.13.180
```

### 2. Validating Initial Access to Pivot Host
Authenticating via SSH to WebServer `192.168.13.71`:

```bash
user@attackbox:~$ ssh jdoe@192.168.13.71
jdoe@192.168.13.71's password: Summer2026!

Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-166-generic x86_64)

jdoe@webserver:~$ id
uid=1001(jdoe) gid=1001(jdoe) groups=1001(jdoe)

jdoe@webserver:~$ ip -br a
lo               UNKNOWN        127.0.0.1/8 ::1/128 
eth0             UP             192.168.13.71/24
```

Connectivity to the initial pivot host is established.

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | I'm ready to move laterally! | `No answer needed` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Kernel route for `192.168.13.0/24` pointing to `tun0` verified.
2. Successful SSH authentication to `192.168.13.71` with credentials `jdoe:Summer2026!`.
3. Read-readiness of the course module requirements confirmed.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Penetration testing does not occur in a flat, unsegmented vacuum. Real-world Active Directory assessments require mastering network pivoting. A Linux or Windows host with standard network routing into internal subnets is your bridgehead into the internal domain.
