# Task 03: Conditional Statements (الجمل الشرطية والتحكم في مسار التنفيذ)

## Stage A: Original Task Content & Educational Context (نص المهمة والسياق التعليمي)
Programs achieve decision-making capabilities through **conditional branching**. By evaluating expressions that resolve to Boolean values (`True` or `False`), the interpreter selects mutually exclusive execution paths.

In Python, multi-way decision trees are implemented using the `if`, `elif` (short for "else if"), and `else` keywords:

### Code Snippet (`guess_v2.py`):
```python
# Give a hint using if / elif / else.
if guess < 1 or guess > 20:
    print("That number is out of range. Try again.")
elif guess < secret:
    print("Too low, try again.")
elif guess > secret:
    print("Too high, try again.")
else:
    print("You got it in", tries, "tries!")
```

### Execution Flow Rules:
1. Python evaluates conditions sequentially from top to bottom.
2. The code block beneath the first condition evaluating to `True` is executed.
3. Once a matching condition block executes, Python skips all subsequent `elif` and `else` branches in that decision tree.
4. If none of the `if` or `elif` expressions evaluate to `True`, the catch-all `else` block executes.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والتشغيلية)
- **Short-Circuit Evaluation:** In logical expressions using `or`, Python evaluates operands from left to right. If the left-hand expression (`guess < 1`) evaluates to `True`, the entire expression is guaranteed to be `True`, and the interpreter skips evaluation of `guess > 20`. In offensive scripts, short-circuiting can be leveraged to prevent runtime exceptions (e.g., checking `if obj is not None and obj.status == 200:`).
- **Branch Order Vulnerabilities:** The order of conditional statements is vital. If boundary checks (`guess < 1 or guess > 20`) were placed after `guess < secret` or `guess > secret`, an input like `-5` would prematurely trigger `"Too low, try again."` instead of warning about range violation. In penetration testing tools, improper branch order can cause false positives during service fingerprinting or vulnerability scanning.
- **Timing Side Channels:** Complex branching conditions with differing execution times can introduce timing side-channel vulnerabilities. In cryptographic routines, comparing secret keys or hashes using `==` short-circuits on the first mismatched byte, allowing attackers to infer secret bytes by measuring nanosecond response differentials. Secure comparisons require constant-time comparison functions like `hmac.compare_digest()`.

---

## Stage C: Guided Hints (تلميحات متدرجة)
1. **Tier 1 (Conceptual):** Unlike languages like C, Java, or PHP which use `else if`, Python contracts this into a single 4-letter keyword.
2. **Tier 2 (Logic Trace):** Trace what happens when `guess = 50`. Evaluate the first line: `if guess < 1 or guess > 20:`. Since $50 > 20$ is `True`, what string does the `print()` function output?
3. **Tier 3 (Exact Solution):**
   - Python's "else if" syntax is `elif`.
   - An input of `50` triggers the first branch, outputting `That number is out of range. Try again.`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)
Let's analyze the exact evaluation steps when `guess = 50` and `secret = 12`:

```
Input: guess = 50, secret = 12

Step 1: Evaluate Condition 1
        guess < 1 or guess > 20
        50 < 1  --> False
        50 > 20 --> True
        False or True --> True!
        
Step 2: Execute Condition 1 Block
        print("That number is out of range. Try again.")
        
Step 3: Skip remaining elif / else branches
        - elif guess < secret (SKIPPED)
        - elif guess > secret (SKIPPED)
        - else (SKIPPED)
```

Terminal demonstration:
```python
guess = 50
secret = 12
tries = 1

if guess < 1 or guess > 20:
    print("That number is out of range. Try again.")
elif guess < secret:
    print("Too low, try again.")
elif guess > secret:
    print("Too high, try again.")
else:
    print("You got it in", tries, "tries!")
```
Output:
```
That number is out of range. Try again.
```

---

## Stage E: Final Answer & Key Values (الإجابة النهائية والقيم المستخرجة)
- **Question 1:** How does Python write “else if”?
  - **Answer:** `elif`
- **Question 2:** What will the program display if the user’s input is 50?
  - **Answer:** `That number is out of range. Try again.`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)
- **Test Script Verification:**
  ```python
  def test_conditions(guess, secret, tries=1):
      if guess < 1 or guess > 20:
          return "That number is out of range. Try again."
      elif guess < secret:
          return "Too low, try again."
      elif guess > secret:
          return "Too high, try again."
      else:
          return f"You got it in {tries} tries!"

  assert test_conditions(50, 10) == "That number is out of range. Try again."
  assert test_conditions(-5, 10) == "That number is out of range. Try again."
  assert test_conditions(5, 10) == "Too low, try again."
  assert test_conditions(15, 10) == "Too high, try again."
  assert test_conditions(10, 10, 3) == "You got it in 3 tries!"
  print("All conditional tests passed successfully.")
  ```
  Output:
  ```
  All conditional tests passed successfully.
  ```

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)
Conditional statements enforce logic gates in programs. Python uses `elif` for chained conditions. Ensure boundary and validation checks take precedence over normal operational branches to maintain reliable input sanitation.
