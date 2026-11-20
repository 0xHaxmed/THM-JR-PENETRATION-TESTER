# Task 01: Introduction

## Stage A: Original Task Content & Operational Objectives
The primary objective of this task is to introduce the operational mechanics and theoretical foundations of password cracking within an ethical penetration testing engagement. When assessing corporate infrastructure, web applications, or compromised host systems, security professionals frequently recover password hashes from databases, SAM hives, `/etc/shadow` files, Active Directory NTDS.dit dumps, or intercepted network authentication exchanges.

### Key Learning Objectives:
1. Understand cryptographic hash functions and how operating systems and web services store credentials securely.
2. Differentiate between fast cryptographic hashes (e.g., MD5, SHA-1, SHA-256) and slow, adaptive key derivation functions (e.g., bcrypt, Argon2, PBKDF2, scrypt).
3. Identify unknown hash signatures by inspecting byte length, character sets, formatting prefixes, and executing automated identification engines (`hashid`, `hashcat --identify`).
4. Select and configure high-performance offline cracking suites: CPU-optimized John the Ripper and GPU-accelerated Hashcat.
5. Deploy distinct attack strategies tailored to target password complexity policies: dictionary attacks, mask attacks, combinatorial attacks, and rule-based mutations.

---

## Stage B: Independent Thinking & Operational Security (OpSec)
During an authorized penetration testing engagement or red team assessment, offline password cracking introduces distinct OpSec advantages and risk factors:

```
[ Compromised Target ]
        |
        | (Extract Hashes via SQLi / LFI / SAM Dump)
        v
[ Exfiltrate Hashes Out-of-Band ]
        |
        v
[ Isolated Dedicated Cracking Rig (Multi-GPU Rig / Air-Gapped) ]
        |---> Zero network traffic generated against client systems
        |---> Immune to account lockouts and active SIEM detections
        |---> Infinite retry attempts without defensive tripwires
```

### OpSec Considerations:
- **Zero Target Impact:** Because cracking occurs entirely offline on tester-controlled hardware, it generates zero network packets, zero authentication logs, and zero risk of triggering account lockout thresholds (`pam_tally2`, `pam_faillock`, Active Directory BadPwdCount).
- **Client Data Confidentiality:** Hashes extracted from client systems represent sensitive authentication secrets. Under NDAs and compliance standards (e.g., PCI DSS, HIPAA, GDPR), testers must **NEVER** upload target client hashes to public online lookup engines (e.g., `crackstation.net`, `hashes.com`). Online lookups log submitted hashes in public search databases, immediately compromising client confidentiality. All cracking must occur on local, encrypted storage.
- **Hardware Management:** Intensive GPU cracking draws significant electrical power and generates extreme heat. Operators must monitor thermal thresholds (`nvidia-smi` / `hashcat --hwmon-temp-abort=90`) to prevent hardware degradation.

---

## Stage C: Guided Hints & Progressive Clues
### Question 1: I understand the learning objectives and am ready to password-crack!
- **Tier 1 (Conceptual):** This is an introductory acknowledgement question confirming readiness to proceed into password cracking mechanics.
- **Tier 2 (Methodology):** Review the core concepts outlined in the task summary.
- **Tier 3 (Direct Guidance):** No practical command execution is required. Acknowledge the prompt to complete the task.

---

## Stage D: Complete Practical Solution & Technical Deep Dive
To prepare the testing environment on an assessment machine (such as Kali Linux or the TryHackMe AttackBox), verify the installation and operational versions of the essential cracking binaries:

```bash
# Verify John the Ripper installation and supported formats
john --version

# Verify Hashcat installation and OpenCL/CUDA acceleration engines
hashcat --version
hashcat -I

# Verify wordlist availability
ls -lh /usr/share/wordlists/rockyou.txt
```

Understanding the core difference between online authentication attacks (e.g., Hydra, Medusa) and offline hash cracking (e.g., Hashcat, John):
- **Online Attacks:** Bounded by network latency, connection handshakes, daemon response times, and account lockout policies. Typically limited to tens or hundreds of guesses per minute.
- **Offline Attacks:** Bounded solely by local hardware compute capacity (GPU shaders, clock speeds, SIMD vector instructions). Modern GPUs can compute hundreds of billions of MD5 or NTLM hashes per second.

---

## Stage E: Final Answer & Key Values
- **Question 1:** `No answer needed`

---

## Stage F: Practical Verification & Proof of Work
Verify that Hashcat detects local compute devices (CPUs and GPUs) using standard OpenCL/CUDA queries:

```bash
hashcat -I
```
*Expected Output snippet:*
```text
OpenCL Info:
Platform ID #1
  Vendor  : NVIDIA Corporation
  Name    : NVIDIA CUDA
  Device ID #1
    Name          : NVIDIA GeForce RTX 3080
    Type          : GPU
    Compute Units : 68
    Max Clock     : 1710 MHz
    Global Memory : 10240 MB
```

---

## Stage G: Key Educational Takeaway
Offline password cracking decouples authentication cracking from the target's live operational boundaries. Success relies entirely on understanding cryptographic hash formats, obtaining clean hash representations, and applying tailored wordlists and mutation rules that reflect real-world user password generation psychology.
