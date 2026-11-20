# Room 03: Python: Building Scripts — Additional Technical Notes & Deep Dive

## 1. Python Variable Scopes & The LEGB Rule
Understanding variable scope is essential when structuring modular scripts. CPython resolves variable names using the **LEGB Rule** (evaluating scopes from innermost to outermost):

```
+-------------------------------------------------------------+
|                     The LEGB Scope Hierarchy                |
+-------------------------------------------------------------+
|  [L] Local Scope: Inside the current function (def / lambda)|
|        |                                                    |
|        v                                                    |
|  [E] Enclosing Scope: Inside any enclosing outer functions  |
|        |                                                    |
|        v                                                    |
|  [G] Global Scope: Top-level module variables               |
|        |                                                    |
|        v                                                    |
|  [B] Built-in Scope: Preloaded names (print, len, open, int)|
+-------------------------------------------------------------+
```

### The `global` and `nonlocal` Keywords:
- If a function assigns to a variable name without declaring it `global`, Python treats it as a new local variable, shadowing any global variable of the same name.
- Attempting to read a global variable and modify it locally in the same scope raises an `UnboundLocalError`:
  ```python
  counter = 0
  def increment():
      # counter += 1  # Raises UnboundLocalError!
      global counter
      counter += 1    # Permitted, but modifying global state is an anti-pattern
  ```

---

## 2. In-Depth: Context Manager Protocol & File Descriptors
Why is `with open(...)` so critical in high-volume security tooling?

### Operating System File Descriptor Limits:
On Linux systems, every open file, network socket, and pipe is assigned an integer known as a **File Descriptor (FD)**. The operating system imposes a per-process limit (`ulimit -n`, typically 1,024 by default).
- If a multi-threaded port scanner or fuzzer opens sockets or files without closing them, the process quickly exhausts available FDs, causing all subsequent network calls to fail with:
  `OSError: [Errno 24] Too many open files`

### The Context Management Implementation:
```python
class SafeFileHandler:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.file:
            self.file.close()
        # Returning True would suppress exceptions; returning False bubbles them up
        return False
```

---

## 3. Cryptographic Hashing Mechanics (`hashlib`)
Task 5 demonstrates generating SHA-256 digests. Let's examine the cryptographic mechanics:

### SHA-256 (Secure Hash Algorithm 256-bit):
- **Input:** Arbitrary-length byte sequence (must be encoded to bytes: `"string".encode('utf-8')`).
- **Block Size:** 512 bits (64 bytes).
- **Output:** Fixed 256-bit digest (represented as 64 hexadecimal characters).
- **One-Way Pre-image Resistance:** Computationally infeasible to reverse $H(m) \rightarrow m$.
- **Avalanche Effect:** Altering a single bit in the input flips approximately 50% of the output bits.

```python
import hashlib

data = "TryHackMe".encode("utf-8")
hasher = hashlib.sha256()
hasher.update(data)
digest = hasher.hexdigest()

print(f"Algorithm: SHA-256")
print(f"Digest Length: {len(digest)} hex characters ({len(digest)*4} bits)")
print(f"Digest: {digest}")
```

---

## 4. Password Security: Shannon Entropy vs. Naïve Complexity
Task 6 implements a rule-based password checker. While rule-based checks (length, uppercase, digits, symbols) are common in corporate active directories, modern security standards (e.g., **NIST SP 800-63B**) favor **Entropy** and **Breach List Comparison**.

### Shannon Entropy Formula:
$$H = L \times \log_2(N)$$
Where:
- $H$ = Information entropy in bits.
- $L$ = Password length (number of characters).
- $N$ = Size of the character pool:
  - Digits only ($0-9$): $N = 10$ ($\log_2(10) \approx 3.32$ bits/char)
  - Lowercase only ($a-z$): $N = 26$ ($\log_2(26) \approx 4.70$ bits/char)
  - Alphanumeric ($a-z, A-Z, 0-9$): $N = 62$ ($\log_2(62) \approx 5.95$ bits/char)
  - Full printable ASCII ($+32$ symbols): $N = 94$ ($\log_2(94) \approx 6.55$ bits/char)

### Length vs. Complexity Comparison:
- Naïve complex password: `P@ss1` ($L=5, N=94$):
  $$H = 5 \times 6.55 = 32.75 \text{ bits of entropy}$$
- Passphrase: `correct-horse-battery-staple` ($L=28, N=27$):
  $$H = 28 \times 4.75 = 133.0 \text{ bits of entropy}$$
The longer passphrase requires $2^{100}$ times more computational work to brute-force than the short complex password!

---

## 5. Professional CLI Argument Parsing (`argparse`)
While simple scripts use `input()`, professional offensive tools accept command-line flags. The standard library module `argparse` provides automated help generation, flag parsing, and input validation:

```python
#!/usr/bin/env python3
import argparse
import sys

def parse_arguments():
    parser = argparse.ArgumentParser(
        description="Network Service Probe & Credential Auditor",
        epilog="Example: python3 probe.py -t 10.10.10.10 -p 80,443 -w wordlist.txt"
    )
    parser.add_argument("-t", "--target", required=True, help="Target IP or hostname")
    parser.add_argument("-p", "--ports", default="80", help="Comma-separated list of target ports (default: 80)")
    parser.add_argument("-w", "--wordlist", help="Path to dictionary file for credential testing")
    parser.add_argument("-v", "--verbose", action="store_true", help="Enable verbose debug output")
    
    return parser.parse_args()

if __name__ == "__main__":
    args = parse_arguments()
    print(f"[*] Attacking Target: {args.target}")
    print(f"[*] Target Ports: {args.ports}")
```
Running with `--help`:
```shell-session
$ python3 probe.py --help
usage: probe.py [-h] -t TARGET [-p PORTS] [-w WORDLIST] [-v]

Network Service Probe & Credential Auditor

options:
  -h, --help            show this help message and exit
  -t TARGET, --target TARGET
                        Target IP or hostname
  -p PORTS, --ports PORTS
                        Comma-separated list of target ports (default: 80)
  -w WORDLIST, --wordlist WORDLIST
                        Path to dictionary file for credential testing
  -v, --verbose         Enable verbose debug output
```
