# Comprehensive Documentation: Nmap Advanced Port Scans

---

### 1. Metadata & Room Information
* **Room Title:** Nmap Advanced Port Scans
* **Learning Path:** TryHackMe — Jr Penetration Tester (PT1 Aligned Curriculum)
* **Module:** Module 04: Nmap — Room 03
* **Room Slug:** `nmap03`
* **Difficulty:** Medium / Advanced Foundations
* **Core Focus:** Non-compliant TCP flag manipulation (Null `-sN`, FIN `-sF`, Xmas `-sX`, Maimon `-sM`), firewall rule mapping via ACK scans (`-sA`), source address spoofing (`-S`) and decoys (`-D`), IP packet fragmentation (`-f/-ff`), blind blindfolded IP ID oracle reconnaissance (Idle/Zombie Scan `-sI`), and granular reason reporting (`--reason`).
* **TryHackMe URL:** [https://tryhackme.com/room/nmap03](https://tryhackme.com/room/nmap03)
* **Documentation & Parity Status:** 100% Complete & Synchronized with user's live account.

---

### 2. Architecture & Overview
Transitioning beyond baseline three-way handshakes and half-open SYN sweeps, this laboratory explores the deep, subtle protocol nuances defined in RFC 793 and the historical discrepancies between Unix, BSD, and Microsoft Windows IP stack implementations.

The core objective is exploiting protocol boundary conditions to bypass stateless packet filters and intrusion detection systems while obfuscating attacker attribution through packet fragmentation, decoy flooding, and third-party idle zombie delegation.

---

### 3. Executive Summary
Across 9 structured tasks, this lab covers advanced port scanning and evasion:
1. Deconstructing non-compliant flag combinations: Null Scan (0 flags), FIN Scan (1 flag), and Xmas Scan (3 flags: FIN, PSH, URG).
2. Verifying that Linux systems strictly adhere to RFC 793 by silently discarding probes targeting open ports, yielding 9 `open|filtered` ports in the target environment.
3. Analyzing the BSD-specific flaw utilized in the Maimon Scan (`-sM`), which sets exactly 2 flags (FIN, ACK).
4. Mapping firewall rulesets via TCP ACK scans (`-sA`), discovering 5 unfiltered ports, and unveiling the hidden listening SSL/TLS service on port **`443`**.
5. Applying operational deception using source IP address spoofing (`-S 10.10.10.11`) and decoy arrays (`-D 10.10.20.21,10.10.20.28,ME`) to blind defensive SIEM correlations.
6. Calculating IP fragmentation: dividing a 64-byte TCP segment using `-ff` (16 bytes per fragment) into exactly **`4`** fragments to traverse non-reassembling packet filters.
7. Dissecting the 3-step IP ID oracle mechanism in the TCP Idle Scan (`-sI 10.10.5.5`), enabling zero-packet attribution scans utilizing an idle network printer.
8. Unlocking diagnostic transparency using `--reason` to verify the precise packet response (**`syn-ack`**) responsible for open-port determinations.

---

### 4. Technical Theory & Foundations

#### TCP Idle/Zombie Scan Sequence Architecture
```mermaid
sequenceDiagram
    autonumber
    participant Attacker as Attacker Scanner
    participant Zombie as Idle Zombie Printer (10.10.5.5)
    participant Target as Target Server

    Note over Attacker,Zombie: 1. Probe Baseline Zombie IP ID
    Attacker->>Zombie: SYN/ACK
    Zombie-->>Attacker: RST (IP ID = 31337)

    Note over Attacker,Target: 2. Probe Target Port Spoofing Zombie IP
    Attacker->>Target: TCP SYN (Spoofed Source IP = 10.10.5.5)
    alt Target Port is OPEN
        Target->>Zombie: TCP SYN-ACK
        Zombie-->>Target: TCP RST (Zombie resets unsolicited connection; IP ID increments!)
    else Target Port is CLOSED
        Target->>Zombie: TCP RST
        Note right of Zombie: Zombie ignores incoming RST; IP ID unchanged!
    end

    Note over Attacker,Zombie: 3. Re-probe Zombie to Measure IP ID Delta
    Attacker->>Zombie: SYN/ACK
    Zombie-->>Attacker: RST (New IP ID)
    Note over Attacker: Delta = +2: Target Port is OPEN!<br/>Delta = +1: Target Port is CLOSED / FILTERED!
```

---

### 5. Audit Methodology & Mindset
* **Evasion & Rule Mapping Workflow:**
  1. **Phase 1: Firewall Rule Mapping (`-sA`):** Identify permitted ingress rules and differentiate stateful firewalls from stateless ACLs.
  2. **Phase 2: Anomaly Exploitation (`-sN`, `-sF`, `-sX`):** Leverage non-SYN packets to bypass simple inbound SYN filters against Unix/Linux environments.
  3. **Phase 3: Attribution Obfuscation (`-D` or `-sI`):** Cloak operational scans within background noise using randomized decoys or route entire sweeps through an idle printer zombie.
  4. **Phase 4: Packet Slicing (`-f` / `-ff`):** Fragment TCP headers to bypass signature-based inline filters incapable of real-time stateful reassembly.

---

### 6. Step-by-Step Practical Walkthrough

#### Task 1: Environment Readiness
* Deployed the target lab instances and verified bilateral network reachability.

#### Task 2: TCP Null, FIN, and Xmas Scans
* A Null Scan sets exactly **`0`** flags.
* A FIN Scan sets exactly **`1`** flag (`FIN`).
* A Xmas Scan sets exactly **`3`** flags (`FIN`, `PSH`, `URG`).
* Executing `sudo nmap -sF MACHINE_IP` returned **`9`** ports classified as `open|filtered`.
* Repeating with `sudo nmap -sN MACHINE_IP` returned the identical set of **`9`** `open|filtered` ports.

#### Task 3: TCP Maimon Scan
* The Maimon Scan activates exactly **`2`** control flags (`FIN` and `ACK`).

#### Task 4: TCP ACK, Window, and Custom Scans
* A TCP Window Scan examines incoming RST window sizes using exactly **`1`** control flag (`ACK`).
* Setting the reset bit during a custom scan: `--scanflags` followed by **`RST`**.
* Executing `sudo nmap -sA MACHINE_IP` revealed **`5`** unfiltered ports traversing the firewall.
* Comparing with earlier scans revealed the newly exposed port: **`443`**.
* Validating port 443 with SYN probing confirmed an active web service (**`yea`**).

#### Task 5: Address Spoofing & Decoy Evasion
* Forcing the scan source to appear from IP 10.10.10.11: **`-S 10.10.10.11`**.
* Injecting decoys alongside the true scanning interface: **`-D 10.10.20.21,10.10.20.28,ME`**.

#### Task 6: Fragmented Packets
* Dividing a 64-byte TCP segment using `-ff` (16 bytes per chunk) produces $64 \div 16 =$ **`4`** IP fragments.

#### Task 7: Idle/Zombie Scan Mechanics
* Leveraging an idle printer at 10.10.5.5 as a zombie scanner: **`-sI 10.10.5.5`**.

#### Task 8: Granular Reason Introspection
* Executing `nmap -sS -F --reason MACHINE_IP` verified that open ports are classified based on the receipt of **`syn-ack`** packets.

#### Task 9: Summary & Next Stage Alignment
* Verified technical options and prepared for post-port enumeration (`nmap04`).

---

### 7. Command Analysis & Tool Dissection

```bash
# Production-Grade Obfuscated Firewall Mapping Sweep
sudo nmap -sA -D 10.10.20.21,10.10.20.28,ME -p 1-1000 --reason MACHINE_IP
```
* `sudo`: Required for raw socket manipulation and decoy address forging.
* `-sA`: Maps ingress packet filtering rules via ACK probes.
* `-D ...`: Floods target logs with multiple concurrent spoofed origins.
* `-p 1-1000`: Restricts scope to well-known ports.
* `--reason`: Unveils raw RST response evidence.

---

### 8. Defensive & Blue Team Countermeasures
1. **Stateful Firewall Enforcement:** Drop non-SYN first packets (`INVALID` state in iptables/nftables) to neutralize Null, FIN, Xmas, and unsolicited ACK scans.
2. **Mandatory Deep Packet Reassembly:** Ensure edge inspection engines reassemble fragmented packets before running signature evaluation.
3. **Randomized IP ID Generation:** Modernize OS kernels to enforce per-flow or cryptographically randomized IP ID sequences to prevent Idle/Zombie exploitation.
4. **BCP 38 Ingress/Egress Filtering:** Prevent forwarding packets carrying spoofed internal or external source IPs.

---

### 9. Common Pitfalls & Edge Cases
* **Windows Immunity to Xmas/Null/FIN:** Windows responds with RST to all non-SYN probes regardless of port state; tests will misclassify open services as closed.
* **Blind Spoofing Traps:** Running `-S` without a packet tap on the return path prevents the scanner from receiving target responses.
* **Active Zombie Interference:** Choosing a busy host for an Idle scan corrupts the IP ID counter delta, rendering results invalid.

---

### 10. Key Educational Takeaways
1. RFC compliance differences between Unix and Windows serve as reliable OS fingerprinting signatures.
2. ACK scans do not open ports; they determine whether firewalls permit traffic through specific boundaries.
3. The Idle Scan demonstrates that predictable sequence numbers in lower-layer protocols can leak higher-layer security states.

---

### 11. Verification & Proof of Work
* 100% verification parity achieved across all 9 tasks against the user's live completed TryHackMe account.
* Packet mathematics (4 fragments for 64 bytes with `-ff`), port 443 discovery, and reasoning codes verified against target machine outputs.

---

### 12. Tasks Index & Matrix
* For detailed 7-stage task analyses:
  * [Task 01: Introduction (TASK-01.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-01.md)
  * [Task 02: TCP Null, FIN, and Xmas Scans (TASK-02.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-02.md)
  * [Task 03: TCP Maimon Scan (TASK-03.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-03.md)
  * [Task 04: TCP ACK, Window, and Custom Scans (TASK-04.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-04.md)
  * [Task 05: Spoofing and Decoys (TASK-05.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-05.md)
  * [Task 06: Fragmented Packets (TASK-06.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-06.md)
  * [Task 07: Idle/Zombie Scan (TASK-07.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-07.md)
  * [Task 08: Getting More Details (TASK-08.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-08.md)
  * [Task 09: Summary (TASK-09.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS/TASK-09.md)
* See also [Tasks Index & Answers Summary (TASKS.md)](../../03-NMAP-ADVANCED-PORT-SCANS/TASKS.md).

---

### 13. Additional Technical Notes
* For in-depth RFC 793 comparisons, IP ID state machine diagrams, and fragmentation header fields:
  * [Additional Notes & Deep Dive (ADDITIONAL-NOTES.md)](../../03-NMAP-ADVANCED-PORT-SCANS/ADDITIONAL-NOTES.md)

---

### 14. References & Standard RFCs
* Standard references, RFC 793, RFC 791, and tactical cheat sheet:
  * [Official References & Cheatsheet (REFERENCES.md)](../../03-NMAP-ADVANCED-PORT-SCANS/REFERENCES.md)
