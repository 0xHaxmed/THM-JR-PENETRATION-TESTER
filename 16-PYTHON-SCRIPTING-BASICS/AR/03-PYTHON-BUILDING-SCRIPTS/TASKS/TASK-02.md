# Task 02: Functions & Modular Design (الدوال والتصميم البرمجي المعياري)

## Stage A: Original Task Content & Educational Context (نص المهمة والسياق التعليمي)
Functions are named blocks of reusable code designed to perform a specific task. They encapsulate operational complexity, eliminate code duplication, and accept inputs to produce deterministic outputs.

### Function Mechanics in Python:
- **Definition:** Declared using the `def` keyword, followed by the function identifier, parameter list enclosed in parentheses, and a colon: `def function_name(param1, param2):`.
- **Parameters vs. Arguments:**
  - *Parameters:* The variable names listed in the function definition header (e.g., `target`, `port`).
  - *Arguments:* The actual values supplied when invoking the function (e.g., `"192.168.1.1"`, `443`).
- **Return Values (`return`):** The `return` keyword passes a computed value back to the caller and terminates function execution immediately. If a function reaches the end of its block without executing a `return` statement, it returns `None`.
- **Default Parameters:** Parameters can specify default fallback values (e.g., `def scan(target, port=80):`). If the caller omits the argument during invocation, Python assigns the default value (`80`).
- **Variable Scope (LEGB Rule):** Variables instantiated within a function body are local to that function's execution stack frame and cannot be accessed from the outer/global scope.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والتشغيلية)
- **CPython Stack Frame Overhead:** Every function call in CPython allocates a `PyFrameObject` on the call stack to manage local variables and bytecode execution pointers. For massive loops ($> 10^7$ iterations), inlining trivial expressions can yield performance gains, but for standard security scripting, the architectural modularity of functions far outweighs negligible function-call overhead.
- **The Mutable Default Argument Vulnerability:** A notorious Python gotcha is defining functions with mutable defaults (e.g., `def add_target(ip, target_list=[]):`). Because function definitions are executed once when the module is imported, `target_list` is created once in memory. Every subsequent invocation of `add_target()` that relies on the default argument mutates that *single shared list*, leading to state contamination across threads and runs.
  ```python
  # SECURE PATTERN:
  def add_target(ip, target_list=None):
      if target_list is None:
          target_list = []
      target_list.append(ip)
      return target_list
  ```
- **Side Effects & Idempotence:** In offensive tools, functions performing reconnaissance should avoid unwanted side effects (e.g., modifying global scan queues unexpectedly or leaving unclosed network connections).

---

## Stage C: Guided Hints (تلميحات متدرجة)
1. **Tier 1 (Conceptual):**
   - Question 1 asks for the 6-letter Python keyword that outputs data from a function back to the script.
   - Question 2 asks for the value assumed by `port` when `def scan(target, port=80):` is called without providing a port.
   - Question 3 asks for the numerical score computed by `functions_demo.py` for `"TryHackMe2025!"`.
2. **Tier 2 (Logic Trace):**
   - In Question 1, it is `return`.
   - In Question 2, look at the default assignment `port=80`. Since no argument was passed, it defaults to `80`.
   - In Question 3, trace `"TryHackMe2025!"` through `score_password()`:
     - Length is $15 \ge 8$ (+1)
     - Length is $15 \ge 12$ (+1)
     - Contains digits `'2'`, `'0'`, `'2'`, `'5'` (+1)
     - Contains uppercase `'T'`, `'H'`, `'M'` (+1)
     - Not in common password list (+1)
     - Total score = $1 + 1 + 1 + 1 + 1 = 5$.
3. **Tier 3 (Exact Values):**
   - Q1: `return`
   - Q2: `80`
   - Q3: `5`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

Let's inspect the password scoring logic in `functions_demo.py`:

```python
# functions_demo.py excerpt
def score_password(password, common_list):
    score = 0
    if len(password) >= 8:
        score += 1
    if len(password) >= 12:
        score += 1
    if any(c.isdigit() for c in password):
        score += 1
    if any(c.isupper() for c in password):
        score += 1
    if password not in common_list:
        score += 1
    return score

common = ["password", "123456", "admin", "welcome"]
test_pwd = "TryHackMe2025!"
result = score_password(test_pwd, common)
print(f"Password '{test_pwd}' received score: {result}")
```

Running the script on the VM:
```shell-session
ubuntu@tryhackme:~/Building-Scripts$ python3 functions_demo.py
Password 'TryHackMe2025!' received score: 5
```

### Deconstructing the Score for `"TryHackMe2025!"`:
1. `len("TryHackMe2025!")` = 14:
   - Check 1: $14 \ge 8 \rightarrow \text{True} \implies \text{score} = 1$
   - Check 2: $14 \ge 12 \rightarrow \text{True} \implies \text{score} = 2$
2. `any(c.isdigit())`: Contains `'2'`, `'0'`, `'2'`, `'5'` $\rightarrow \text{True} \implies \text{score} = 3$
3. `any(c.isupper())`: Contains `'T'`, `'H'`, `'M'` $\rightarrow \text{True} \implies \text{score} = 4$
4. `"TryHackMe2025!" not in common`: True $\implies \text{score} = 5$
Total returned score = `5`.

---

## Stage E: Final Answer & Key Values (الإجابة النهائية والقيم المستخرجة)
- **Question 1:** What keyword sends a value back from a function to the caller?
  - **Answer:** `return`
- **Question 2:** If a function is defined as `def scan(target, port=80):`, what value does `port` take if you call `scan("192.168.1.1")` without specifying a port?
  - **Answer:** `80`
- **Question 3:** On the VM, run `functions_demo.py`. What score does `score_password` return for the password `"TryHackMe2025!"`?
  - **Answer:** `5`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)
- **Automated Verification Script:**
  ```python
  def scan(target, port=80):
      return port

  assert scan("192.168.1.1") == 80
  assert scan("192.168.1.1", 443) == 443

  def score_password(pwd, common):
      score = 0
      if len(pwd) >= 8: score += 1
      if len(pwd) >= 12: score += 1
      if any(c.isdigit() for c in pwd): score += 1
      if any(c.isupper() for c in pwd): score += 1
      if pwd not in common: score += 1
      return score

  assert score_password("TryHackMe2025!", ["password", "admin"]) == 5
  print("Task 02 Functions and Parameters Verified Successfully.")
  ```
  Output:
  ```
  Task 02 Functions and Parameters Verified Successfully.
  ```

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)
Functions provide modular abstraction and maintainability. Always use `return` to emit data back into the pipeline, leverage default parameters for sensible standard options (e.g., default HTTP port 80), and strictly avoid mutable default parameters.
