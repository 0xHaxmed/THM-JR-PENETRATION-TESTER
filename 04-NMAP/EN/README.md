# Module 04: Advanced Network & Port Auditing (Nmap)

---

### 📌 Introduction to Module 04
Created by *Gordon Lyon (Fyodor)* in 1997, **Nmap (Network Mapper)** stands as the global standard and most indispensable utility in a penetration tester's toolkit. Beyond determining whether an individual port is open or closed, Nmap provides deep structural introspection into target TCP/IP stack implementations, firewall filtering behaviors, operating system fingerprints, and vulnerability states.

In this module, we transition from wide-angle reconnaissance (Module 03) into tactical, high-resolution host and port discovery (Vulnerability & Port Auditing), laying the precise operational groundwork required for targeted exploitation.

---

### 🗂️ Module Rooms Curriculum Index

1. [Room 01: Nmap Live Host Discovery](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/01-NMAP-LIVE-HOST-DISCOVERY/README_EN.md)
   * Local network discovery via ARP/ND and routed host discovery across ICMP (Echo, Timestamp, Netmask) and TCP/UDP ping techniques.
2. [Room 02: Nmap Basic Port Scans](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/02-NMAP-BASIC-PORT-SCANS/README_EN.md)
   * Full three-way handshake scanning (TCP Connect `-sT`), half-open stealth scanning (TCP SYN `-sS`), and stateless UDP auditing (`-sU`).
3. [Room 03: Nmap Advanced Port Scans](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/03-NMAP-ADVANCED-PORT-SCANS/README_EN.md)
   * Non-compliant TCP flag manipulation (Null, FIN, Xmas), firewall rule mapping via TCP ACK scan, and blind spoofed attribution via Idle/Zombie Scan (`-sI`).
4. [Room 04: Nmap Post Port Scans](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/04-NMAP-POST-PORT-SCANS/README_EN.md)
   * High-accuracy service version probing (`-sV`), OS fingerprinting heuristics (`-O`), Nmap Scripting Engine (NSE) automation, and multi-format evidence reporting (`-oA`).
5. [Room 05: Topic Transition Recap](file:///e:/THM-JR-PENETRATION-TESTER/04-NMAP/05-TOPIC-TRANSITION-RECAP/README_EN.md)
   * Synthesis review, cross-room assessment quiz, and evaluation before advancing to Web Application Security Fundamentals.

---

### 🛠️ Core Utilities & Protocols
* **Core Engine:** `nmap`
* **Packet Inspection:** `wireshark`, `tcpdump`
* **Protocol Standards:** RFC 793 (TCP), RFC 792 (ICMP), ARP Resolution, IP ID Sequential Behaviors, Raw Sockets, Firewall Traversal.
