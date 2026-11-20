# Module 16: Topic Transition Recap — Assessment Questions & Solutions

## Recap Metadata
- **Module:** Module 16 — Python Scripting Basics
- **Recap Title:** Topic Transition Recap
- **Path:** Jr Penetration Tester
- **Recap Canonical URL:** `https://tryhackme.com/recap/6afe01d5f306fc3f659280c2`
- **Recap ID:** `6afe01d5f306fc3f659280c2`
- **Total Points:** 48 / 48 (8 points per question, 100% first-attempt score)
- **Status:** Completed & Audited (100% Live Account Parity)

---

## Question 1: Stages of an Offensive Engagement (Re-Order Sequence)
- **Question ID:** `6afe01d5f306fc3f659280a7`
- **Question Type:** Re-Order Sequence
- **Points:** 8 / 8
- **Prompt:**
  > *Order the stages of the engagement so each step consumes what the previous one produced.*

### Technical & Methodological Analysis:
An offensive penetration test follows a strictly sequential, data-driven attack chain where each phase ingests the intelligence produced by the preceding phase:
1. **Stage 1: Scan TCP ports to find the web service and SSH listener (`6afe01d5f306fc3f659280a5`)**
   - *Rationale:* Port scanning and service discovery identify network attack surfaces. You cannot enumerate a web application without knowing which port hosts the HTTP listener.
2. **Stage 2: Enumerate directories on the discovered web port for pages (`6afe01d5f306fc3f659280a3`)**
   - *Rationale:* With the web port confirmed (e.g., port 8000), directory fuzzing discovers hidden endpoints (`apollo.html`, `private.html`), exposing application comments, usernames (`tester`), and password hashes.
3. **Stage 3: Crack the discovered hash against the wordlist (`6afe01d5f306fc3f659280a6`)**
   - *Rationale:* Cracking requires the cryptographic hash harvested from the enumerated web page and produces the cleartext credential (`rainbow`).
4. **Stage 4: Authenticate over SSH with the recovered credentials (`6afe01d5f306fc3f659280a4`)**
   - *Rationale:* Establishing an SSH session requires both the username and the cracked password, achieving remote host compromise and flag acquisition.

- **Verified Submitted Sequence:**
  1. `Scan TCP ports to find the web service and SSH listener`
  2. `Enumerate directories on the discovered web port for pages`
  3. `Crack the discovered hash against the wordlist`
  4. `Authenticate over SSH with the recovered credentials`

---

## Question 2: Python Exception Handling (Match Pairs)
- **Question ID:** `6afe01d5f306fc3f659280b0`
- **Question Type:** Match Pairs
- **Points:** 8 / 8
- **Prompt:**
  > *Match each failing Python operation to the exception it raises.*

### Technical & Exception Hierarchy Analysis:
Python implements a structured exception hierarchy deriving from `BaseException`. Matching runtime failures to specific subclasses:
1. **`open("missing.txt")` ⟷ `FileNotFoundError`**
   - *Mechanism:* The operating system syscall `open()` returns error code `ENOENT` (Error Number 2). Python catches this and raises `FileNotFoundError` (subclass of `OSError`).
2. **`int("abc")` ⟷ `ValueError`**
   - *Mechanism:* The `int()` constructor receives a valid string type (`str`), but its textual content cannot be parsed as a base-10 numerical literal. Because the type is correct but the value is invalid, `ValueError` is raised.
3. **`mylist[99] on a three-item list` ⟷ `IndexError`**
   - *Mechanism:* Accessing an array offset beyond the bounds of the sequence ($[0, 2]$) raises `IndexError: list index out of range`.
4. **`10 / 0` ⟷ `ZeroDivisionError`**
   - *Mechanism:* Division or modulo operations with a divisor of zero trigger a CPU arithmetic fault, caught by CPython as `ZeroDivisionError`.

- **Verified Submitted Pairs:**
  - `open("missing.txt")` $\longleftrightarrow$ `FileNotFoundError`
  - `int("abc")` $\longleftrightarrow$ `ValueError`
  - `mylist[99] on a three-item list` $\longleftrightarrow$ `IndexError`
  - `10 / 0` $\longleftrightarrow$ `ZeroDivisionError`

---

## Question 3: Script Execution Workflow (Re-Order Sequence)
- **Question ID:** `6afe01d5f306fc3f659280b5`
- **Question Type:** Re-Order Sequence
- **Points:** 8 / 8
- **Prompt:**
  > *Order the steps of a script that flags usernames from a text file against a list of weak passwords.*

### Technical & Pipeline Architecture Analysis:
Building a scalable data processing pipeline requires strict sequential staging:
1. **Step 1: Read the usernames file contents into a string (`6afe01d5f306fc3f659280b2`)**
   - *Rationale:* Before any processing or tokenization can occur, the raw byte stream must be loaded from disk storage into memory.
2. **Step 2: Split the file contents into a list of usernames (`6afe01d5f306fc3f659280b3`)**
   - *Rationale:* The single contiguous string block must be parsed and split by newline delimiters (`\n`) into an iterable collection of discrete usernames.
3. **Step 3: Loop over the username list, testing membership in the weak list (`6afe01d5f306fc3f659280b4`)**
   - *Rationale:* The script iterates through the username list, evaluating whether each candidate exists in the dictionary of known weak credentials.
4. **Step 4: Write each flagged username to the report file (`6afe01d5f306fc3f659280b1`)**
   - *Rationale:* Only usernames identified as compromised during the iteration step are persisted to the output audit report.

- **Verified Submitted Sequence:**
  1. `Read the usernames file contents into a string`
  2. `Split the file contents into a list of usernames`
  3. `Loop over the username list, testing membership in the weak list`
  4. `Write each flagged username to the report file`

---

## Question 4: Python Function Variable Scope (Boolean)
- **Question ID:** `6afe01d5f306fc3f659280b8`
- **Question Type:** Boolean True / False
- **Points:** 8 / 8
- **Prompt:**
  > *A variable created inside a function can be read from outside that function after it returns.*

### Technical & Scope Analysis:
- Under CPython’s LEGB resolution rules, variables assigned within a function block reside strictly inside that function’s local stack frame (`PyFrameObject`).
- Once the function returns, its execution frame is deallocated and its local variables are destroyed (unless explicitly returned or referenced in an outer closure).
- Attempting to access a local variable from outside the function raises a `NameError: name 'variable' is not defined`.
- This scope isolation prevents functions from contaminating each other’s operational state.

- **Verified Selected Answer:** **`False`**

---

## Question 5: Pentesting Legal Boundaries (Boolean)
- **Question ID:** `6afe01d5f306fc3f659280bb`
- **Question Type:** Boolean True / False
- **Points:** 8 / 8
- **Prompt:**
  > *Running a port scan or credential brute force against a network you do not own is legal as long as no data is stolen.*

### Legal & Regulatory Analysis:
- In international cybersecurity jurisprudence (e.g., Computer Fraud and Abuse Act [CFAA] in the US, Computer Misuse Act [CMA 1990] in the UK), **unauthorized access or unauthorized scanning is criminalized regardless of whether damage occurs or data is exfiltrated**.
- Port scanning, vulnerability probing, and credential brute-forcing constitute unauthorized access attempts.
- Legality is derived exclusively from **explicit written authorization**, formally documented in a Statement of Work (SoW), Contract, and agreed Rules of Engagement (RoE).

- **Verified Selected Answer:** **`False`**

---

## Question 6: File Handling Mode for Binary Content (Single Choice)
- **Question ID:** `6afe01d5f306fc3f659280c1`
- **Question Type:** Single Choice
- **Points:** 8 / 8
- **Prompt:**
  > *Which file mode should a Python downloader use when saving fetched content such as images or archives?*

### Binary Stream Analysis:
- Opening a file in standard text mode (`"w"`) applies operating system encoding translations (e.g., converting `\n` to `\r\n` on Windows), which mangles raw binary payloads, executable headers (ELF, PE), and archive magic bytes (ZIP, TAR).
- The binary write mode **`"wb"`** opens the file descriptor directly for raw byte stream ingestion without any encoding modification, ensuring byte-exact fidelity.

- **Available Options:**
  - `"r"`: Read-only text mode.
  - **`"wb"` (CORRECT):** Write binary mode.
  - `"w"`: Write text mode (corrupts binaries).
  - `"rb"`: Read-only binary mode.
  - `"a"`: Append text mode.
- **Verified Selected Answer:** **`"wb"`**
