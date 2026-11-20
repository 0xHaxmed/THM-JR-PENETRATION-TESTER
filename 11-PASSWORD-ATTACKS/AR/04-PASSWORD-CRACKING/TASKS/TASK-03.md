# Task 03: Identifying Hash Types

## Stage A: Original Task Content & Operational Objectives
Before executing any offline password cracking tool (such as John the Ripper or Hashcat), a penetration tester must accurately determine the underlying hashing algorithm. Feeding an unrecognized hash or specifying an incorrect mode leads to wasted computational cycles, syntax errors, or false negatives.

### Key Concepts:
1. **Visual Characteristics & Fingerprinting:**
   - **MD5 / NTLM:** 32 hexadecimal characters (128 bits, represented in hex where each character is 4 bits: $128 / 4 = 32$).
   - **SHA-1:** 40 hexadecimal characters (160 bits: $160 / 4 = 40$).
   - **SHA-256:** 64 hexadecimal characters (256 bits: $256 / 4 = 64$).
   - **SHA-512:** 128 hexadecimal characters (512 bits: $512 / 4 = 128$).
   - **Modular Crypt Format:** Hashes formatted as `$id$salt$hash`. For instance, `$1$` indicates MD5-crypt, `$5$` indicates SHA-256-crypt, `$6$` indicates SHA-512-crypt, and `$2a$` / `$2b$` / `$2y$` indicates bcrypt.
2. **Contextual Disambiguation:**
   - Both **MD5** and **NTLM** output 32-character hexadecimal strings.
   - If the hash was dumped from a Windows SAM database or Active Directory domain controller (via Mimikatz, secretsdump.py, or Volume Shadow Copy), it is virtually guaranteed to be **NTLM** (Hashcat mode `-m 1000`, John format `--format=nt`).
   - If the hash was extracted from a legacy CMS, MySQL user table, or custom PHP web app, it is typically **MD5** (Hashcat mode `-m 0`, John format `--format=raw-md5`).
3. **Automated Identification Utilities:**
   - `hashid`: Python-based signature matcher that outputs candidate algorithms ordered by likelihood.
   - `hashcat --identify`: Hashcat's built-in identification engine that maps candidate strings directly to internal Hashcat modes (`-m`).

---

## Stage B: Independent Thinking & Operational Security (OpSec)
When identifying hashes during an assessment:

```
[ Unidentified Hash Extracted ]
              |
              +---> Check Length & Character Set
              |        |-- 32 Hex  -> Context: Windows (NTLM) vs Web/DB (MD5)
              |        |-- 40 Hex  -> SHA-1
              |        |-- 64 Hex  -> SHA-256
              |        |-- Prefix  -> $2y$ (bcrypt), $6$ (SHA-512 crypt)
              |
              +---> Run Automated Local CLI Identifier:
              |        `hashid '<hash>'` OR `hashcat --identify '<hash>'`
              |
              +---> [ STRICT OPSEC MANDATE ]:
                       NEVER upload client hashes to online databases (e.g. CrackStation)!
```

### OpSec Warning:
Online cracking services (such as CrackStation, OnlineHashCrack, or MD5Decrypt) retain query logs. If a penetration tester pastes a sensitive hash belonging to an enterprise client, that hash and any associated context may become public knowledge, violating customer confidentiality agreements. Always utilize offline command-line identification tools.

---

## Stage C: Guided Hints & Progressive Clues
### Question 1: What is the character length of an SHA-256 hash?
- **Tier 1 (Conceptual):** A SHA-256 hash produces 256 bits of cryptographic output.
- **Tier 2 (Calculation):** When represented in hexadecimal, each hexadecimal digit represents 4 bits ($2^4 = 16$). Divide 256 by 4.
- **Tier 3 (Direct Guidance):** The numeric answer is 64.

### Question 2: What John the Ripper format would you use for an SHA-256 hash?
- **Tier 1 (Conceptual):** John the Ripper distinguishes between salted system hashes (like `/etc/shadow`) and raw standalone cryptographic hashes.
- **Tier 2 (Syntax Convention):** The format prefix for an unformatted, unsalted hash is `raw-` followed by the lowercase algorithm name.
- **Tier 3 (Direct Guidance):** Combine `raw-` with `sha256`: `raw-sha256`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Hash Identification Cheatsheet
Below is the definitive cross-reference matrix between visual traits, John the Ripper formats, and Hashcat modes:

| Hash Algorithm | Hex Length | Example Prefix | Hashcat Mode (`-m`) | John the Ripper Format (`--format=`) |
| :--- | :--- | :--- | :--- | :--- |
| **MD5** | 32 | None | `0` | `raw-md5` |
| **NTLM** | 32 | None | `1000` | `nt` |
| **SHA-1** | 40 | None | `100` | `raw-sha1` |
| **SHA-256** | 64 | None | `1400` | `raw-sha256` |
| **SHA-512** | 128 | None | `1700` | `raw-sha512` |
| **Linux MD5-crypt** | Variable | `$1$` | `500` | `md5crypt` |
| **Linux SHA-512-crypt** | Variable | `$6$` | `1800` | `sha512crypt` |
| **bcrypt** | ~60 | `$2a$`, `$2b$`, `$2y$` | `3200` | `bcrypt` |

### 2. Practical Execution of Identification Commands
Execute `hashid` against various hash samples:

```bash
# Identifying an SHA-256 hash (64 hex characters)
hashid '5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8'
```
*Expected Output:*
```text
Analyzing '5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8'
[+] SHA-256
[+] Haval-256
[+] RIPEMD-256
[+] GOST R 34.11-94
```

Execute `hashcat --identify`:
```bash
hashcat --identify '5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8'
```
*Expected Output snippet:*
```text
The following Hash modes might be what you're looking for:

  1400 | SHA2-256                                  | Raw Hash
  1410 | sha256($pass.$salt)                       | Raw Hash salted and/or iterated
  1420 | sha256($salt.$pass)                       | Raw Hash salted and/or iterated
```

---

## Stage E: Final Answer & Key Values
- **Question 1:** `64`
- **Question 2:** `raw-sha256`

---

## Stage F: Practical Verification & Proof of Work
Verify that John the Ripper lists `raw-sha256` as an officially supported sub-format:

```bash
john --list=formats | grep -i raw-sha256
```
*Expected Output:*
```text
raw-sha256, raw-sha256-free-opencl, raw-sha256-opencl
```

---

## Stage G: Key Educational Takeaway
Correct hash identification is the prerequisite for all password cracking workflows. By mastering bit lengths, hexadecimal conversions (e.g., 256 bits $\div$ 4 = 64 characters), prefix conventions, and system context (Windows NTLM vs web MD5), penetration testers can instantly parameterize Hashcat and John the Ripper for successful execution.
