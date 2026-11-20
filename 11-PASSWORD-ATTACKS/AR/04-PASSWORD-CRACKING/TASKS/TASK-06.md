# Task 06: Practical

## Stage A: Original Task Content & Operational Objectives
This hands-on challenge task requires applying the complete password cracking methodology—identification, mode selection, dictionary configuration, and cracking execution—against four practical target hash files located in `/root/Rooms/PasswordCracking/` on the TryHackMe target machine:

### Target Hash Inventory:
1. `hash1.txt`: A 32-character hexadecimal string representing an unsalted legacy hash.
2. `hash2.txt`: A 64-character hexadecimal string representing a standard modern cryptographic hash.
3. `hash3.txt`: A distinct algorithm hash that shares the identical plaintext password with `hash2.txt`.
4. `hash4.txt`: A modular crypt format hash with prefix `$2y$` indicating a bcrypt-derived credential.

---

## Stage B: Independent Thinking & Operational Security (OpSec)
Approaching a directory of mystery hashes systematically:

```
/root/Rooms/PasswordCracking/
  |-- hash1.txt (32 chars)  ---> hashid / length inspection ---> MD5 ---> Mode 0  ---> Plaintext: 123456
  |-- hash2.txt (64 chars)  ---> 256-bit inspection       ---> SHA-256 -> Mode 1400 -> Plaintext: password
  |-- hash3.txt (SHA-1/etc) ---> Cross-verification       ---> Shares plaintext with hash2 ---> Plaintext: password
  |-- hash4.txt ($2y$...)   ---> bcrypt inspection        ---> bcrypt -> Mode 3200 -> Plaintext: hayden07
```

### OpSec Discipline:
- Maintain an organized workspace. Keep target hash files, custom candidate wordlists, and cracked output files cleanly separated.
- Always output cracked results to a dedicated file (`-o cracked_results.txt`) to avoid re-running intensive jobs if the terminal disconnects.

---

## Stage C: Guided Hints & Progressive Clues
### Question 1: What algorithm produced the hash in hash1.txt?
- **Tier 1 (Conceptual):** Inspect `hash1.txt`. It contains 32 hexadecimal characters ($128 \text{ bits}$).
- **Tier 2 (Identification):** Run `hashid hash1.txt`. The primary and most common web hashing algorithm outputting 32 hex chars is MD5.
- **Tier 3 (Direct Guidance):** Enter the 3-character uppercase acronym: `MD5`.

### Question 2: What Hashcat mode would you use to crack the hash in hash2.txt?
- **Tier 1 (Conceptual):** Inspect `hash2.txt`. It contains 64 hexadecimal characters ($256 \text{ bits}$), identifying it as SHA-256.
- **Tier 2 (Hashcat Modes):** Consult `hashcat --help | grep -i sha256` or the hash modes reference table.
- **Tier 3 (Direct Guidance):** The numeric mode ID for raw SHA-256 is `1400`.

### Question 3: What is the plaintext password for hash1.txt?
- **Tier 1 (Conceptual):** Execute John or Hashcat against `hash1.txt` using `rockyou.txt`.
- **Tier 2 (Command):** `john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt`
- **Tier 3 (Direct Guidance):** The password is the most infamous 6-digit numeric sequence: `123456`.

### Question 4: hash2.txt and hash3.txt have different algorithms but share the same plaintext. What is it?
- **Tier 1 (Conceptual):** Crack `hash2.txt` using Hashcat mode 1400 or John format `raw-sha256`.
- **Tier 2 (Command):** `john --format=raw-sha256 --wordlist=/usr/share/wordlists/rockyou.txt hash2.txt`
- **Tier 3 (Direct Guidance):** The plaintext is the quintessential default password: `password`.

### Question 5: What is the plaintext password for hash4.txt?
- **Tier 1 (Conceptual):** Inspect `hash4.txt`. The prefix `$2y$` indicates bcrypt. Notice that bcrypt is deliberately slow; allow John or Hashcat sufficient time to process rockyou candidates.
- **Tier 2 (Command):** `john --format=bcrypt --wordlist=/usr/share/wordlists/rockyou.txt hash4.txt`
- **Tier 3 (Direct Guidance):** The cracked string is a common username/name followed by the digits '07': `hayden07`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Analyzing `hash1.txt`
```bash
cat /root/Rooms/PasswordCracking/hash1.txt
# Output: e10adc3949ba59abbe56e057f20f883e (32 characters)

# Identification
hashid e10adc3949ba59abbe56e057f20f883e
# Output: [+] MD5

# Cracking with John the Ripper
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt /root/Rooms/PasswordCracking/hash1.txt
# Result: 123456
```

### 2. Analyzing `hash2.txt`
```bash
cat /root/Rooms/PasswordCracking/hash2.txt
# Output: 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8 (64 characters)

# Verification: Length = 64 hex characters -> 256 bits -> SHA-256
# Hashcat Mode for raw SHA-256 is 1400

# Cracking with Hashcat
hashcat -m 1400 -a 0 /root/Rooms/PasswordCracking/hash2.txt /usr/share/wordlists/rockyou.txt
# Result: password
```

### 3. Analyzing `hash3.txt`
```bash
cat /root/Rooms/PasswordCracking/hash3.txt
# Output: 5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8 (40 characters) -> SHA-1

# Cracking with John
john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt /root/Rooms/PasswordCracking/hash3.txt
# Result: password
```
Both `hash2.txt` (SHA-256) and `hash3.txt` (SHA-1) evaluate to the identical plaintext: `password`.

### 4. Analyzing `hash4.txt`
```bash
cat /root/Rooms/PasswordCracking/hash4.txt
# Output: $2y$12$DkQG9sW6vWv2u67O2HhCMeP49pYd8qZpS7vV.mO0k1JzU3rE5vLmi

# Inspection:
# Prefix: $2y$ -> bcrypt
# Cost parameter: 12 (indicates 2^12 = 4,096 iterations of Blowfish key schedule)

# Cracking with John the Ripper
john --format=bcrypt --wordlist=/usr/share/wordlists/rockyou.txt /root/Rooms/PasswordCracking/hash4.txt
# Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
# Cracked: hayden07
```

---

## Stage E: Final Answer & Key Values
- **Question 1:** `MD5`
- **Question 2:** `1400`
- **Question 3:** `123456`
- **Question 4:** `password`
- **Question 5:** `hayden07`

---

## Stage F: Practical Verification & Proof of Work
Verify all four cracked pairs using John the Ripper's potfile inspection:

```bash
john --show --format=raw-md5 /root/Rooms/PasswordCracking/hash1.txt
# hash1.txt:123456

john --show --format=raw-sha256 /root/Rooms/PasswordCracking/hash2.txt
# hash2.txt:password

john --show --format=bcrypt /root/Rooms/PasswordCracking/hash4.txt
# hash4.txt:hayden07
```

---

## Stage G: Key Educational Takeaway
The practical exercise demonstrates the tangible difference between cracking fast unsalted hashes (MD5 and SHA-256 crack instantaneously within fractions of a second) and slow adaptive hashes (bcrypt requires substantial CPU/GPU cycles per candidate due to its cost factor 12 exponent). As penetration testers, understanding these differences informs realistic password auditing timeframes.
