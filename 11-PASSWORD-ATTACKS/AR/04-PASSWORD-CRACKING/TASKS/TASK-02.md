# Task 02: How Passwords Are Stored

## Stage A: Original Task Content & Operational Objectives
This task explores the fundamental cryptographic principles governing secure credential storage, explaining why storing plaintext passwords is catastrophic and analyzing the mechanics of cryptographic hash functions, salts, and purpose-built password hashing algorithms.

### Key Concepts:
1. **The Fallacy of Plaintext Storage:** Plaintext storage allows any database breach, backup leakage, or rogue insider to immediately compromise user credentials across all systems where passwords are reused.
2. **Properties of Cryptographic Hash Functions:**
   - **One-Way Functionality (Pre-image Resistance):** Given a hash $H = \text{hash}(P)$, it is computationally infeasible to compute the inverse function $\text{hash}^{-1}(H)$ to recover $P$.
   - **Determinism:** The same input string $P$ will always yield the exact same hash output $H$ under the same hashing algorithm.
   - **Fixed Output Length:** Regardless of input length (1 character or 10 gigabytes), the hash output has a fixed bit/byte length (MD5 produces 128 bits; SHA-256 produces 256 bits).
   - **Collision Resistance:** It is computationally infeasible to find two distinct inputs $P_1 \neq P_2$ such that $\text{hash}(P_1) = \text{hash}(P_2)$.
   - **Avalanche Effect:** Altering a single bit in the input radically alters the entire resulting hash value.
3. **The Speed Dilemma:** General-purpose cryptographic hash algorithms (MD5, SHA-1, SHA-256) were engineered for data integrity verification and message authentication. They are designed to compute as quickly as possible. Modern consumer GPUs can calculate over 50 billion MD5 hashes per second, making unsalted or fast hashes trivial to crack.
4. **Salting Mechanisms:** Adding a unique, cryptographically random string (the salt) to the password before hashing:
   $$\text{Stored Record} = \text{Salt} \mathbin{\Vert} \text{Hash}(\text{Password} \mathbin{\Vert} \text{Salt})$$
   Salting ensures that two users with identical passwords have completely different hashes, rendering precomputed lookup tables (Rainbow Tables) useless.
5. **Slow/Adaptive Password Hashing Algorithms:** Algorithms like **bcrypt**, **Argon2**, and **PBKDF2** incorporate configurable computational work factors (cost) and memory requirements, forcing cracking hardware to spend measurable CPU time and memory per candidate guess.

---

## Stage B: Independent Thinking & Operational Security (OpSec)
Understanding storage architectures enables testers to identify systemic weaknesses during vulnerability assessments:

```
[ Plaintext Password: "Password123" ]
                |
                +---> [ Fast Hash (MD5/SHA1) ] -----------> 50,000,000,000 checks/sec (Trivial Crack)
                |
                +---> [ Fast Hash + Salt ] ---------------> Rainbow Tables defeated; Fast GPU still viable
                |
                +---> [ Slow Hash (bcrypt cost 12) ] -----> ~1,000 checks/sec (Computationally Infeasible)
```

### Analytical Insights:
- When encountering database dumps during an engagement, immediately inspect the prefix structure. An unsalted SHA-1 or MD5 column indicates critical cryptographic hygiene failure (OWASP A02:2021 / A04:2025 Cryptographic Failures).
- In contrast, seeing `$2a$12$` or `$argon2id$` indicates modern defense-in-depth where mass offline cracking is severely throttled.

---

## Stage C: Guided Hints & Progressive Clues
### Question 1: What property of a hash function means you cannot reverse a hash to recover the original input?
- **Tier 1 (Conceptual):** Cryptographic hashes are mathematically designed to be easy to compute in the forward direction, but practically impossible to reverse.
- **Tier 2 (Terminology):** Think of street traffic moving in only a single legal direction.
- **Tier 3 (Direct Guidance):** The term is hyphenated and consists of two words describing a single directional vector: `one-way`.

### Question 2: What term describes the unique random string added to a password before hashing to prevent rainbow table attacks?
- **Tier 1 (Conceptual):** Precomputed tables rely on looking up identical plaintext-to-hash mappings across millions of records.
- **Tier 2 (Culinary Analogy):** What culinary seasoning is sprinkled onto food to give it unique flavor?
- **Tier 3 (Direct Guidance):** A four-letter English noun: `salt`.

### Question 3: Which hashing algorithm was designed specifically for passwords and is deliberately slow by design?
- **Tier 1 (Conceptual):** Developed in 1999 by Niels Provos and David Mazières based on the Blowfish cipher, it uses an internal cost factor.
- **Tier 2 (Prefix Signature):** Hashes using this algorithm start with prefixes like `$2a$`, `$2b$`, or `$2y$`.
- **Tier 3 (Direct Guidance):** The algorithm name begins with the letter 'b' and ends with 'crypt': `bcrypt`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### 1. Mathematical and Algorithmic Comparison
Let us analyze why general cryptographic hashes fail to protect passwords compared to password-specific key derivation functions:

| Characteristic | MD5 / SHA-256 | bcrypt | Argon2id |
| :--- | :--- | :--- | :--- |
| **Primary Design Goal** | Rapid data integrity | Slow password hashing | Memory-hard password hashing |
| **GPU Optimization** | Extreme (bit shifts, simple ALU ops) | Moderate (Blowfish cipher core) | Minimal (memory bandwidth bottleneck) |
| **Tunable Cost Factor** | None (fixed rounds) | Yes ($2^{\text{cost}}$ iterations) | Yes (Iterations, Memory, Parallelism) |
| **Sample Hash Format** | `5f4dcc3b5aa765d61d8327deb882cf99` | `$2y$12$e87w...` | `$argon2id$v=19$m=65536,t=3,p=4$...` |

### 2. Practical Demonstration of Hashing Mechanisms
We can observe these properties directly using standard command-line tools:

```bash
# Generating an MD5 hash of "password"
echo -n "password" | md5sum
# Result: 5f4dcc3b5aa765d61d8327deb882cf99

# Demonstrating Avalanche Effect (altering 1 character: 'password' -> 'passwore')
echo -n "passwore" | md5sum
# Result: cd88a4e8d3fe7ad2bf2ec2e8f1ecf3bc (Completely different 32 hex chars)

# Generating a SHA-256 hash
echo -n "password" | sha256sum
# Result: 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8 (64 hex characters)
```

### 3. Understanding Salting and Rainbow Table Invalidation
Without salt, `hash("hunter2")` yields the same hash on every system on the planet. Attackers build precomputed databases (rainbow tables) indexing hundreds of millions of common passwords against their hashes.
When a 16-byte random salt is generated:
$$\text{Salt}_1 = \text{a9f1b2c3d4e5f6a7} \implies \text{Hash} = \text{MD5}(\text{hunter2} \mathbin{\Vert} \text{a9f1b2c3d4e5f6a7})$$
$$\text{Salt}_2 = \text{b8e2c4d6f8a0b2c4} \implies \text{Hash} = \text{MD5}(\text{hunter2} \mathbin{\Vert} \text{b8e2c4d6f8a0b2c4})$$
An attacker can no longer use a precomputed rainbow table; they must compute a custom dictionary for every individual user's salt, multiplying the cracking workload by the number of users.

---

## Stage E: Final Answer & Key Values
- **Question 1:** `one-way`
- **Question 2:** `salt`
- **Question 3:** `bcrypt`

---

## Stage F: Practical Verification & Proof of Work
Verify bcrypt structure and cost factor via Python's standard `bcrypt` library or interactive shell:

```bash
python3 -c "import bcrypt; salt = bcrypt.gensalt(rounds=12); h = bcrypt.hashpw(b'hunter2', salt); print(h.decode())"
```
*Expected Output structure:*
```text
$2b$12$K1d0wQWp8rP... (Starts with algorithm identifier $2b$, cost factor 12, followed by 22-char salt and 31-char hash)
```

---

## Stage G: Key Educational Takeaway
A secure password architecture requires two mandatory controls: **unique cryptographic salting** to negate precomputed lookup attacks, and an **adaptive, slow key derivation function** (bcrypt, Argon2id, PBKDF2) to make offline brute-force and dictionary attacks economically and computationally prohibitive for attackers.
