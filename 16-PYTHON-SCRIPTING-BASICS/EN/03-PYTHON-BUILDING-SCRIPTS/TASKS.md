# Room 03: Python: Building Scripts — Tasks & Solutions Index

## Room Metadata
- **Room Title:** Python: Building Scripts
- **Slug:** `pythonbuildingscripts`
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
| **02** | Functions | 3 | `return`, `80`, `5` | COMPLETE |
| **03** | Error Handling | 3 | `ValueError`, `FileNotFoundError`, `ZeroDivisionError` | COMPLETE |
| **04** | Reading and Writing Files | 3 | `with`, `.strip()`, `58` | COMPLETE |
| **05** | Libraries and Pip | 3 | `pip`, `string`, `5` | COMPLETE |
| **06** | Putting It All Together: Password Strength Checker | 3 | `0`, `Strong`, `14` | COMPLETE |
| **07** | Conclusion | 1 | `No answer needed` | COMPLETE |

---

## Detailed Task Navigation

### [Task 01: Introduction](TASKS/TASK-01.md)
- **Focus:** Transitioning to modular software engineering, four pillars of robust scripting, and project overview.
- **Questions:**
  1. *Let's build real scripts!* $\rightarrow$ `No answer needed`

### [Task 02: Functions](TASKS/TASK-02.md)
- **Focus:** Function definitions (`def`), parameters vs arguments, default values, return statements (`return`), scope isolation, and `functions_demo.py` execution.
- **Questions:**
  1. *What keyword sends a value back from a function to the caller?* $\rightarrow$ `return`
  2. *If a function is defined as `def scan(target, port=80):`, what value does `port` take if you call `scan("192.168.1.1")` without specifying a port?* $\rightarrow$ `80`
  3. *On the VM, run `functions_demo.py`. What score does `score_password` return for the password `"TryHackMe2025!"`?* $\rightarrow$ `5`

### [Task 03: Error Handling](TASKS/TASK-03.md)
- **Focus:** Runtime exception mechanics, `try / except` blocks, specific vs general exceptions, input validation loops, and common exception types.
- **Questions:**
  1. *What type of exception is raised when you attempt `int("hello")`?* $\rightarrow$ `ValueError`
  2. *What type of exception is raised when you try to open a file that does not exist?* $\rightarrow$ `FileNotFoundError`
  3. *On the VM, open a Python terminal and type `print(10 / 0)`. What is the name of the exception that Python raises?* $\rightarrow$ `ZeroDivisionError`

### [Task 04: Reading and Writing Files](TASKS/TASK-04.md)
- **Focus:** Context managers (`with`), file descriptor safety, streaming file iteration, line stripping (`.strip()`), write modes (`"r"`, `"w"`, `"a"`), and `files_demo.py` execution.
- **Questions:**
  1. *What keyword introduces a context manager for safely opening files?* $\rightarrow$ `with`
  2. *What string method removes the trailing newline character from each line read from a file?* $\rightarrow$ `.strip()`
  3. *On the VM, run `files_demo.py`. How many passwords did it load from `common_passwords.txt`?* $\rightarrow$ `58`

### [Task 05: Libraries and Pip](TASKS/TASK-05.md)
- **Focus:** Modular imports (`import`, `from ... import`, `as`), Standard Library utilities (`string`, `hashlib`), PyPI package management via `pip`, and SHA-256 hash extraction from `imports_demo.py`.
- **Questions:**
  1. *What is the name of Python's package manager used to install third-party libraries?* $\rightarrow$ `pip`
  2. *Which module from the standard library provides constants like `ascii_uppercase`, `digits`, and `punctuation`?* $\rightarrow$ `string`
  3. *On the VM, run `imports_demo.py`. What is the first character of the SHA-256 hash it prints for the default input string?* $\rightarrow$ `5`

### [Task 06: Putting It All Together: Password Strength Checker](TASKS/TASK-06.md)
- **Focus:** Complete end-to-end security project (`password_checker.py`), dictionary override logic, interactive CLI loop, strength rating, and asterisk masking (`password_log.txt`).
- **Questions:**
  1. *In the `check_password` function, what score does the password receive if it is found in the common_passwords list?* $\rightarrow$ `0`
  2. *On the VM, run `password_checker.py` and enter the password `TryHackMe!2025`. What strength label does the program display?* $\rightarrow$ `Strong`
  3. *After running the checker with the password `TryHackMe!2025`, open `password_log.txt` on the VM. How many asterisks appear on that line?* $\rightarrow$ `14`

### [Task 07: Conclusion](TASKS/TASK-07.md)
- **Focus:** Synthesis of script building principles and conceptual roadmap to offensive pentesting scripts (sockets, port scanning, web fuzzing).
- **Questions:**
  1. *I have successfully completed the room!* $\rightarrow$ `No answer needed`
