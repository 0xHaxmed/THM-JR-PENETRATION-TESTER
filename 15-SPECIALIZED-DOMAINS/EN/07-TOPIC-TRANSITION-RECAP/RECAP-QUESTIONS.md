# Module 15: Topic Transition Recap — Assessment Questions & Solutions

**Module:** Specialized Domains (`15-SPECIALIZED-DOMAINS`)  
**Canonical Recap URL:** `https://tryhackme.com/recap/6afe01d5f306fc3f659280a2`  
**Total Points Awarded:** 48 / 48 Points (100% First-Attempt Score)  
**Status:** Completed & Audited

---

## Question 1: Cloud IAM Over-Permissive Wildcards

### Question Text:
> A policy statement has `Effect: Allow` with `Action` set to `*` and `Resource` set to `*`. What does it grant?

### Available Options:
- `[A]` Access limited to a single bucket prefix
- `[B]` Access only within the account's home region
- `[C]` **Any action on any resource in the account** *(Verified Correct)*
- `[D]` Read-only access to all resources
- `[E]` Nothing, because wildcards are ignored by policy engines

### Detailed Feedback & Technical Deep Dive:
- **Verified Correct Answer:** `Any action on any resource in the account`
- **Platform Pedagogical Explanation:** *"Wildcards in both fields remove every restriction on operations and targets. Scoping to one prefix requires an explicit resource path, which the wildcard deliberately omits. Region is not expressed by these fields, so no such limitation exists in this statement. Nothing in the statement limits operations to reads; the wildcard covers write and delete actions too. Wildcards are fully honoured; they are the classic over-permissive pattern found on engagements."*
- **Exam Tip / Key Takeaway:** In cloud IAM (AWS, Azure, GCP), `"Action": "*"` and `"Resource": "*"` represents full administrative control (e.g., `AdministratorAccess`). An attacker acquiring credentials bound to this policy effectively owns the entire cloud tenant.

---

## Question 2: DevSecOps Shift-Left Core Philosophy

### Question Text:
> Leaving all security testing until the final stage before release is the recommended DevSecOps approach.

### Available Options:
- `[False]` **False** *(Verified Correct)*
- `[True]` True

### Detailed Feedback & Technical Deep Dive:
- **Verified Correct Answer:** `False`
- **Platform Pedagogical Explanation:** *"DevSecOps embeds security across every stage so flaws surface early and cost less to fix. Late-stage-only testing is the legacy model that caused delays, rollbacks and friction between teams."*
- **Exam Tip / Key Takeaway:** The core definition of **Shift-Left** in DevSecOps is moving security testing from the end of the development lifecycle upstream to the earliest phases (planning, design, and local coding). Late testing is the definition of the legacy Waterfall model.

---

## Question 3: LLM Jailbreaking & Generalization Mismatch

### Question Text:
> Why can wrapping a harmful request in Base64 raise the chance of bypassing a model's safety behaviour?

### Available Options:
- `[A]` Base64 strings bypass the tokeniser and reach the weights directly
- `[B]` **Safety behaviour was trained mainly on plain-text phrasings of such requests** *(Verified Correct)*
- `[C]` Encoded input is treated as data rather than instruction by the model
- `[D]` It shortens the prompt enough to fall under the safety filter threshold
- `[E]` Base64 encryption prevents the provider from decrypting the prompt

### Detailed Feedback & Technical Deep Dive:
- **Verified Correct Answer:** `Safety behaviour was trained mainly on plain-text phrasings of such requests`
- **Platform Pedagogical Explanation:** *"This is a generalisation mismatch: the encoded representation falls outside the distribution safety training covered. All input is tokenised; no encoding provides a path around that stage. The model has no data/instruction boundary at all, which is precisely why these attacks succeed. Base64 lengthens content, and refusal behaviour is not triggered by a length threshold. Base64 is an encoding, not encryption, and it is trivially reversible by anyone who sees it."*
- **Exam Tip / Key Takeaway:** RLHF (Reinforcement Learning from Human Feedback) trains safety refusals predominantly on natural language English prompts. Encoding payloads in Base64, ROT13, or low-resource languages exploits a **generalization mismatch**, where the model's ability to decode the payload exceeds its ability to recognize the harmfulness of the decoded intent.

---

## Question 4: Wireless & Bluetooth Attack Matching

### Question Text:
> Match each wireless attack technique to the outcome it achieves for the attacker.

### Matching Pairs:
1. **Bluesnarfing** $\longleftrightarrow$ **Unauthorised reading of data stored on the device**
   - *Platform Feedback:* "Here the attacker retrieves information held on the target device without permission. Matches the technique whose goal is extracting stored information."
2. **Bluebugging** $\longleftrightarrow$ **Control gained over device functions via a flaw**
   - *Platform Feedback:* "This goes beyond reading data by abusing a flaw to operate features of the device itself. Matches the technique that exploits a vulnerability to operate the device."
3. **Bluejacking** $\longleftrightarrow$ **Unsolicited messages sent to a nearby device**
   - *Platform Feedback:* "The mildest of the Bluetooth abuses: content is pushed toward a nearby device, but nothing is read or controlled. Matches the technique that only pushes content at a target in range."
4. **Deauthentication attack** $\longleftrightarrow$ **Clients forced to disconnect from the network**
   - *Platform Feedback:* "This targets availability of a wireless association rather than the data on any device. Matches the technique that disrupts existing wireless connections."

---

## Question 5: MITRE ATT&CK Structural Taxonomy

### Question Text:
> In MITRE ATT&CK, what sits directly below a technique as a more specific implementation of it?

### Available Options:
- `[A]` Indicators of compromise
- `[B]` Mitigations
- `[C]` Data sources
- `[D]` **Sub-techniques** *(Verified Correct)*
- `[E]` Tactics

### Detailed Feedback & Technical Deep Dive:
- **Verified Correct Answer:** `Sub-techniques`
- **Platform Pedagogical Explanation:** *"They narrow a technique to a specific method, such as password guessing under brute force. Indicators are observable artifacts like hashes or IPs; they are not part of the framework's behavioural hierarchy. Mitigations are defensive recommendations mapped to techniques, not a level of behavioural specificity. Data sources describe where evidence of a technique can be found, not a finer-grained behaviour. Tactics sit above techniques and describe the adversary's goal rather than a narrower method."*
- **Exam Tip / Key Takeaway:** The MITRE ATT&CK Enterprise hierarchy is:
  $$\text{Tactic (Goal - Why)} \longrightarrow \text{Technique (Method - What)} \longrightarrow \mathbf{\text{Sub-technique (Implementation - How)}} \longrightarrow \text{Procedure (Observed Execution)}$$

---

## Question 6: NFC vs. RFID Physical Operating Distance

### Question Text:
> NFC is designed to operate over a far shorter distance than typical RFID deployments.

### Available Options:
- `[False]` False
- `[True]` **True** *(Verified Correct)*

### Detailed Feedback & Technical Deep Dive:
- **Verified Correct Answer:** `True`
- **Platform Pedagogical Explanation:** *"Range is the main practical difference between the two: one requires near-touching proximity, the other does not. Contactless exchanges work within a few centimetres, while RFID tags can be read from metres away depending on tag type and band."*
- **Exam Tip / Key Takeaway:** Near Field Communication (NFC) operates at high frequency (13.56 MHz) and is strictly limited by physics to a transmission distance of less than 4–10 centimeters, making it suitable for secure contactless payments. Radio Frequency Identification (RFID) spans Low Frequency (125 kHz) through Ultra-High Frequency (UHF 860–960 MHz), which can be read from distances of several meters.
