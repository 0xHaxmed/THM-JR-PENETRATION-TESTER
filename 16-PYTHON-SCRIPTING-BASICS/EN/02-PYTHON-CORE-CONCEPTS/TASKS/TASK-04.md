# Task 04: Lists and Dictionaries (القوائم والقواميس وهياكل البيانات)

## Stage A: Original Task Content & Educational Context (نص المهمة والسياق التعليمي)
Security scripts depend on composite data structures to store collections of IP addresses, ports, credentials, and configuration maps. Python provides two primary built-in data structures for these tasks:

### 1. Lists (`list`):
- **Characteristics:** Ordered, zero-indexed, mutable sequences enclosed in square brackets `[]`.
- **Usage Example:** Storing a scan queue: `ports = [22, 80, 443, 8080]`.
- **Core Methods:**
  - `.append(x)`: Appends an item to the end of the list ($O(1)$ amortized).
  - `.remove(x)`: Removes the first occurrence of value `x`.
  - `.pop(i)`: Removes and returns the item at index `i` (default last item).
  - `.sort()`: Sorts elements in place ($O(n \log n)$ via Timsort).
  - `.reverse()`: Reverses list in place.

### 2. Dictionaries (`dict`):
- **Characteristics:** Unordered (insertion-ordered in Python 3.7+) key-value mappings enclosed in curly braces `{}`.
- **Usage Example:** Service lookup table: `services = {22: "SSH", 80: "HTTP", 443: "HTTPS"}`.
- **Key Lookup:** Access via bracket notation `services[80]` returns `"HTTP"`. If a key does not exist, it raises a fatal `KeyError`.
- **Safe Retrieval (`.get()`):** `services.get(key, default)` returns the value if present, or a fallback default value (or `None`) without throwing an exception.
- **Core Methods:** `.keys()`, `.values()`, `.items()`.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والتشغيلية)
- **Time Complexity & Algorithmic Security:**
  - Searching a list (`x in target_list`) has **$O(n)$ linear time complexity**. Checking a wordlist of 100,000 items in a list takes $O(n)$ comparisons per check.
  - Searching a dictionary or set (`x in target_dict`) has **$O(1)$ average constant time complexity** due to internal hash tables. Converting lookup wordlists to sets or dictionary keys yields dramatic performance speedups (from hours to seconds) during high-throughput fuzzing.
- **Defensive Error Handling:** In production exploit tools, accessing `dict[key]` directly is a critical anti-pattern. If a remote target returns an unexpected JSON response or an unmapped port (e.g., port 9999), direct indexing crashes the script with a `KeyError`. Using `.get(port, "Unknown Service")` ensures that the scanner logs the unrecognized service gracefully and continues execution.
- **Mutable Default Arguments:** Never use an empty list or dictionary as a default argument in functions (e.g., `def scan(ports=[])`). Because default arguments are evaluated once at module load time, the same list instance is shared across all function calls, creating state pollution and race conditions.

---

## Stage C: Guided Hints (تلميحات متدرجة)
1. **Tier 1 (Conceptual):**
   - Question 1 asks for the list method that appends an element to the tail of the list.
   - Question 2 tests dictionary key retrieval syntax: what value maps to key `80`?
   - Question 3 asks for the method that prevents `KeyError` by providing a fallback default value.
2. **Tier 2 (Syntax Check):**
   - For Question 1, include the leading dot and parentheses: `.append()`.
   - For Question 2, look at `{22: "SSH", 80: "HTTP"}`. The key `80` maps to the string value.
   - For Question 3, include the leading dot and parentheses: `.get()`.
3. **Tier 3 (Exact Values):**
   - Q1: `.append()`
   - Q2: `HTTP`
   - Q3: `.get()`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### Python Hash Tables & Dictionary Architecture:
CPython implements dictionaries as compact hash tables. When calling `services[80]`:
1. Python computes the hash of the key: `hash(80)`.
2. It masks the hash to calculate an index into an entry array.
3. If an entry exists with matching key and hash, the associated value pointer is returned (`"HTTP"`).
4. If no entry matches:
   - Direct bracket access (`services[8080]`) invokes `dict.__getitem__()` and immediately raises `KeyError: 8080`.
   - Safe access via `services.get(8080, "Unknown")` intercepts the lookup failure and returns `"Unknown"`.

```python
services = {22: "SSH", 80: "HTTP"}

# Direct lookup
val = services[80]
print(val)  # Prints: HTTP

# Safe lookup with fallback
unknown_val = services.get(8080, "Unmapped Service")
print(unknown_val)  # Prints: Unmapped Service
```

### List Resizing & Amortized $O(1)$ Appending:
In CPython, a `list` is an array of pointers (`PyObject**`). When calling `.append()`, if the pre-allocated memory slots are filled, CPython allocates a larger array (growth pattern: $0, 4, 8, 16, 24, 32, 40, 52, 64, \dots$) and copies existing pointers. Because resizing occurs geometrically, the time complexity of `.append()` is amortized $O(1)$.

---

## Stage E: Final Answer & Key Values (الإجابة النهائية والقيم المستخرجة)
- **Question 1:** What method adds an element to the end of a list?
  - **Answer:** `.append()`
- **Question 2:** Given `services = {22: "SSH", 80: "HTTP"}`, what does `services[80]` return?
  - **Answer:** `HTTP`
- **Question 3:** What dictionary method lets you retrieve a value with a safe fallback if the key does not exist?
  - **Answer:** `.get()`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)
- **Automated Python Verification Script:**
  ```python
  # List operations
  ports = [22, 80]
  ports.append(443)
  assert ports == [22, 80, 443]

  # Dictionary operations
  services = {22: "SSH", 80: "HTTP"}
  assert services[80] == "HTTP"
  assert services.get(9999, "Unknown") == "Unknown"
  assert services.get(80, "Unknown") == "HTTP"
  print("List and Dictionary operations verified successfully.")
  ```
  Output:
  ```
  List and Dictionary operations verified successfully.
  ```

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)
Use lists for ordered sequences and work queues. Use dictionaries for associative lookups and configurations. Always use `.get()` when querying remote or user-supplied keys to prevent abrupt application crashes caused by unhandled `KeyError` exceptions.
