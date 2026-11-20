# Comprehensive Documentation: Nmap Basic Port Scans

---

### 1. Metadata & Room Information
* **Room Title:** Nmap Basic Port Scans
* **Learning Path:** TryHackMe — Jr Penetration Tester (PT1 Aligned Curriculum)
* **Module:** Module 04: Nmap — Room 02
* **Room Slug:** `nmap02`
* **Difficulty:** Easy / Foundations
* **Core Focus:** Dissecting fundamental port auditing techniques (TCP Connect `-sT`, TCP SYN `-sS`, UDP `-sU`), understanding Nmap's six port state models, TCP control flags, and performance tuning (timing templates, parallelism, port ranges).
* **TryHackMe URL:** [https://tryhackme.com/room/nmap02](https://tryhackme.com/room/nmap02)
* **Documentation & Parity Status:** 100% Complete & Synchronized with user's live account.

---

### 2. Architecture & Overview
Following live host discovery, port scanning represents the critical reconnaissance phase where penetration testers map out active listening services across target endpoints.

This room focuses on the mechanical differences between completing full OS-managed handshakes (TCP Connect Scan) versus crafting raw half-open connections (TCP SYN Stealth Scan), while exploring connectionless UDP auditing and kernel-level ICMP rate limiting.

---

### 3. Executive Summary
Across 8 structured tasks, this lab covers the core principles of port scanning:
1. Recognizing standard service port mappings (UDP 53 for DNS, TCP 22 for SSH).
2. Deeply analyzing **The Six Port States** in Nmap (`Open`, `Closed`, `Filtered`, `Unfiltered`, `Open|Filtered`, `Closed|Filtered`) and the network conditions producing them.
3. Inspecting the 20-byte TCP header and the 6 primary control flags (URG, ACK, PSH, RST, SYN, FIN).
4. Executing **TCP Connect Scan (`-sT`)** to map listening ports using standard unprivileged system calls, noting its heavy footprint in application logs.
5. Executing **TCP SYN Stealth Scan (`-sS`)**, intercepting 4 `SYN-ACK` responses and tearing down sessions via immediate `RST` packets.
6. Auditing connectionless **UDP ports (`-sU`)**, interpreting `ICMP Type 3 Code 3` error packets to confirm closed states (e.g. SNMP on port 161).
7. Fine-tuning scan parameters via port range delimiters (`-p5000-5500`), timing templates (`-T0` Paranoid to `-T4` Aggressive), and minimum probe parallelism (`--min-parallelism=64`).

---

### 4. Technical Theory & Foundations

#### Packet Exchange Comparison: Full Handshake (-sT) vs. Half-Open Stealth (-sS)
```mermaid
sequenceDiagram
    autonumber
    participant Attacker as Nmap Scanner
    participant Kernel as Target OS Kernel
    participant App as Target Application (e.g. Apache)

    Note over Attacker,App: 1. TCP Connect Scan (-sT) - Full Handshake
    Attacker->>Kernel: TCP SYN (Port 80)
    Kernel->>Attacker: TCP SYN-ACK
    Attacker->>Kernel: TCP ACK (Connection ESTABLISHED)
    Kernel->>App: accept() syscall -> Connection Handed Over!
    Note right of App: Logged into application access.log immediately!
    Attacker->>Kernel: TCP RST-ACK (Disconnect)

    Note over Attacker,App: 2. TCP SYN Stealth Scan (-sS) - Half-Open Scan
    Attacker->>Kernel: TCP SYN (Port 80)
    Kernel->>Attacker: TCP SYN-ACK (Port is OPEN!)
    Attacker->>Kernel: TCP RST (Abrupt Reset!)
    Note right of Kernel: Socket dropped from kernel backlog
    Note right of App: Application is never notified; leaves no application log!
```

---

### 5. Audit Methodology & Mindset
* **Professional Port Scanning Workflow:**
  1. **Phase 1: Fast Surface Enumeration:** Run a rapid SYN scan on common top ports with aggressive timing:
     `sudo nmap -sS -T4 -n TARGET`
  2. **Phase 2: Comprehensive Background Sweep:** Audit the entire 65,535 TCP port spectrum to capture non-standard administrative interfaces:
     `sudo nmap -sS -p- --min-rate 1000 TARGET -oN all-tcp-ports.txt`
  3. **Phase 3: Targeted UDP Surface Inspection:** Audit high-value UDP services (DNS, SNMP, NTP, TFTP) without triggering severe kernel rate delays:
     `sudo nmap -sU --top-ports 50 TARGET`

---

### 6. Step-by-Step Practical Walkthrough

#### Tasks 1 & 2: Port Fundamentals & Nmap's Six Port States
* The Domain Name System **DNS** defaults to **UDP port 53**.
* Secure Shell **SSH** defaults to **TCP port 22**.
* Nmap recognizes **`6`** distinct port states. The single most valuable state for a penetration tester seeking exploitable entry points is **`Open`**.

#### Task 3: TCP Header & Control Bits
* The 3-letter abbreviation for the Reset flag is **`RST`**.
* The flag required to initiate the TCP three-way handshake and synchronize initial sequence numbers is **`SYN`**.

#### Task 4: TCP Connect Scan (`-sT`)
* Running `nmap -sT MACHINE_IP` confirms that the FTP service on port 21 is in the **`open`** state.
* Nmap matches port 53 against its standard database and identifies the service as **`domain`**.

#### Task 5: TCP SYN Stealth Scan (`-sS`)
* Running `sudo nmap -sS MACHINE_IP` results in exactly **`4`** `SYN-ACK` packets received at the AttackBox interface.
* Target system hosts exactly **`4`** open listening ports (21 FTP, 22 SSH, 53 Domain, 80 HTTP).

#### Task 6: UDP Scanning (`-sU`)
* Querying UDP services reveals port 161 in the **`closed`** state due to returned ICMP Port Unreachable error packets.
* The standard service running on UDP port 161 is **`snmp`** (Simple Network Management Protocol).

#### Task 7: Scope Delimitation & Timing Optimization
* Specifying a contiguous TCP port range between 5000 and 5500: **`-p5000-5500`**.
* Enforcing a minimum baseline of 64 parallel active probes: **`--min-parallelism=64`**.
* Instructing Nmap to run in an ultra-slow, paranoid IDS-evasive timing mode: **`-T0`**.

---

### 7. Command Analysis & Tool Dissection

```bash
# Optimized Production Port Audit Command
sudo nmap -sS -p 1-1000 -T4 --min-parallelism=64 MACHINE_IP -oN basic-scan.txt
```
* `sudo`: Grants raw socket capability to forge SYN frames without completing handshakes.
* `-sS`: Half-open SYN scan avoiding application connection logging.
* `-p 1-1000`: Audits the top 1000 reserved and well-known ports.
* `-T4`: Aggressive timing template reducing round-trip timeout delays.
* `--min-parallelism=64`: Enforces parallel probe generation for predictable execution speed.
* `-oN basic-scan.txt`: Writes human-readable evidence for reporting.

---

### 8. Defensive & Blue Team Countermeasures
1. **Network Intrusion Detection Rules (Snort/Suricata):**
   * Configure alerting thresholds on incoming packets bearing solely the `SYN` flag followed immediately by `RST` teardowns without intermediate data transmission.
2. **Dynamic Host Defense (Fail2ban & Port Knocking):**
   * Obfuscate critical services behind port-knocking daemons.
   * Automate IP firewall bans (`iptables` / `nftables`) upon detecting rapid port traversal.
3. **ICMP Hardening:**
   * Maintain kernel rate-limiting on outgoing ICMP unreachable packets to slow down and disrupt automated UDP discovery.

---

### 9. Common Pitfalls & Edge Cases
* **False Assumption of Total Stealth:** SYN scans evade application logs (`access.log`), but modern network firewalls and IDSs log them with high fidelity.
* **Overlooking UDP Vulnerabilities:** Skipping UDP due to execution duration leaves exposed SNMP credentials and DNS cache poisoning avenues unaddressed.
* **Accidental Connect Scans:** Omitting `sudo` forces Nmap to fall back silently to TCP Connect (`-sT`), creating unwanted noise.

---

### 10. Key Educational Takeaways
1. Full connect scans (`-sT`) complete the handshake and register in application-layer logs; SYN scans (`-sS`) terminate via `RST` before application handoff.
2. The operating system kernel generates `RST` packets for closed TCP ports, and `ICMP Port Unreachable` packets for closed UDP ports.
3. Timing template `-T4` is optimal for controlled lab testing, while `-T0` to `-T1` serialize packets across extended windows to bypass threshold-based detection.

---

### 11. Verification & Proof of Work
* All 8 task questions verified with 100% parity against user's live completed TryHackMe account.
* Active services (FTP 21, SSH 22, DNS 53, HTTP 80, SNMP 161) and port responses validated against lab machine results.

---

### 12. Tasks Index & Matrix
* For detailed 7-stage task analyses:
  * [Task 01: Introduction (TASK-01.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-01.md)
  * [Task 02: TCP and UDP Ports (TASK-02.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-02.md)
  * [Task 03: TCP Flags (TASK-03.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-03.md)
  * [Task 04: TCP Connect Scan (TASK-04.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-04.md)
  * [Task 05: TCP SYN Scan (TASK-05.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-05.md)
  * [Task 06: UDP Scan (TASK-06.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-06.md)
  * [Task 07: Fine-Tuning Scope and Performance (TASK-07.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-07.md)
  * [Task 08: Summary (TASK-08.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS/TASK-08.md)
* See also [Tasks Index & Answers Summary (TASKS.md)](../../02-NMAP-BASIC-PORT-SCANS/TASKS.md).

---

### 13. Additional Technical Notes
* For in-depth kernel rate-limiting mechanics and Snort detection signatures:
  * [Additional Notes & Deep Dive (ADDITIONAL-NOTES.md)](../../02-NMAP-BASIC-PORT-SCANS/ADDITIONAL-NOTES.md)

---

### 14. References & Standard RFCs
* Standard references, RFC 793, RFC 768, and tactical cheat sheet:
  * [Official References & Cheatsheet (REFERENCES.md)](../../02-NMAP-BASIC-PORT-SCANS/REFERENCES.md)
