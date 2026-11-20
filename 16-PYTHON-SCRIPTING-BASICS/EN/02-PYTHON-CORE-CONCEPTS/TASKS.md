# Room 02: Python: Core Concepts — Tasks & Solutions Index

## Room Metadata
- **Room Title:** Python: Core Concepts
- **Slug:** `pythoncoreconcepts`
- **Module:** 16 — Python Scripting Basics
- **Path:** Jr Penetration Tester
- **Difficulty:** Easy
- **Type:** Walkthrough / Hands-on Lab
- **Estimated Completion Time:** 60 minutes
- **Account Completion Status:** 100% Completed (Verified live on TryHackMe)

---

## Tasks Summary Matrix

| Task # | Task Title | Questions Count | Flags / Core Values Extracted | Status |
|:---:|---|:---:|---|:---:|
| **01** | Introduction | 1 | `No answer needed` | COMPLETE |
| **02** | Quick Review: Hello World, Variables, and Conditionals | 3 | `type()`, `str`, `Scan complete: 192.168.1.1 has 3 open ports` | COMPLETE |
| **03** | Working with Strings | 3 | `len()`, `Hack`, `.lower()` | COMPLETE |
| **04** | Lists and Dictionaries | 3 | `.append()`, `HTTP`, `.get()` | COMPLETE |
| **05** | Arithmetic and Membership Operators | 3 | `%`, `3`, `1024` | COMPLETE |
| **06** | Loops: for and while | 3 | `for`, `0, 1, 2`, `break` | COMPLETE |
| **07** | Conclusion | 1 | `No answer needed` | COMPLETE |

---

## Detailed Task Navigation

### [Task 01: Introduction](TASKS/TASK-01.md)
- **Focus:** High-volume credential auditing scenario, offensive automation pipeline, and curriculum overview.
- **Questions:**
  1. *Let's master Python's core concepts!* $\rightarrow$ `No answer needed`

### [Task 02: Quick Review: Hello World, Variables, and Conditionals](TASKS/TASK-02.md)
- **Focus:** Data type inspection (`type()`), `input()` behavior returning `str`, f-string formatting, and `fstrings_demo.py` execution.
- **Questions:**
  1. *What built-in function reveals the data type of a value?* $\rightarrow$ `type()`
  2. *If a user types 3.14 at an input() prompt, what data type does Python store it as before any conversion?* $\rightarrow$ `str`
  3. *On the attached VM, open and run fstrings_demo.py. What is the last line printed to the terminal?* $\rightarrow$ `Scan complete: 192.168.1.1 has 3 open ports`

### [Task 03: Working with Strings](TASKS/TASK-03.md)
- **Focus:** String length (`len()`), zero-indexed slicing (`word[3:7]`), case conversion (`.lower()`), and sanitization methods.
- **Questions:**
  1. *What function returns the number of characters in a string?* $\rightarrow$ `len()`
  2. *Given word = "TryHackMe", what does word[3:7] return?* $\rightarrow$ `Hack`
  3. *What string method converts "ADMIN" to "admin"?* $\rightarrow$ `.lower()`

### [Task 04: Lists and Dictionaries](TASKS/TASK-04.md)
- **Focus:** Ordered mutable lists, element appending (`.append()`), associative hash dictionaries, key lookup, and exception-safe retrieval (`.get()`).
- **Questions:**
  1. *What method adds an element to the end of a list?* $\rightarrow$ `.append()`
  2. *Given services = {22: "SSH", 80: "HTTP"}, what does services[80] return?* $\rightarrow$ `HTTP`
  3. *What dictionary method lets you retrieve a value with a safe fallback if the key does not exist?* $\rightarrow$ `.get()`

### [Task 05: Arithmetic and Membership Operators](TASKS/TASK-05.md)
- **Focus:** Specialized operators: modulus (`%`), integer floor division (`//`), exponentiation (`**`), membership (`in`, `not in`), and arbitrary-precision arithmetic.
- **Questions:**
  1. *What operator returns the remainder of a division?* $\rightarrow$ `%`
  2. *What does 10 // 3 evaluate to?* $\rightarrow$ `3`
  3. *What does 2 ** 10 evaluate to?* $\rightarrow$ `1024`

### [Task 06: Loops: for and while](TASKS/TASK-06.md)
- **Focus:** Definite sequence iteration with `for`, `range()` generator arithmetic, and early loop exit via `break`.
- **Questions:**
  1. *What type of loop is best suited for iterating over each item in a list?* $\rightarrow$ `for`
  2. *What does range(3) produce?* $\rightarrow$ `0, 1, 2`
  3. *What keyword immediately exits a loop?* $\rightarrow$ `break`

### [Task 07: Conclusion](TASKS/TASK-07.md)
- **Focus:** Synthesis of core constructs, credential auditor demonstration, and transition to modular script engineering in Room 03.
- **Questions:**
  1. *I have successfully completed the room!* $\rightarrow$ `No answer needed`
