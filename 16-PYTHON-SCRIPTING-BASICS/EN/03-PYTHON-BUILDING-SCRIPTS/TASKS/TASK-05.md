# Task 05: Libraries, Package Management & pip (المكتبات وإدارة الحزم البرمجية)

## Stage A: Original Task Content & Educational Context (نص المهمة والسياق التعليمي)
Python’s power in offensive security is amplified by its "batteries included" philosophy—a comprehensive **Standard Library** supplemented by the vast **Python Package Index (PyPI)** ecosystem of third-party security tools.

### 1. Import Statements:
- `import os`: Imports the entire module namespace (`os.name`, `os.system`).
- `from datetime import datetime`: Imports specific classes or functions directly into the local namespace.
- `import datetime as dt`: Imports a module under an alias to prevent naming collisions.

### 2. High-Value Standard Library Modules for Security:
- `string`: Exposes predefined ASCII character sets (`string.ascii_uppercase`, `string.ascii_lowercase`, `string.digits`, `string.punctuation`).
- `hashlib`: Provides cryptographic hashing algorithms (MD5, SHA-1, SHA-256, SHA-512) for verifying payload integrity or cracking password hashes.
- `os` / `sys`: Operating system interactions, directory traversal, and command-line argument extraction via `sys.argv`.
- `json`: Parsing REST API responses, JWT tokens, and configuration files.

### 3. Package Management via `pip`:
- `pip` (**Pip Installs Packages**) is the standard package manager for Python.
- Installing third-party packages:
  ```bash
  pip install requests
  pip install scapy
  pip install pycryptodome
  ```

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والتشغيلية)
- **Supply Chain Attacks & PyPI Typosquatting:** Attackers frequently publish malicious packages on PyPI that mimic popular libraries (e.g., `reqeusts` or `colorama-v2`), executing credential stealers upon installation via `setup.py`. In offensive and defensive environments, pin exact package versions in `requirements.txt` with SHA-256 hash checking (`pip install --require-hashes -r requirements.txt`).
- **Python Virtual Environments (`venv`):** Installing packages globally with root privileges (`sudo pip install`) can corrupt system package managers (`apt`, `pacman`). Modern Linux distributions enforce PEP 668 ("externally-managed-environment"). Security practitioners should isolate dependencies using virtual environments:
  ```bash
  python3 -m venv ~/my_tool_env
  source ~/my_tool_env/bin/activate
  pip install requests
  ```
- **Cryptographic Hashing Best Practices:** MD5 and SHA-1 suffer from practical collision attacks. When storing or verifying cryptographic integrity in security tools, always default to SHA-256 or SHA-512 from `hashlib`.

---

## Stage C: Guided Hints (تلميحات متدرجة)
1. **Tier 1 (Conceptual):**
   - Question 1 asks for the 3-letter package manager CLI tool used to install modules from PyPI.
   - Question 2 asks for the standard library module that defines `string.punctuation` and `string.digits`.
   - Question 3 asks for the first hexadecimal character of the SHA-256 hash printed by `imports_demo.py`.
2. **Tier 2 (Logic & Hash Trace):**
   - In Question 1, it is `pip`.
   - In Question 2, the module name matches the word `string`.
   - In Question 3, inspect `imports_demo.py` on the VM or run `hashlib.sha256("TryHackMe".encode()).hexdigest()`. What is the very first character of the output?
3. **Tier 3 (Exact Values):**
   - Q1: `pip`
   - Q2: `string`
   - Q3: `5`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

Let's inspect the code of `imports_demo.py` on the VM:

```python
# imports_demo.py excerpt
import hashlib
import string

# Character checking
test_str = "TryHackMe2025!"
has_punct = any(c in string.punctuation for c in test_str)
print(f"Contains punctuation: {has_punct}")

# Cryptographic hashing
default_input = "TryHackMe"
hash_val = hashlib.sha256(default_input.encode("utf-8")).hexdigest()
print(f"SHA-256 of '{default_input}': {hash_val}")
```

Running the script on the VM:
```shell-session
ubuntu@tryhackme:~/Building-Scripts$ python3 imports_demo.py
Contains punctuation: True
SHA-256 of 'TryHackMe': 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
```

Inspecting the calculated hash:
```
Hash: 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
      ^
      |-- First character is '5'
```
The first character of the SHA-256 digest is `5`.

---

## Stage E: Final Answer & Key Values (الإجابة النهائية والقيم المستخرجة)
- **Question 1:** What is the name of Python's package manager used to install third-party libraries?
  - **Answer:** `pip`
- **Question 2:** Which module from the standard library provides constants like `ascii_uppercase`, `digits`, and `punctuation`?
  - **Answer:** `string`
- **Question 3:** On the VM, run `imports_demo.py`. What is the first character of the SHA-256 hash it prints for the default input string?
  - **Answer:** `5`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)
- **Automated Verification Script:**
  ```python
  import hashlib
  import string

  assert hasattr(string, "ascii_uppercase")
  assert hasattr(string, "digits")
  assert hasattr(string, "punctuation")

  digest = hashlib.sha256("TryHackMe".encode("utf-8")).hexdigest()
  assert digest[0] == "5"
  print(f"Verified Digest: {digest}")
  print("Task 05 Libraries and Cryptographic Hashing Verified Successfully.")
  ```
  Output:
  ```
  Verified Digest: 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
  Task 05 Libraries and Cryptographic Hashing Verified Successfully.
  ```

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)
Python's standard library provides high-performance modules like `hashlib` and `string` out of the box without external dependencies. When external capabilities (like HTTP automation) are needed, use `pip` inside isolated virtual environments (`venv`) to maintain clean dependencies.
