# Task 02 - Deploy the VM (تشغيل البيئة المعملية الافتراضية)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Deploy the attached virtual machine and access the web application at http://MACHINE_IP.

The target machine hosts a series of educational labs designed to systematically demonstrate various File Inclusion vulnerabilities and filtering bypasses:
- Lab #1: Basic Local File Inclusion with absolute path references.
- Lab #2: Directory prepending and relative path traversal.
- Lab #3: Appended file extensions and null byte injection (%00).
- Lab #4: Keyword and pattern-based filtering bypasses.
- Lab #5: Non-recursive path traversal stripping.
- Lab #6: Mandatory directory prefix validation bypasses.
- Lab #Playground: Remote File Inclusion (RFI) testing ground.
- Challenges: Multi-stage challenges incorporating POST requests, cookie manipulation, and filter evasion.

Ensure you allow 2-3 minutes for the web server and PHP engine to boot completely before sending requests.
```

### 2. Arabic Pedagogical Translation
```text
قم بتشغيل الآلة الافتراضية المرفقة، والوصول إلى تطبيق الويب عبر الرابط: `http://MACHINE_IP`.

تستضيف الآلة المستهدفة مجموعة من المعامل التعليمية المصممة بطريقة منهجية لشرح مختلف ثغرات تضمين الملفات وأساليب الالتفاف على الفلاتر:
- معمل Lab #1: التضمين المحلي الأساسي للملفات عبر استدعاء المسارات المطلقة.
- معمل Lab #2: التعامل مع المجلدات المسبقة واجتياز المسارات النسبية (Relative Traversal).
- معمل Lab #3: التعامل مع الامتدادات الإجبارية واستغلال حقن البايت الفارغ (%00).
- معمل Lab #4: الالتفاف على فلاتر الكلمات المفتاحية والأنماط الثابتة.
- معمل Lab #5: الالتفاف على عمليات حذف الرموز غير التكرارية (Non-recursive Stripping).
- معمل Lab #6: تجاوز التحقق الإلزامي من بادئة المجلد المحدد.
- معمل Lab #Playground: بيئة اختبار التضمين البعيد للملفات (RFI) وتنفيذ الأوامر عن بُعد.
- قسم التحديات (Challenges): تحديات متقدمة تتطلب استخدام طلبات POST، والتلاعب بالكوكيز، وتجاوز الفلاتر المعقدة.

يرجى الانتظار من دقيقتين إلى ثلاث دقائق حتى يكتمل إقلاع خادم الويب ومحرك PHP قبل إرسال الطلبات.
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Verification of Target Availability
قبل استهلاك الوقت في اختبار الفلاتر، نتحقق من جاهزية خادم Apache:
```bash
curl -s -o /dev/null -w "%{http_code}\n" http://MACHINE_IP/
```
إذا كانت النتيجة `200`، فالخادم جاهز لاستقبال الطلبات والاختبارات.

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** المهمة مخصصة لتشغيل الآلة الافتراضية والتحقق من الاتصال عبر الشبكة.
>
> **المستوى الثاني (توجيهي):** اضغط على زر `Start Machine` وانتظر ظهور عنوان الـ IP المخصص.
>
> **المستوى الثالث (الحل المباشر):** انقر على زر `Completed` للانتقال إلى شرح تقنيات Path Traversal.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Lab Architecture & Endpoints Map
* الصفحة الرئيسية: `http://MACHINE_IP/`
* الروابط المعملية:
  - `http://MACHINE_IP/lab1.php`
  - `http://MACHINE_IP/lab2.php`
  - `http://MACHINE_IP/lab3.php`
  - `http://MACHINE_IP/lab4.php`
  - `http://MACHINE_IP/lab5.php`
  - `http://MACHINE_IP/lab6.php`
  - `http://MACHINE_IP/playground.php`
  - `http://MACHINE_IP/challenges/index.php`

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال / المتطلب | الإجابة النموذجية |
| :--- | :--- |
| **Deploy the VM and progress to the next section.** | `No answer needed` (تم التشغيل والتحقق بنجاح) |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من عمل الآلة والاتصال بها بنجاح واجتياز المهمة بنسبة 100%.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **الترتيب المنطقي للبيئة المعملية:** بناء الغرفة على هيئة معامل منفصلة من Lab 1 إلى Lab 6 يوضح التدرج الواقعي في كيفية محاولة المطورين حظر ثغرات اجتياز المسارات عبر كتابة فلاتر يدوية ضعيفة، وكيف ينجح المهاجم دائماً في الالتفاف عليها ما لم يتم تطبيق المعايير الهندسية الصحيحة.
