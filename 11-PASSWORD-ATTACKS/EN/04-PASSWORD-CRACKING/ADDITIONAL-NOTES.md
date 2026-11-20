# Additional Notes & Deep Technical Dive: Password Cracking

## 1. Cryptographic Hash Architecture: Merkle–Damgård vs. Sponge Constructions

Traditional cryptographic hash functions such as MD5, SHA-1, and SHA-256 utilize the **Merkle–Damgård construction**:

```
Message M ---> [ Pad Message & Append Length ]
                        |
                        v
                 +--------------+
  IV ----------> | Comp. Func f |
                 +--------------+
                        |  H1
                        v
                 +--------------+
  Block M2 ----> | Comp. Func f |
                 +--------------+
                        |  H2
                        v
                      [...]
                        |  Hn
                        v
                   Output Hash
```

### Merkle–Damgård Properties:
- Input messages are split into fixed-size blocks (e.g., 512 bits for MD5 and SHA-256).
- A compression function $f$ takes an internal chaining state $H_{i-1}$ and block $M_i$ to produce the next state $H_i$.
- **Vulnerability to Length Extension Attacks:** Because the state after processing $M$ is directly exposed in the final hash output, an attacker can append data to $M$ and calculate $\text{hash}(M \mathbin{\Vert} M')$ without knowing secret prefixes (e.g., $\text{hash}(\text{Secret} \mathbin{\Vert} \text{Data})$).

### Contrast with Modern Password Key Derivation Functions:
General hashes are optimized for sequential block streaming across hardware pipelines. In contrast, modern password algorithms break this sequential streaming to frustrate hardware parallelization:
- **bcrypt:** Runs the Blowfish block cipher through a modified Eksblowfish (Expensive Key Schedule) setup across $2^{\text{cost}}$ iterations.
- **Argon2:** Fills a large pseudo-random memory array (e.g., 64 MB to 1 GB), enforcing frequent sequential memory lookups that saturate GPU VRAM memory buses rather than ALU compute units.

---

## 2. bcrypt Algorithmic Anatomy & Cost Calibration

The structure of a bcrypt hash string is strictly standardized:

$$\underbrace{\$2y\$}_{\text{Algorithm Identifier}} \underbrace{12\$}_{\text{Cost Parameter } (2^{12})} \underbrace{\text{DkQG9sW6vWv2u67O2HhCMe}}_{\text{Radix-64 Encoded Salt (22 chars / 16 bytes)}} \underbrace{\text{P49pYd8qZpS7vV.mO0k1JzU3rE5vLmi}}_{\text{Radix-64 Encoded Ciphertext (31 chars / 24 bytes)}}$$

### The bcrypt Computation Sequence:
1. **Initialize State:** Sets up standard Blowfish P-boxes and S-boxes with digits of $\pi$.
2. **Eksblowfish Setup:** Expands key and salt into internal state through $2^{\text{cost}}$ expansion rounds:
   $$\text{Rounds} = 2^{\text{cost}} \quad (\text{Cost } 12 = 4,096 \text{ rounds}; \text{ Cost } 14 = 16,384 \text{ rounds})$$
3. **Ciphertext Encryption:** Encrypts the 24-byte static string `"OrpheanBeholderScryDoubt"` 64 consecutive times using the generated state.
4. **Output Assembly:** Concatenates the version, cost, salt, and ciphertext.

### Hardware Impact on Password Cracking:
- For MD5, an RTX 4090 computes $\sim 160,000,000,000$ hashes/second.
- For bcrypt (cost 12), the same RTX 4090 computes only $\sim 25,000$ hashes/second.
- This represents a **6,400,000-fold reduction** in attacker throughput!

---

## 3. GPU SIMD Architecture vs. CPU Branching in Offline Cracking

Understanding why Hashcat outperforms John the Ripper on raw hashes requires analyzing CPU vs. GPU silicon architecture:

```
[ CPU: Central Processing Unit ]
- Optimized for low-latency, serial execution
- 8 to 64 large cores with complex branch prediction & massive L3 caches
- Excellent at: Complex format parsing, file unpacking (zip/pdf), irregular branching

[ GPU: Graphics Processing Unit ]
- Optimized for high-throughput, massive SIMD (Single Instruction, Multiple Data) parallelism
- 5,000 to 18,000 tiny ALU shader cores executing parallel vector instructions
- Massive memory bandwidth (e.g. 1 TB/sec on GDDR6X)
- Crushes: Unsalted linear hashes (MD5, SHA-1, SHA-256, NTLM)
```

### SIMD Vector Processing in Hashcat:
When Hashcat attacks MD5 (`-m 0`), each GPU warp/wavefront executes identical arithmetic and bit-shift instructions across 32 or 64 candidate passwords simultaneously. As long as candidates follow the same instruction path (no irregular branching), GPU execution pipelines achieve near 100% compute efficiency.

---

## 4. Comprehensive Hashcat Rule Engine Specification

Rule files transform dictionary words into complex candidates. Below is the advanced syntax reference for custom rule development:

| Operator | Action | Example Input | Example Output |
| :--- | :--- | :--- | :--- |
| `:` | Do nothing (pass-through) | `password` | `password` |
| `l` | Lowercase all characters | `PassWORD` | `password` |
| `u` | Uppercase all characters | `password` | `PASSWORD` |
| `c` | Capitalize first character, lowercase rest | `pASSWORD` | `Password` |
| `C` | Lowercase first character, uppercase rest | `password` | `pASSWORD` |
| `t` | Toggle case of all characters | `PassWord` | `pASSwORD` |
| `r` | Reverse string | `secure` | `eruces` |
| `d` | Duplicate entire string | `pass` | `passpass` |
| `f` | Duplicate and reflect string | `pass` | `passssap` |
| `$X` | Append character $X$ to end | `admin` with `$!` | `admin!` |
| `^X` | Prepend character $X$ to beginning | `admin` with `^#` | `#admin` |
| `sXY` | Replace all occurrences of $X$ with $Y$ | `password` with `sa@` | `p@ssword` |
| `[ ]` | Delete first / last character | `test` with `]` | `tes` |
| `Dk` | Delete character at 0-indexed position $k$ | `secure` with `D1` | `scure` |
| `iKX` | Insert character $X$ at position $K$ | `pass` with `i2_` | `pa_ss` |

### Chaining Rules:
Rules execute sequentially from left to right on a single line:
```text
# Capitalize first character, append '2024', and append '!'
c $2 $0 $2 $4 $!
# Input: "company" -> Output: "Company2024!"
```

---

## 5. John the Ripper `*2john` Extraction Ecosystem

John the Ripper includes purpose-built parsers in `/usr/share/john/` that convert proprietary file formats into cracked hash strings readable by John and Hashcat:

```bash
# SSH Private Keys
ssh2john id_rsa > id_rsa.hash
john --wordlist=rockyou.txt id_rsa.hash

# KeePass Password Manager Databases (.kdbx)
keepass2john Personal.kdbx > keepass.hash
john --wordlist=rockyou.txt keepass.hash
# Hashcat equivalent mode: -m 13400

# Encrypted ZIP Archives
zip2john secure_archive.zip > zip.hash
john --wordlist=rockyou.txt zip.hash
# Hashcat equivalent mode: -m 13600 (WinZip) / -m 17200 (PKZIP)

# Encrypted PDFs
pdf2john document.pdf > pdf.hash
john --wordlist=rockyou.txt pdf.hash
# Hashcat equivalent modes: -m 10400 through -m 10700

# Microsoft Office Documents (.docx, .xlsx, .pptx)
office2john financial_q4.xlsx > office.hash
john --wordlist=rockyou.txt office.hash
# Hashcat equivalent mode: -m 9600 (Office 2013)
```

---

## 6. Enterprise Defensive Standards: NIST SP 800-63B & OWASP

### 1. Storage Standards (OWASP Password Storage Cheat Sheet):
- **Primary Standard:** **Argon2id** with minimum configuration parameters:
  - Memory: $m = 65,536 \text{ KiB}$ (64 MiB)
  - Time iterations: $t = 3$
  - Parallelism: $p = 4$ threads
- **Secondary Standard:** **bcrypt** with minimum work factor:
  - $\text{Work Factor} \ge 12$ ($2^{12} = 4,096$ rounds) for general web applications.
  - $\text{Work Factor} \ge 14$ ($2^{14} = 16,384$ rounds) for high-value / administrative authentication.
- **Legacy Phasing Out:** Immediate retirement of MD5, SHA-1, SHA-256, and NTLM. If legacy systems cannot immediately change underlying schema, implement an **HMAC wrapping layer** with a secret key stored in an HSM:
  $$\text{Stored} = \text{HMAC-SHA256}(\text{bcrypt}(\text{Password}, \text{Salt}), \text{K}_{\text{pepper}})$$

### 2. Password Policy Standards (NIST SP 800-63B Guidelines):
- **Abandon Arbitrary Complexity Mandates:** Forcing users to include 1 uppercase, 1 lowercase, 1 number, and 1 symbol directly drives predictable human behavior (e.g., `Password1!`), rendering mask and rule attacks trivial.
- **Enforce Length over Complexity:** Require a minimum length of at least 12 characters (16 characters recommended for privileged accounts). Entropy scales exponentially with length ($|\Sigma|^L$).
- **Eliminate Periodic Password Expiry:** Periodic rotation causes users to make trivial increments (e.g., `Summer2023!` $\to$ `Fall2023!`). Passwords should only be forced to change upon suspected compromise.
- **Enforce Blacklist / Breach Screening:** Systematically check candidate passwords at registration time against breached corpuses (e.g., HaveIBeenPwned API) and common dictionary words.
