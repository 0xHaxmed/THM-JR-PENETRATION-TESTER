# Module 11: Password Attacks & Credential Compromise

---

## 1. Module Header & Metadata

- **English Title:** Module 11: Password Attacks
- **Learning Path:** Junior Penetration Tester (PT1 Aligned Curriculum)
- **Filesystem Identifier:** `11-PASSWORD-ATTACKS`
- **Total Rooms & Challenges:** 6 units (5 core rooms & hands-on challenge labs + 1 topic transition recap)
- **Prerequisites:** Module 03 (Network Reconnaissance), Module 09 (Vulnerability Knowledge), Module 10 (OWASP Top 10 - 2025).

---

## 2. Comprehensive Module Overview

Credentials and authentication tokens represent both the primary defensive perimeter and the most heavily targeted assets in modern penetration testing. This module provides complete theoretical and operational coverage of the entire credential-attack lifecycle:

1. **Social Engineering & Phishing Analysis (Phishing Basics):**
   Dissecting deceptive communication tradecraft, email header analysis (`Received`, `Authentication-Results`, `DKIM`, `SPF`, `DMARC`), payload identification, and credential-harvesting indicators.
2. **Active Online Network Brute-Forcing (Hydra):**
   Mastering the high-speed network authentication attack tool **Hydra** across remote administrative protocols (SSH, FTP, RDP, SMB) and web authentication endpoints (HTTP-GET, HTTP-POST-FORM).
3. **Targeted Wordlist Engineering (Wordlists):**
   Moving beyond generic static dictionaries (`rockyou.txt`) by weaponizing target-specific reconnaissance, utilizing web-scraping dictionary generators (CeWL), and programmatic pattern builders (Crunch, Hashcat rules).
4. **Offline Hash Cracking Tradecraft (Password Cracking):**
   Understanding cryptographic hash structures (MD5, SHA-256, NTLM, NetNTLMv2, bcrypt, Argon2) and leveraging accelerated parallel cracking engines: **John the Ripper** and **Hashcat** using dictionary, mask, and rule-based modes.
5. **Applied Capstone Challenge (Checkmate):**
   A multi-stage capture-the-flag simulation integrating network reconnaissance, credential discovery, brute-forcing, service exploitation, and privilege escalation.

---

## 3. Structural Roadmap & Room Interconnection

```mermaid
graph TD
    M11["Module 11: Password Attacks"] --> R1["01: Phishing Basics (Social Engineering)"]
    M11 --> R2["02: Hydra (Active Network Brute-Forcing)"]
    M11 --> R3["03: Introduction to Wordlists (Dictionary Engineering)"]
    M11 --> R4["04: Password Cracking (Offline Hash Recovery)"]
    M11 --> R5["05: Checkmate (Comprehensive Capstone Challenge)"]
    
    R1 & R2 & R3 & R4 & R5 --> R6["06: Topic Transition Recap (Assessment & Transition)"]
    R6 --> M12["Module 12: Metasploit and Exploitation"]
```
