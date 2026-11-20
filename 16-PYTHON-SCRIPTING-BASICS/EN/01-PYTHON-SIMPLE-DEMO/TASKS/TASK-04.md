# Task 04: Iterations & While Loops (التكرار وحلقات الدوران)

## Stage A: Original Task Content & Educational Context (نص المهمة والسياق التعليمي)
Without iterative constructs, a script executes linearly from top to bottom and terminates after a single guess. To allow the player to guess repeatedly until they discover the secret number, the program introduces a **loop**.

Python supports two primary looping paradigms:
- `for` loops (definite iteration over a known sequence or range).
- `while` loops (indefinite iteration running as long as a Boolean condition remains `True`).

In this task, the game employs a `while` loop controlled by the inequality relational operator `!=` ("does not equal"):
```python
while guess != secret:
```

### Complete Game Code (`guess_v3.py`):
```python
import random

secret = random.randint(1, 20)
tries = 0
guess = 0  # Starts out of range so that (guess != secret) is initially True

print("I'm thinking of a number between 1 and 20")

# Repeat until the user guesses the secret number
while guess != secret:
    text = input("Take a guess: ")
    guess = int(text)
    
    tries = tries + 1

    # Give a hint using if / elif / else
    if guess < 1 or guess > 20:
        print("That number is out of range. Try again.")
    elif guess < secret:
        print("Too low, try again.")
    elif guess > secret:
        print("Too high, try again.")
    else:
        print("You got it in", tries, "tries!")
```

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والتشغيلية)
- **Infinite Loops & Resource Exhaustion (DoS):** A `while` loop depends strictly on the condition evaluating to `False` at some point. If the condition never changes (e.g., forgetting to update `guess` or writing `while True:` without a break statement or socket timeout), the process enters an infinite loop, consuming 100% of a CPU core. In offensive scripts (such as port scanners or banner grabbers), infinite loops without socket timeouts can freeze the testing tool indefinitely.
- **State Initialization:** Notice that `guess` is initialized to `0`. If `guess` were initialized to `random.randint(1, 20)`, there is a 5% chance ($1/20$) that `guess == secret` on initial evaluation, causing the loop to never execute! Initializing `guess = 0` guarantees that `guess != secret` evaluates to `True` on the first check.
- **Memory & Stack Impact:** Unlike recursion, iterative `while` loops do not allocate new stack frames for each iteration, making them memory-efficient for long-running network polling and daemon listeners.

---

## Stage C: Guided Hints (تلميحات متدرجة)
1. **Tier 1 (Conceptual):** Look at the loop keyword at the start of line 16. It is a 5-letter keyword that means "as long as".
2. **Tier 2 (Syntax Check):** Look at the `else` clause inside the loop: `print("You got it in", tries, "tries!")`. If the user succeeds on attempt number 3, replace `tries` with `3`.
3. **Tier 3 (Exact Solution):**
   - The loop type is `while`.
   - The output string when `tries = 3` is `You got it in 3 tries!`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)
Let's trace how the `print()` function formats multiple arguments separated by commas:
```python
tries = 3
print("You got it in", tries, "tries!")
```
In Python's `print()` function:
- Argument 1: `"You got it in"`
- Argument 2: `3` (automatically converted to string `"3"`)
- Argument 3: `"tries!"`
By default, `sep=' '` inserts a single space between each argument. Therefore:
$$\text{"You got it in"} + \text{" "} + \text{"3"} + \text{" "} + \text{"tries!"} = \text{"You got it in 3 tries!"}$$

### Step-by-Step Loop State Progression Table:
Suppose `secret = 7`:

| Iteration | Initial `guess != secret` | User Input (`text`) | `guess = int(text)` | `tries` | Condition Evaluated | Terminal Output |
|:---:|:---:|:---:|:---:|:---:|:---:|---|
| **Start** | `0 != 7` (`True`) | - | `0` | `0` | - | `"I'm thinking of a number..."` |
| **Round 1** | `0 != 7` (`True`) | `"15"` | `15` | `1` | `15 > 7` (`elif`) | `"Too high, try again."` |
| **Round 2** | `15 != 7` (`True`) | `"3"` | `3` | `2` | `3 < 7` (`elif`) | `"Too low, try again."` |
| **Round 3** | `3 != 7` (`True`) | `"7"` | `7` | `3` | `7 == 7` (`else`) | `"You got it in 3 tries!"` |
| **End** | `7 != 7` (`False`) | - | `7` | `3` | Loop Exits | Program terminates cleanly |

---

## Stage E: Final Answer & Key Values (الإجابة النهائية والقيم المستخرجة)
- **Question 1:** What type of loop does this program use?
  - **Answer:** `while`
- **Question 2:** What will the program display if the user makes the correct guess in 3 tries?
  - **Answer:** `You got it in 3 tries!`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)
- **Mocking the Loop Programmatically:**
  ```python
  def simulate_game(secret, guesses):
      tries = 0
      guess = 0
      output = []
      
      idx = 0
      while guess != secret:
          guess = guesses[idx]
          idx += 1
          tries += 1
          
          if guess < 1 or guess > 20:
              output.append("That number is out of range. Try again.")
          elif guess < secret:
              output.append("Too low, try again.")
          elif guess > secret:
              output.append("Too high, try again.")
          else:
              output.append(f"You got it in {tries} tries!")
              
      return output

  sim_output = simulate_game(7, [15, 3, 7])
  assert sim_output[-1] == "You got it in 3 tries!"
  print("Simulation Output:", sim_output)
  print("Verification Successful!")
  ```
  Output:
  ```
  Simulation Output: ['Too high, try again.', 'Too low, try again.', 'You got it in 3 tries!']
  Verification Successful!
  ```

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)
Loops enable programs to perform repetitive work autonomously. `while` loops are essential in penetration testing for continuous listeners, asynchronous polling, retry logic on flaky connections, and brute-force wordlist iteration.
