# Additional Technical Notes: OWASP Top 10 2025 - Insecure Data Handling (ملاحظات تقنية متقدمة)

---

## 1. Cryptographic Entropy & Key Derivation Weaknesses

### 1.1 Entropy Calculations & Feasible Brute-Force
Entropy measures the unpredictability of a key:
$$H(K) = \log_2(|\mathcal{K}|)$$
- **Cryptographic Standard:** A 128-bit key possesses $H = 128$ bits of entropy ($2^{128} \approx 3.4 \times 10^{38}$ states), requiring billions of years across supercomputers to crack.
- **Vulnerable Key Scheme:** A 4-character key where 3 characters are known constants and the last character is a decimal digit:
  $$|\mathcal{K}| = 10^1 = 10 \implies H = \log_2(10) \approx 3.32 \text{ bits}$$
  An attacker can exhaust the entire keyspace in under 10 HTTP requests.

### 1.2 Enterprise KDF Implementation
When deriving encryption keys from user-supplied passwords:
```python
import hashlib
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
from cryptography.hazmat.primitives import hashes

# Secure PBKDF2-HMAC-SHA256 Implementation
salt = os.urandom(16)
kdf = PBKDF2HMAC(
    algorithm=hashes.SHA256(),
    length=32,               # 256-bit output key
    salt=salt,
    iterations=600000,       # OWASP recommended minimum iterations
)
derived_key = kdf.derive(password.encode())
```

---

## 2. Server-Side Template Injection (SSTI) Taxonomy

### 2.1 Engine Identification Matrix
Attackers determine the backend templating engine using a polyglot test sequence:

```
                      ${{7*7}}
                     /        \
                    /          \
                Evaluates    Does not evaluate
                 to 49:            to 49:
                   |                 |
                {{7*7}}            <%= 7*7 %>
               /       \             |
              /         \         ERB (Ruby)
          Evaluates   Evaluates
           to 49:      to 7777777:
             |            |
          Jinja2 /      Jinja2 /
          Twig /       Jinja (Python)
          Smarty       {{7*'7'}}
```

### 2.2 Jinja2 Object Exploration Primitives
In Python's Jinja2, expressions execute within a sandboxed runtime. However, sandbox escapes leverage object introspection:
- `().__class__.__base__` $\longrightarrow$ `<class 'object'>`
- `object.__subclasses__()` $\longrightarrow$ Returns all loaded classes in memory.
- Searching for `subprocess.Popen`, `_io.FileIO`, or `warnings.catch_warnings`.
- Alternatively, utilizing standard Jinja2 context helpers that expose `__globals__`:
  ```jinja2
  {{ url_for.__globals__.__builtins__.__import__('os').popen('id').read() }}
  {{ lipsum.__globals__.__builtins__.__import__('os').popen('id').read() }}
  {{ cycler.__init__.__globals__.os.popen('id').read() }}
  ```

---

## 3. Insecure Deserialization & The Python Pickle Virtual Machine

### 3.1 Pickle Opcodes & Execution Mechanics
Python's `pickle` is not a static data parser; it is a **stack-based virtual machine**:
- Opcode `c`: Push a global object (module, class, or function) onto the stack.
- Opcode `(`: Push a markobject onto the stack.
- Opcode `t`: Build a tuple from elements on the stack up to the markobject.
- Opcode `R`: Pop a tuple of arguments and a callable from the stack, call the callable, and push the result back onto the stack.
- Opcode `.`: Stop execution and return the top-of-stack object.

### 3.2 Hand-Crafted Pickle Assembly
An attacker can write raw pickle bytecode without using Python classes:
```text
csubprocess
check_output
(S'cat flag.txt'
tR.
```
When `pickle.loads()` encounters this stream, it loads `subprocess.check_output`, builds the argument tuple `('cat flag.txt',)`, and invokes it immediately.

### 3.3 Safe Serialization Alternatives
| Format | Human Readable | Code Execution Risk | Primary Use Case |
| :--- | :---: | :---: | :--- |
| **Pickle** | No (Binary) | **EXTREME (Inherently Insecure)** | Internal process memory sharing only |
| **JSON** | Yes (Text) | **Zero (Data Only)** | Public REST APIs, configurations |
| **Protocol Buffers** | No (Binary) | **Zero (Schema Enforced)** | High-performance microservices / gRPC |
| **MessagePack** | No (Binary) | **Zero (Data Only)** | Compact cross-language data exchange |

---

## 4. Input Validation & Parameterized Safety Architecture

### 4.1 SQL Injection Prevention: Prepared Statements
```python
# VULNERABLE: Direct concatenation
cursor.execute(f"SELECT * FROM users WHERE username = '{username}'")

# SECURE: Parameterized Query
cursor.execute("SELECT * FROM users WHERE username = %s", (username,))
```

### 4.2 Template Rendering Safety
```python
# VULNERABLE: Dynamic template evaluation
return render_template_string(f"Welcome {user_name}!")

# SECURE: Pre-compiled static template with context binding
return render_template("welcome.html", name=user_name)
```
In `welcome.html`:
```html
<h1>Welcome {{ name }}!</h1>
<!-- Jinja2 automatically escapes special characters by default in static HTML templates -->
```
