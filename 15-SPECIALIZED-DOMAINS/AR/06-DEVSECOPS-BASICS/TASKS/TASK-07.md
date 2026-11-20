# Task 07: Exercise: Fuel Trouble

## Stage A: Original Task Content & Educational Context
In this interactive capstone comic exercise, you analyze a space expedition scenario to evaluate how different software development models respond to real-world risk, resource constraints, and operational uncertainties.

### Scenario Background:
A space exploration crew composed of four cross-functional specialists—**SEC3PO** (Security), **X Fighter Dev** (Development), **Chewba-QA** (Quality Assurance), and **S2-D2** (Operations)—funded by an investor named Watto, must navigate a starship across deep space.
- The ship has a maximum fuel capacity to travel **130,000 Light Years**.
- Their mission is to discover and extract valuable minerals from distant target planets while minimizing operational risk.
- **Candidate Planet Options:**
  - *Testooine:* 125,000 Light Years (High fuel risk; leaves minimal emergency margin).
  - *Hackboo:* 100,000 Light Years.
  - *TryHothMe:* 80,000 Light Years.
  - *Dagobug / Dagobah:* 60,000 Light Years (Lowest fuel risk; optimal safety margin).

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Model Evaluation Criteria:**
   - **Comic 1 (Rigid Execution):** The crew establishes a fixed destination at the start of the journey and stubbornly adheres to it despite preliminary sensor tests indicating high failure risk and near-total fuel depletion. This linear, unyielding behavior typifies the **Waterfall** model.
   - **Comic 2 (Iterative Adaptation with Gaps):** The crew demonstrates flexibility, adjusting their trajectory mid-flight based on changing conditions. However, testing is incomplete, and teams compromise on a suboptimal destination without full alignment. This sprint-based iteration typifies **Agile**.
   - **Comic 3 (Continuous Collaboration & Integrated Testing):** All crew members (Dev, QA, Security, Ops) collaborate from day one, conduct continuous automated diagnostics, re-evaluate trajectories dynamically, and select the safest, lowest-risk planet (Dagobah). This integrated collaboration typifies **DevOps / DevSecOps**.

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** What model sticks strictly to initial plans regardless of testing hints?
- **Q1 Tier 2 Hint:** One word: Waterfall.
- **Q2 Tier 1 Hint:** What model changes course adaptively in response to change, but had missing tests?
- **Q2 Tier 2 Hint:** One word: Agile.
- **Q3 Tier 1 Hint:** What model unites Dev, QA, Ops, and Sec from the start to perform comprehensive testing and choose the optimal route?
- **Q3 Tier 2 Hint:** One word: DevOps.
- **Q4 Tier 1 Hint:** Complete the interactive comic review steps to unlock the final flag.
- **Q4 Tier 2 Hint:** Flag format: `THM{ONE_...}`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive

### Step 1: Comic 1 Analysis (Waterfall)
- In Comic 1, the crew sticks rigidly to the original trajectory towards Testooine (125,000 Light Years) despite the fact that only preliminary tests were passed and the ship will barely have enough fuel to survive.
- *Model Identified:* **`Waterfall`**

### Step 2: Comic 2 Analysis (Agile)
- In Comic 2, the crew demonstrates sprint flexibility by altering their flight path mid-course. However, QA and security tests were omitted, resulting in a suboptimal intermediate planet.
- *Model Identified:* **`Agile`**

### Step 3: Comic 3 Analysis (DevOps / DevSecOps)
- In Comic 3, the entire cross-functional crew collaborates continuously, integrates rigorous multi-variable testing early in the journey, and selects the safest destination (Dagobah at 60,000 Light Years).
- *Model Identified:* **`DevOps`**

### Step 4: Extracting the Flag
Submitting all three correct development models in the interactive simulation reveals the capstone flag:
$$\mathbf{THM\{ONE\_TWO\_THREE\}}$$

---

## Stage E: Final Answer & Key Values
- **Question 1:** *What Software Development Model did the team in Comic 1 follow?*
  - **Answer:** `Waterfall`
- **Question 2:** *What Software Development Model did the team in Comic 2 follow?*
  - **Answer:** `Agile`
- **Question 3:** *What Software Development Model did the team in Comic 3 follow?*
  - **Answer:** `DevOps`
- **Question 4:** *What is the flag?*
  - **Answer:** `THM{ONE_TWO_THREE}`

---

## Stage F: Practical Verification & Proof of Work
1. Comic 1 matches the linear, rigid characteristics of `Waterfall`.
2. Comic 2 matches the sprint-based, flexible characteristics of `Agile`.
3. Comic 3 matches the integrated, continuous testing characteristics of `DevOps`.
4. The verification flag `THM{ONE_TWO_THREE}` confirmed on the TryHackMe platform.

---

## Stage G: Key Educational Takeaway
The *Fuel Trouble* simulation illustrates why DevSecOps is vital in modern engineering: integrating security, quality, and operations from the beginning eliminates guesswork, optimizes resource expenditure, and navigates complex mission parameters safely.
