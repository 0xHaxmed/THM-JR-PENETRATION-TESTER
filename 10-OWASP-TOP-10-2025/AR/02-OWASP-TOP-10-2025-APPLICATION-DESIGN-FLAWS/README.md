# غرفة: OWASP Top 10: 2025 - عيوب التصميم المعماري للتطبيقات (Application Design Flaws)

---

## 1. نظرة عامة على الغرفة وهندستها المعمارية (Room Overview & Architecture)

تُعد غرفة **OWASP Top 10: 2025 - Application Design Flaws** (`owasptopten2025two`) الغرفة التطبيقية الثانية في **الوحدة العاشرة: OWASP Top 10 2025** على مسار Jr Penetration Tester في منصة TryHackMe.
تركز هذه الغرفة على تشريح الثغرات الأمنية التي لا تنشأ عن مجرد أخطاء برمجية عابرة في الكود، بل تتجذر بعمق في البنية المعمارية للأنظمة وسوء التهيئة التشغيلية:
- **AS02: Security Misconfigurations (التهيئة الأمنية الخاطئة):** خوادم وبيئات تشغيلية بنوافذ تصحيح مفتوحة ورسائل خطأ تفصيلية تسرب هياكل النظم والأسرار.
- **AS03: Software Supply Chain Failures (إخفاقات سلاسل توريد البرمجيات):** استيراد مكتبات وحزم برمجية ونماذج ذكاء اصطناعي قديمة أو ملغومة دون تدقيق مستقل.
- **AS04: Cryptographic Failures (الإخفاقات التشفيرية):** تضمين مفاتيح التشفير بشكل نصي صلب داخل الكود المصدر، أو استخدام خوارزميات مكسورة.
- **AS06: Insecure Design (التصميم غير الآمن):** افتراضات معمارية فاسدة تثق بالبيانات الوصفية المرسلة من العميل (مثل ترويسات المتصفح).

```
       +-------------------------------------------------------------+
       |             APPLICATION DESIGN FLAWS ARCHITECTURE           |
       +-------------------------------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
+------------------+                                      +------------------+
|      AS02:       |                                      |      AS03:       |
| MISCONFIGURATION |                                      |   SUPPLY CHAIN   |
|   (Port 5002)    |                                      |   (Port 5003)    |
+------------------+                                      +------------------+
| - Verbose Stack  |                                      | - Outdated Libs  |
| - Debug Leakage  |                                      | - /api/process   |
| - Local Frames   |                                      | - Hidden Params  |
+------------------+                                      +------------------+
         |                                                         |
         +----------------------------+----------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
+------------------+                                      +------------------+
|      AS04:       |                                      |      AS06:       |
|  CRYPTO FAILURES |                                      |  INSECURE DESIGN |
|   (Port 5004)    |                                      |   (Port 5005)    |
+------------------+                                      +------------------+
| - Hardcoded Key  |                                      | - Untrusted Meta |
| - Static Secrets |                                      | - User-Agent Spoof|
| - Vault Bypass   |                                      | - Mobile Bypass  |
+------------------+                                      +------------------+
```

---

## 2. المتطلبات الأساسية وإعداد المعمل (Prerequisites & Lab Setup)

### 2.1 المتطلبات التقنية والمعرفية
- فهم المعمارية متعددة الطبقات لتطبيقات الويب (Presentation, Application, Data Layer).
- معرفة أساسية بمبادئ التشفير المتماثل (Symmetric Encryption) وإدارة المفاتيح.
- الإلمام ببيئات إدارة الحزم (PyPI, npm) ومفهوم شجرة التبعيات (Dependency Trees).
- القدرة على استخدام أدوات التفاعل الشبكي مثل `curl` وتعديل ترويسات HTTP.

### 2.2 بنية المعمل وتوزيع الخدمات
يتم تشغيل آلة افتراضية تقدم 4 خدمات مستقلة عبر منافذ HTTP مخصصة:
- **المنفذ 5002:** خدمة إدارة المستخدمين المصابة برسائل الخطأ التفصيلية (AS02).
- **المنفذ 5003:** واجهة معالجة الطلبات المعتمدة على مكتبة خارجية قديمة (AS03).
- **المنفذ 5004:** قبو فك تشفير البيانات المعتمد على مفتاح تشفير صلب داخل الكود (AS04).
- **المنفذ 5005:** واجهة العمليات المقيدة للأجهزة المحمولة بافتراض معماري غير آمن (AS06).

---

## 3. الأهداف التعليمية وتصنيف بلوم (Learning Objectives & Bloom's Taxonomy)

1. **التذكر والفهم (Remembering & Understanding):** استيعاب الفارق الهيكلي بين علل الكود البرمجي (Bugs) وعيوب التصميم المعماري (Design Flaws)، ودراسة قضايا واقعية مثل اختراق SolarWinds و Uber و Clubhouse.
2. **التطبيق والتحليل (Applying & Analyzing):** إثارة أخطاء النظام لاستدراج رسائل التتبع (Stack Traces)؛ تدقيق تبعيات الطرف الثالث المستوردة؛ وفحص الشفرات البرمجية للعثور على الأسرار المدمجة.
3. **التقييم والاستنتاج (Evaluating & Synthesizing):** تقييم هشاشة الافتراضات المبنية على بيانات العميل (مثل ترويسة `User-Agent`)، واستنتاج خطورة الثقة غير المبررة في أجهزة الهواتف المحمولة.
4. **التصميم والدفاع (Creating & Defending):** تصميم حلول وقائية جذرية تشمل: معالجات الأخطاء الموحدة، ومستودعات التخزين الآمن للأسرار (KMS/Vault)، وفحص التبعيات بـ SBOM، ونمذجة التهديدات بنظام STRIDE.

---

## 4. العمق المفاهيمي والتقني: عيوب التصميم والتهيئة (Technical Concept Deep Dive)

### 4.1 تفكيك تصنيفات معيار OWASP 2025 المشمولة
- **AS02 (Security Misconfigurations):** البيئات غير المحصنة تترك وضع التصحيح مفعلاً (`DEBUG=True`). عندما يواجه الخادم مدخلاً شاذاً، يقوم بإلقاء استثناء غير معالج يسرب متغيرات الذاكرة وتفاصيل المسارات.
- **AS03 (Software Supply Chain Failures):** الاعتماد على حزم برمجية خارجية دون فحص محتواها الداخلي يعرض التطبيق للأبواب الخلفية وميزات التصحيح المنسية التي يمكن استدعاؤها عبر معاملات خفية.
- **AS04 (Cryptographic Failures):** كسر مبدأ كيرشوف (Kerckhoffs's Principle) بوضع المفتاح المتماثل داخل ملفات العرض أو السكربتات المكشوفة، مما يلغي الحماية التشفيرية تماماً.
- **AS06 (Insecure Design):** بناء نظام أمان يفترض أن الطلب القادم من متصفح محمول هو طلب آمن وموثوق، بينما بروتوكول HTTP يسمح لأي أداة بتزييف قيمة `User-Agent`.

---

## 5. سطح الهجوم ونمذجة التهديدات عبر المنافذ (Attack Surface & Threat Modeling)

```
[Attacker]
   |
   +---> (Port 5002) Malformed Type Input   ---> Leaks DEBUG_FLAG via 500 Stack Trace
   |
   +---> (Port 5003) POST /api/process      ---> Triggers legacy debug mode in library
   |
   +---> (Port 5004) Inspect vault.js       ---> Extracts MASTER_VAULT_KEY
   |
   +---> (Port 5005) Spoof Mobile User-Agent---> Bypasses "Mobile Only" access check
```

---

## 6. منهجية الاستغلال والحل العملي المفصل خطوة بخطوة (Exploitation Methodology)

### الخطوة 1: استغلال التهيئة الخاطئة وتسريب رسائل الخطأ (Task 02 - Port 5002)
1. استهداف واجهة إدارة المستخدمين بإرسال قيمة نصية غير متوقعة لمعرف المستخدم:
   ```bash
   curl -i "http://MACHINE_IP:5002/api/user/error_test"
   ```
2. يستجيب الخادم برمز خطأ `500 Internal Server Error` ويعرض تتبع المكدس كاملاً.
3. تفحص المتغيرات المحلية في إطار العمل يكشف راية التحدي:
   ```text
   Flag: THM{V3RB0S3_3RR0R_L34K}
   ```

### الخطوة 2: استغلال ثغرة سلاسل التوريد في المكتبات المستوردة (Task 03 - Port 5003)
1. فحص كود المكتبة المستوردة `lib/vulnerable_utils.py` المرفقة في المعمل.
2. نلاحظ وجود شرط تصحيح قديم: إذا كان المدخل يحتوي على `debug: true`، يقوم التابع بإرجاع الراية.
3. إرسال طلب POST مخصص إلى المسار `/api/process`:
   ```bash
   curl -s -X POST http://MACHINE_IP:5003/api/process \
     -H "Content-Type: application/json" \
     -d '{"debug": true}'
   ```
4. استخراج الراية من الرد:
   ```text
   Flag: THM{SUPPLY_CH41N_VULN3R4B1L1TY}
   ```

### الخطوة 3: استخراج مفتاح التشفير الصلب وفك تشفير البيانات (Task 04 - Port 5004)
1. استعراض ملفات الواجهة الأمامية أو كود الخادم في القبو المشفر `http://MACHINE_IP:5004`.
2. العثور على المفتاح المتماثل المضمن صراحة في الكود:
   ```text
   MASTER_VAULT_KEY = "Sup3rS3cr3tCryptoK3y2025!"
   ```
3. استخدام المفتاح في واجهة فك التشفير لكشف المحتوى السري والراية:
   ```text
   Flag: THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}
   ```

### الخطوة 4: كسر الافتراض المعماري وتزييف ترويسة العميل (Task 05 - Port 5005)
1. زيارة `http://MACHINE_IP:5005` ينتج عنها رسالة `403 Forbidden` تطلب استخدام جهاز محمول.
2. تنفيذ الطلب باستخدام ترويسة هاتف محمول مخصصة عبر `curl`:
   ```bash
   curl -s -H "User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 17_0 like Mac OS X) AppleWebKit/605.1.15" http://MACHINE_IP:5005/
   ```
3. يستجيب الخادم بعرض لوحة التحكم الخاصة بالهواتف المحمولة وتظهر الراية المعتمدة:
   ```text
   Flag: THM{1NS3CUR3_D35IGN_4SSUMPT10N}
   ```

---

## 7. التحليل الجنائي وتفتيش الأصول البرمجية (Forensic Analysis & Artifact Inspection)

- **تفتيش إطارات الذاكرة (Memory Frames):** يوضح تحليل استجابة المنفذ 5002 أن تفعيل بيئة التطوير في بيئات الإنتاج يعرض كائنات الذاكرة الحية (Live Objects) للمهاجم دون الحاجة لصلاحيات جذرية.
- **فحص شجرة التبعيات (Dependency Auditing):** أثبت تحدي المنفذ 5003 أن المطور الرئيسي قد يكتب كوداً آمناً بنسبة 100%، ولكن استيراد دالة واحدة من حزمة غير موثوقة يقوض أمان النظام بالكامل.

---

## 8. الهندسة الدفاعية والحلول المعمارية الجذرية (Defensive Engineering & Remediation)

### 8.1 إغلاق تسريب الأخطاء وتعطيل بيئة التطوير
- فرض إيقاف خيارات التصحيح في كافة البيئات الحية (`DEBUG = False`).
- تطبيق معالجات أخطاء موحدة ترجع معرّف ارتباط عشوائي (Correlation ID) للمستخدم مع تسجيل التفاصيل داخلياً في الـ SIEM.

### 8.2 إدارة أمان سلاسل التوريد (SBOM & SCA)
- توليد قوائم البرمجيات (SBOM) دورياً بواسطة أدوات مثل Syft.
- حظر نشر أي تطبيق يتضمن مكتبات مصنفة كقديمة أو تحتوي على ثغرات معروفة (عبر أدوات `pip-audit` أو `trivy`).

### 8.3 الحماية التشفيرية وإدارة الأسرار
- منع كتابة المفاتيح والأسرار في الكود بتفعيل أدوات التدقيق التلقائي للـ Git (`gitleaks`, `trufflehog`).
- تفويض عمليات التشفير إلى أنظمة KMS السحابية واستخدام خوارزميات التشفير الموثق الحديثة (`AES-256-GCM`).

### 8.4 التحقق الصارم من الأجهزة وتجنب الافتراضات الفاسدة
- عدم الاعتماد إطلاقاً على ترويسة `User-Agent` لأغراض الأمان أو تقييد الصلاحيات.
- تطبيق التوثيق العتادي المشفر للأجهزة المحمولة عبر شهادات متبادلة (mTLS) أو واجهات الأمان الرسمية (Google Play Integrity / Apple DeviceCheck).

---

## 9. مرجع الأوامر والأدوات (Tools & Command Reference)

| الأداة | الأمر النموذجي | الغرض العملياتي |
| :--- | :--- | :--- |
| `curl` | `curl -i "http://target:5002/api/user/test"` | إثارة خطأ 500 لاستخراج تتبع المكدس |
| `curl (POST)` | `curl -s -X POST -d '{"debug":true}' http://target:5003/api/process` | إرسال معامل تصحيح خفي إلى الـ API |
| `curl (Header)` | `curl -s -H "User-Agent: iPhone..." http://target:5005/` | تزييف ترويسة المتصفح لتجاوز فحص الهاتف |
| `gitleaks` | `gitleaks detect --source .` | فحص مستودعات الكود لمنع تسريب المفاتيح الصلبة |

---

## 10. الخلاصة المعرفية والخريطة الذهنية (Key Takeaways & Mind Map)

```
                 +-----------------------------------------+
                 |    APPLICATION DESIGN FLAWS TAXONOMY    |
                 +-----------------------------------------+
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
[AS02: Misconfigurations]                                 [AS03: Supply Chain]
- Debug mode in production                                - Unverified 3rd-party code
- Verbose stack trace leak                                - Dormant debug parameters
- Fix: Sanitized error handlers                           - Fix: SBOM & strict SCA scans
                                      |
         +----------------------------+----------------------------+
         |                                                         |
         v                                                         v
[AS04: Cryptographic Failures]                            [AS06: Insecure Design]
- Hard-coded master keys                                  - Trusting client User-Agent
- Cleartext secret storage                                - False client boundaries
- Fix: Hardware KMS & Vault                               - Fix: Hardware attestation & mTLS
```

---

## 11. مصفوفة المخاطر والثغرات (Vulnerability & Risk Matrix)

| الثغرة (Vulnerability) | التصنيف (OWASP 2025) | الشدة (Severity) | معرّف CWE | الأثر التقني والتشغيلي (Impact) |
| :--- | :---: | :---: | :---: | :--- |
| Verbose Error Information Disclosure | **AS02:2025** | **متوسطة (MEDIUM)** | CWE-209 | تسريب متغيرات الذاكرة الداخلية وتفاصيل معمارية الخادم. |
| Vulnerable Third-Party Library | **AS03:2025** | **عالية (HIGH)** | CWE-1395 | تمكين المهاجم من استدعاء آليات تصحيح كامنة غير موثقة. |
| Use of Hard-coded Cryptographic Key | **AS04:2025** | **حرجة (CRITICAL)** | CWE-798 | الانهيار الكامل لسرية الملفات والبيانات المشفرة. |
| Trust in Untrusted Client Metadata | **AS06:2025** | **عالية (HIGH)** | CWE-285 | تجاوز القيود الأمنية المخصصة للأجهزة المصرحة. |

---

## 12. التحقق وإثبات الإنجاز (Verification & Proof of Work)

تم إكمال كافة مهام الغرفة بنسبة 100% مع مطابقة تامة لحساب المستخدم على المنصة:
- **المهمة 01:** `No answer needed`
- **المهمة 02:** `THM{V3RB0S3_3RR0R_L34K}`
- **المهمة 03:** `THM{SUPPLY_CH41N_VULN3R4B1L1TY}`
- **المهمة 04:** `THM{CRYPTO_FAILURE_H4RDCOD3D_K3Y}`
- **المهمة 05:** `THM{1NS3CUR3_D35IGN_4SSUMPT10N}`
- **المهمة 06:** `No answer needed`

---

## 13. الأسئلة الشائعة والأخطاء المحتملة (Frequently Asked Questions)

### س1: لماذا لا يمكن تصنيف ثغرة User-Agent على أنها خطأ برمجي (Bug) بدلاً من عيب تصميمي (Design Flaw)؟
- لأن المطور كتب الكود كما أراد تماماً: "إذا كان العميل هاتفاً محمولاً اسمح له، وإلا فامنع". الخلل هنا ليس في كتابة جملة `if`، بل في القرار المعماري الكارثي الذي افترض أن العميل لا يمكنه الكذب بشأن هويته في بروتوكول مفتوح مثل HTTP.

### س2: كيف تؤثر نماذج الذكاء الاصطناعي (AI Models) على سلاسل التوريد البرمجية؟
- أصبحت النماذج المستوردة من منصات مثل Hugging Face تعامل كـ "تبعيات برمجية". إذا تم تدريب النموذج على بيانات مسمومة أو تم تضمين أوزان خبيثة، فإن النموذج سيتصرف كباب خلفي داخل التطبيق عند استدعائه.

---

## 14. المراجع وروابط التوثيق الرسمية (References & External Links)

- [OWASP Top 10: 2025 Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)
- [OWASP Software Component Verification Standard (SCVS)](https://scvs.owasp.org/)
- [NIST SP 800-218: Secure Software Development Framework](https://csrc.nist.gov/publications/detail/sp/800-218/final)
- [CWE-798: Use of Hard-coded Credentials](https://cwe.mitre.org/data/definitions/798.html)
- [TryHackMe Room: OWASP Top 10 2025 - Application Design Flaws](https://tryhackme.com/room/owasptopten2025two)
