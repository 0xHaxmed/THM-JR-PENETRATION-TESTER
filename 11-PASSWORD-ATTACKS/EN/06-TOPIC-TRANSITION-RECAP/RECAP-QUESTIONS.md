# Topic Transition Recap: Password Attacks (Module 11)

## Assessment Overview & Performance Metrics
- **Assessment Type:** Topic Transition Assessment
- **Canonical URL:** https://tryhackme.com/recap/6afe01d5f306fc3f65928020
- **Module:** Module 11 — Password Attacks
- **Total Questions:** 6 Questions
- **Total Points Earned:** 48 / 48 (100% Score)
- **Status:** Verified & Complete

---

## Detailed Question-by-Question Analysis

### Question 1: Online Brute-Force Noise & Defensive Visibility
- **Question ID:** `6afe01d5f306fc3f65928001`
- **Format:** Boolean (True / False)
- **Question Prompt:**  
  *"A brute-force login attack against a network service generates many failed authentication log entries."*
- **Options Evaluated:**
  - `True` *(Selected / Correct)*
  - `False`
- **Correct Answer:** **`True`**
- **Technical Deep Dive & First Principles:**
  Unlike offline hash cracking (which occurs purely in memory on attacker-controlled hardware), an online brute-force or password spraying attack (using tools like Hydra or Medusa) submits every individual credential guess across the network to a live daemon (SSH, FTP, HTTP, RDP). Each invalid candidate results in an authentication failure event (`pam_unix`, Windows Security Event ID 4625: "An account failed to log on", or web server 401/403 logs). Security Information and Event Management (SIEM) systems and Intrusion Detection Systems (IDS) trigger alerts when failure thresholds exceed baseline levels.

---

### Question 2: Web Form Reconnaissance & HTTP Method Identification
- **Question ID:** `6afe01d5f306fc3f65928007`
- **Format:** Single Choice (Multiple Choice)
- **Question Prompt:**  
  *"Before brute-forcing a web login page, what must you determine about the login request?"*
- **Options Evaluated:**
  - A. `The TLS cipher suite negotiated by the browser` — *Incorrect: Cipher parameters do not dictate credential submission structure.*
  - B. `The database engine behind the application` — *Incorrect: The backend database is abstracted behind the web tier; the HTTP request structure is what matters.*
  - C. `The server's operating system kernel version` — *Incorrect: Kernel version is irrelevant to constructing a form brute-force command.*
  - D. `The number of CPU cores on the web server` — *Incorrect: Hardware specifications do not alter HTTP form parameters.*
  - E. `Whether it is submitted as a GET or a POST request` *(Selected / Correct)*
- **Correct Answer:** **`Whether it is submitted as a GET or a POST request`**
- **Technical Deep Dive & First Principles:**
  In web application password attacks, the HTTP method determines how credentials are transported over HTTP. In a `GET` request, parameters are appended directly to the URL query string (`/login?user=admin&pass=123456`), requiring tools like Hydra to use the `http-get-form` module. In a `POST` request, parameters travel inside the HTTP message body as `application/x-www-form-urlencoded` or `application/json`, requiring Hydra's `http-post-form` module. Choosing the wrong method results in total attack failure.

---

### Question 3: Ffuf Extension Flag Mechanics (`-e`)
- **Question ID:** `6afe01d5f306fc3f6592800d`
- **Format:** Single Choice (Multiple Choice)
- **Question Prompt:**  
  *"In ffuf, what does `-e .php,.html,/` add to a directory discovery scan?"*
- **Options Evaluated:**
  - A. `Only files with those extensions are reported as hits` — *Incorrect: `-e` expands requests; matchers (`-mc`) govern filtering.*
  - B. `It rewrites the wordlist file on disk with the suffixes` — *Incorrect: Ffuf never modifies wordlist files on disk; expansions occur in memory.*
  - C. `It restricts the scan to three concurrent requests` — *Incorrect: Concurrency is controlled via `-t`.*
  - D. `Responses containing those file types are excluded` — *Incorrect: Exclusion is handled via `-fr` or `-fc`.*
  - E. `Each word is also tried with those suffixes appended` *(Selected / Correct)*
- **Correct Answer:** **`Each word is also tried with those suffixes appended`**
- **Technical Deep Dive & First Principles:**
  The `-e` parameter in Ffuf provides on-the-fly candidate expansion. When a word like `admin` is processed with `-e .php,.html,/`, Ffuf sequentially dispatches requests for:
  1. `admin` (base word)
  2. `admin.php`
  3. `admin.html`
  4. `admin/` (directory check)  
  This multi-extension fuzzing is critical for identifying non-linked server-side endpoints, hidden API directories, and legacy scripts without manually pre-processing wordlists.

---

### Question 4: Targeted Custom Wordlists vs. Generic Dictionaries
- **Question ID:** `6afe01d5f306fc3f65928010`
- **Format:** Boolean (True / False)
- **Question Prompt:**  
  *"A wordlist built from words specific to the target often succeeds where a large generic list fails."*
- **Options Evaluated:**
  - `False` — *Incorrect: Generic lists lack organizational specificity.*
  - `True` *(Selected / Correct)*
- **Correct Answer:** **`True`**
- **Technical Deep Dive & First Principles:**
  Mass generic dictionaries (like `rockyou.txt` with 14M entries) capture broad internet password habits, but frequently fail against enterprise infrastructure where passwords reflect internal product codenames, departmental abbreviations, local street names, or regional employee jargon. Generating target-tailored wordlists using tools like CeWL (content scraping) and CUPP (profiling) drastically reduces the search space while dramatically increasing the probability of uncovering active credentials.

---

### Question 5: Phishing Tool Ecosystem & Attack Outcomes
- **Question ID:** `6afe01d5f306fc3f65928019`
- **Format:** Matching Pairs
- **Question Prompt:**  
  *"Match each phishing capability to the outcome it provides for the attacker."*
- **Matched Pairs Matrix:**

| Phishing Capability / Tool | Specific Outcome Provided |
| :--- | :--- |
| **`SET credential harvester`** | **`Logging submitted form fields on a fake page`** |
| **`Evilginx`** | **`Live capture of session tokens past MFA`** |
| **`GoPhish`** | **`Campaign scheduling with open and click analytics`** |
| **`URL shortener`** | **`Concealing a link's true destination`** |

- **Technical Deep Dive & First Principles:**
  - **SET Credential Harvester:** Clones legitimate landing pages (e.g., Office 365 or Google Workspace) and executes a lightweight local web server that logs raw POST request parameters to disk.
  - **Evilginx:** Operates as a reverse proxy Adversary-in-the-Middle (AiTM). It relays traffic in real-time between the victim and the legitimate service. When the user completes MFA (TOTP, push notification), Evilginx captures the resulting session cookie/bearer token, completely bypassing MFA protections.
  - **GoPhish:** Open-source enterprise campaign framework providing scheduling, tracking pixels, click telemetry, and quantitative risk metrics (compromise rates).
  - **URL Shorteners (Bitly, TinyURL):** Mask malicious infrastructure hostnames and obfuscate redirect chains to bypass visual inspection and basic email gateway heuristics.

---

### Question 6: Cryptographic Salting & Rainbow Table Neutralization
- **Question ID:** `6afe01d5f306fc3f6592801f`
- **Format:** Single Choice (Multiple Choice)
- **Question Prompt:**  
  *"What is the main security benefit of adding a unique random salt to each password before hashing?"*
- **Options Evaluated:**
  - A. `The hashing algorithm becomes slower to compute` — *Incorrect: Slowness is achieved via cost/iteration factors (bcrypt/Argon2), not salts.*
  - B. `The password is encrypted rather than hashed` — *Incorrect: Salting does not convert hashing into reversible encryption.*
  - C. `Pre-computed rainbow tables become useless` *(Selected / Correct)*
  - D. `The hash can no longer be reversed` — *Incorrect: Hashes are already mathematically one-way.*
  - E. `Users can no longer choose weak passwords` — *Incorrect: Salting has zero impact on user password choice.*
- **Correct Answer:** **`Pre-computed rainbow tables become useless`**
- **Technical Deep Dive & First Principles:**
  A Rainbow Table is a pre-computed lookup table of plaintext-to-hash mappings designed to reverse unsalted hashes in seconds. When a unique random salt ($\ge 16$ bytes) is prepended or appended to the password before hashing, identical passwords produce completely different hash outputs across different users:
  $$\text{Hash}_1 = \text{MD5}(\text{"password"} \mathbin{\Vert} \text{Salt}_A)$$
  $$\text{Hash}_2 = \text{MD5}(\text{"password"} \mathbin{\Vert} \text{Salt}_B)$$
  Because the salt is unique per user, an attacker cannot reuse a precomputed table; they must compute a dedicated dictionary for every individual user record, completely neutralizing rainbow table efficiency.
