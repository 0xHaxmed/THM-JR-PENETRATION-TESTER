# Task 04: Wordlists and Attack Strategies

## Stage A: Original Task Content & Operational Objectives
Effective offline password cracking does not rely on brute-force guessing across random character spaces. Instead, professional penetration testers employ distinct attack strategies structured around human behavioral patterns, corporate password policies, and contextual reconnaissance.

### Key Concepts:
1. **Dictionary Attack:**
   - Evaluates a pre-compiled list of real-world passwords sequentially against the target hash.
   - The foundational standard on Linux security distributions (Kali Linux, Parrot OS, TryHackMe AttackBox) is **RockYou**:
     $$\text{Path: } \texttt{/usr/share/wordlists/rockyou.txt}$$
     Contains 14,341,564 unique passwords harvested during the 2009 RockYou social application breach.
2. **Brute-Force Attack:**
   - Exhaustively iterates through every possible permutation of a defined character set up to a maximum length.
   - Suffers from exponential combinatorial explosion:
     $$N = |\Sigma|^L$$
     For an 8-character password utilizing lowercase letters, uppercase letters, and digits ($|\Sigma| = 26 + 26 + 10 = 62$):
     $$N = 62^8 \approx 218,340,105,584,896 \text{ combinations}$$
     Brute force is only viable for low-entropy targets (such as 4-to-6 digit numeric PINs or very short alphanumeric tokens).
3. **Rule-Based Attack:**
   - Takes base words from an existing wordlist and subjects them to programmatic mutations (rules) mimicking how humans modify passwords to comply with complexity mandates:
     - Capitalizing the first character (`c`)
     - Appending current or past years (`$2 $0 $2 $4`)
     - Leetspeak substitutions (`sa@`, `se3`, `si!`, `so0`)
     - Prepending or appending special characters (`$!`)
   - Pre-installed Hashcat rules reside in `/opt/hashcat/rules/` or `/usr/share/hashcat/rules/` (`best64.rule`, `rockyou-30000.rule`, `d3ad0ne.rule`).
4. **Mask Attack:**
   - Employs known structural patterns (templates) to target passwords governed by predictable enterprise password policies.
   - Example policy: "Must start with a capital letter, followed by lowercase letters, and end with two digits" (e.g., `Company24`).
   - Hashcat mask notation: `?u?l?l?l?l?l?l?d?d`
     - `?l`: Lowercase letters (`a-z`)
     - `?u`: Uppercase letters (`A-Z`)
     - `?d`: Digits (`0-9`)
     - `?s`: Special characters (`!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~`)
     - `?a`: All printable ASCII characters

---

## Stage B: Independent Thinking & Operational Security (OpSec)
Selecting the optimal attack strategy optimizes hardware performance and time-to-compromise:

```
                  [ Unknown Target Hash ]
                             |
             +---------------+---------------+
             |                               |
    [ Low Complexity / ]           [ Strict Enterprise ]
    [ Common Password  ]           [ Complexity Policy ]
             |                               |
             v                               v
    [ Dictionary Attack ]          [ Policy Analysis ]
    (rockyou.txt: 14M words)                 |
             |                               +---> Mask Known? (e.g., Uppercase + Word + Year)
             |                               |        |--> YES: [ Mask Attack ]
             v                               |
    [ Rule-Based Attack ] <------------------+--------|--> NO:  Apply Rule Transformations
    (best64.rule, OneRuleToRuleThemAll)
```

### Operational Guidance:
- Always begin cracking with a fast **Dictionary Attack** using `rockyou.txt` (takes seconds for fast hashes).
- If standard dictionary candidates fail, escalate to **Rule-Based Attacks** using lightweight rule sets (`best64.rule`), progressing to comprehensive rules (`OneRuleToRuleThemAll` / `rockyou-30000.rule`).
- If reconnaissance reveals corporate naming conventions or active password complexity requirements (e.g., Season + Year + Symbol), bypass dictionary entirely in favor of targeted **Mask Attacks**.

---

## Stage C: Guided Hints & Progressive Clues
### Question 1: What is the full path to the rockyou wordlist on the AttackBox?
- **Tier 1 (Conceptual):** Standard Unix wordlists reside under the global shared data directory `/usr/share/`.
- **Tier 2 (Path Structure):** Under `/usr/share/`, there is a dedicated folder for lists of words.
- **Tier 3 (Direct Guidance):** The absolute path is `/usr/share/wordlists/rockyou.txt`.

### Question 2: What type of attack applies transformations such as capitalisation and number suffixes to an existing wordlist?
- **Tier 1 (Conceptual):** This attack modifies base dictionary candidates using algorithmic instructions called "rules".
- **Tier 2 (Terminology):** The name describes an attack driven by defined rules.
- **Tier 3 (Direct Guidance):** Enter the hyphenated adjective: `rule-based`.

### Question 3: A password policy requires all passwords to start with a capital letter followed by six lowercase letters and two digits. What attack type is best suited to cracking a hash of such a password?
- **Tier 1 (Conceptual):** When the exact position and character class of every character in the password are known, an attack utilizing placeholders (masks) is optimal.
- **Tier 2 (Terminology):** The attack replaces static positions with dynamic sets like `?u` and `?d`.
- **Tier 3 (Direct Guidance):** The four-letter word is `mask`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Hashcat Mask Placeholders Table
| Placeholder | Character Class | Equivalent Set | Set Size |
| :--- | :--- | :--- | :--- |
| `?l` | Lowercase letters | `abcdefghijklmnopqrstuvwxyz` | 26 |
| `?u` | Uppercase letters | `ABCDEFGHIJKLMNOPQRSTUVWXYZ` | 26 |
| `?d` | Digits | `0123456789` | 10 |
| `?h` | Lowercase hex | `0123456789abcdef` | 16 |
| `?H` | Uppercase hex | `0123456789ABCDEF` | 16 |
| `?s` | Special symbols | ` !"#$%&'()*+,-./:;<=>?@[\]^_`{\|}~` | 33 |
| `?a` | All ASCII printable | `?l?u?d?s` | 95 |
| `?b` | All 8-bit bytes | `0x00 - 0xff` | 256 |

### 2. Combinatorial Space Calculation
Consider Question 3's policy: 1 capital letter (`?u`), 6 lowercase letters (`?l?l?l?l?l?l`), and 2 digits (`?d?d`):
$$\text{Total Combinations} = 26^1 \times 26^6 \times 10^2 = 26^7 \times 100$$
$$26^7 = 8,031,810,176$$
$$\text{Total Search Space} = 8,031,810,176 \times 100 = 803,181,017,600 \text{ candidates}$$
On a modern RTX 4090 GPU calculating NTLM hashes at 150 GH/s ($150 \times 10^9$ hashes/sec), this entire mask attack completes in under **5.4 seconds**!

### 3. Rule Engine Syntax in Hashcat
Rules allow fine-grained mutation. Common rule operators include:
- `c`: Capitalize first letter and lowercase the rest (`password` $\to$ `Password`).
- `u`: Uppercase all characters (`password` $\to$ `PASSWORD`).
- `$1`: Append digit '1' (`password` $\to$ `password1`).
- `^!`: Prepend exclamation mark (`password` $\to$ `!password`).
- `sa@`: Replace all occurrences of 'a' with '@' (`password` $\to$ `p@ssword`).

---

## Stage E: Final Answer & Key Values
- **Question 1:** `/usr/share/wordlists/rockyou.txt`
- **Question 2:** `rule-based`
- **Question 3:** `mask`

---

## Stage F: Practical Verification & Proof of Work
Verify the presence and line count of the RockYou wordlist on the assessment system:

```bash
wc -l /usr/share/wordlists/rockyou.txt
```
*Expected Output:*
```text
14344392 /usr/share/wordlists/rockyou.txt
```

Verify available rule files in Hashcat:
```bash
ls -l /usr/share/hashcat/rules/ | head -n 5
```

---

## Stage G: Key Educational Takeaway
Selecting the appropriate attack strategy converts mathematically intractable cracking problems into trivial compute operations. By scoping attacks to match organizational policies (via masks) or human psychology (via rule-based transformations on standard dictionaries), testers maximize cracking yields while minimizing hardware runtime.
