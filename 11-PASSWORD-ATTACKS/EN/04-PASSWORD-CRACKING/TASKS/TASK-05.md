# Task 05: Cracking with John the Ripper and Hashcat

## Stage A: Original Task Content & Operational Objectives
This task provides a direct technical comparison and operational walkthrough of the two industry-standard offline password cracking suites: **John the Ripper** and **Hashcat**.

### Key Tool Architectures:
1. **John the Ripper (JtR):**
   - Originally created by Alexander Peslyak (Solar Designer) in 1996.
   - Primary strength: Versatile format auto-detection, CPU-optimized execution, seamless cracking of complex archive/document formats via helper scripts (`zip2john`, `rar2john`, `ssh2john`, `keepass2john`).
   - Maintains a permanent cracked-hash database called a **potfile** (`~/.john/john.pot`).
   - View previously cracked credentials using the `--show` argument.
2. **Hashcat:**
   - The world's fastest and most advanced password recovery utility, engineered from the ground up for massive multi-GPU hardware acceleration via OpenCL and CUDA.
   - Employs a kernel-based architecture that compiles custom GPU compute kernels for specific hash modes.
   - Attack Modes (`-a`):
     - `-a 0`: Straight / Dictionary
     - `-a 1`: Combination (combines words from two dictionaries)
     - `-a 3`: Brute-Force / Mask Attack
     - `-a 6`: Hybrid Wordlist + Mask
     - `-a 7`: Hybrid Mask + Wordlist
   - Features checkpointing and session management (`--session`, `--restore`), allowing long-running multi-day jobs to be paused and resumed without progress loss.

---

## Stage B: Independent Thinking & Operational Security (OpSec)
Choosing between John the Ripper and Hashcat depends on the assessment scope and available hardware:

```
                      [ Cracking Task Encountered ]
                                    |
            +-----------------------+-----------------------+
            |                                               |
   [ Non-Standard Formats / ]                      [ High-Volume Raw Hashes / ]
   [ Archives & SSH Keys    ]                      [ Rapid Hash Cracking      ]
            |                                               |
            v                                               v
   [ John the Ripper ]                             [ Hashcat ]
   - Extracts hashes via `*2john`                  - Massive GPU acceleration
   - CPU-centric multithreading                    - Hundreds of billions hashes/sec
   - Syntax: `john --format=...`                   - Syntax: `hashcat -m <mode> -a <type>`
```

### Operational Best Practices:
- When cracking simple raw cryptographic hashes (MD5, NTLM, SHA-1, SHA-256) in bulk, **Hashcat** is orders of magnitude faster due to GPU SIMD parallelism.
- When cracking proprietary file formats (PGP keys, encrypted PDFs, KeePass databases, BitLocker volumes), **John the Ripper**'s rich extraction tooling (`ssh2john`, `keepass2john`) provides superior out-of-the-box support.

---

## Stage C: Guided Hints & Progressive Clues
### Question 1: A penetration tester needs to crack a large set of SHA-256 hashes as quickly as possible. Which tool is the better choice: John the Ripper or Hashcat?
- **Tier 1 (Conceptual):** Consider which tool is specifically engineered for high-throughput GPU kernel parallelization across massive hash lists.
- **Tier 2 (Architecture):** John runs primarily on CPU threads; the other tool leverages thousands of GPU cores.
- **Tier 3 (Direct Guidance):** Enter the lowercase tool name: `hashcat`.

### Question 2: What Hashcat attack mode number is used for a mask attack?
- **Tier 1 (Conceptual):** Hashcat assigns single-digit integer flags to each attack mode (`-a`).
- **Tier 2 (Mode Listing):** Mode 0 is straight dictionary, Mode 1 is combination. Which number denotes brute-force/mask?
- **Tier 3 (Direct Guidance):** The numeric mode is `3`.

### Question 3: Which John the Ripper flag displays previously cracked passwords from the potfile?
- **Tier 1 (Conceptual):** John saves cracked pairs in `john.pot`. When you run John against an already cracked file, it informs you that no hashes remain.
- **Tier 2 (CLI Flag):** The flag literally instructs John to display or reveal the results.
- **Tier 3 (Direct Guidance):** Include the leading double dashes: `--show`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. John the Ripper Workflow Execution
```bash
# Basic Dictionary Attack on MD5 hashes
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

# Applying JtR internal rule engine
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt --rules=wordlist hashes.txt

# Displaying previously cracked credentials from the potfile
john --show --format=raw-md5 hashes.txt
```
*Sample `--show` Output:*
```text
hashes.txt:admin:5f4dcc3b5aa765d61d8327deb882cf99:password
1 password hash cracked, 0 left
```

### 2. Hashcat Workflow Execution
```bash
# Straight Dictionary Attack (-a 0) against SHA-256 (-m 1400)
hashcat -m 1400 -a 0 sha256_hashes.txt /usr/share/wordlists/rockyou.txt

# Rule-Based Attack applying best64 mutations
hashcat -m 1400 -a 0 sha256_hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# Mask Attack (-a 3) targeting an 8-character password (Upper + 6 Lower + Digit)
hashcat -m 1400 -a 3 sha256_hashes.txt '?u?l?l?l?l?l?l?d'

# Displaying cracked hashes from the Hashcat potfile
hashcat -m 1400 sha256_hashes.txt --show
```

### 3. Session Management and Resumption in Hashcat
When executing extensive multi-hour attacks, jobs should always run inside a named session:
```bash
# Initiate a session named 'engagement_target1'
hashcat -m 1000 -a 0 ntlm.txt rockyou.txt --session=engagement_target1

# In the event of system shutdown or interruption, resume immediately:
hashcat --session=engagement_target1 --restore
```

---

## Stage E: Final Answer & Key Values
- **Question 1:** `hashcat`
- **Question 2:** `3`
- **Question 3:** `--show`

---

## Stage F: Practical Verification & Proof of Work
Verify Hashcat help output confirms attack mode 3 as Mask:

```bash
hashcat --help | grep -A 6 "Attack Mode"
```
*Expected Output snippet:*
```text
- [ Attack Modes ] -

  # | Mode
 ===+======
  0 | Straight
  1 | Combination
  3 | Brute-force / Mask
  6 | Hybrid Wordlist + Mask
  7 | Hybrid Mask + Wordlist
```

Verify John the Ripper help output confirms `--show`:
```bash
john --help | grep -i "\-\-show"
```
*Expected Output snippet:*
```text
--show[=LEFT]              show cracked passwords [or left uncracked]
```

---

## Stage G: Key Educational Takeaway
Mastering both John the Ripper and Hashcat provides complete coverage across the offensive security lifecycle. Use John the Ripper for ad-hoc CPU parsing, custom file formats, and rapid single-hash verification; utilize Hashcat for heavy, GPU-accelerated dictionary, rule, and mask attacks across massive enterprise credential sets.
