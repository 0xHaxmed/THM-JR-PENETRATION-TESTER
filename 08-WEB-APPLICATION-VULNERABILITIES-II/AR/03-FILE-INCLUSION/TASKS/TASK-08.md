# Task 08 - Remediation (التحصين الهندسي والمعالجة الجذرية)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Remediating file inclusion vulnerabilities requires a defense-in-depth approach combining strict code-level input validation, secure runtime configuration, and environment isolation.

1. Strict Allowlisting (Whitelisting):
   Never pass user input directly into file inclusion or file-system functions. Instead, map allowed user choices to static, hardcoded file paths:
   <?php
       $languages = [
           'en' => 'languages/en.php',
           'es' => 'languages/es.php',
           'fr' => 'languages/fr.php'
       ];
       $lang = $_GET['lang'] ?? 'en';
       if (array_key_exists($lang, $languages)) {
           include($languages[$lang]);
       } else {
           include('languages/en.php');
       }
   ?>

2. Hardening php.ini Configuration:
   - Disable remote file inclusion: allow_url_include = Off
   - Disable remote URL stream opening if not required: allow_url_fopen = Off
   - Disable verbose error display in production: display_errors = Off
   - Enable logging to a secure internal file: log_errors = On

3. Path Sanitisation Functions:
   - Use basename() to strip all directory traversal sequences (../, ..\):
     $file = basename($_GET['file']);
   - Use realpath() combined with str_starts_with() to ensure the resolved canonical path strictly resides within the authorized base directory.

Question:
- I can now exploit LFI vulnerabilities!
```

### 2. Arabic Pedagogical Translation
```text
تتطلب معالجة ثغرات تضمين الملفات واجتياز المسارات نهجاً قائماً على مبدأ الدفاع المتعمق (Defense-in-Depth) يجمع بين التحقق الصارم من المدخلات على مستوى الكود، والتهيئة الآمنة لبيئة التشغيل، والعزل المحكم للملفات.

1. القوائم البيضاء الصارمة (Allowlisting / Whitelisting):
   لا تقم أبداً بتمرير مدخلات المستخدم مباشرة إلى دوال التضمين أو دوال نظام الملفات. بدلاً من ذلك، اربط خيارات المستخدم المسموح بها بمسارات ملفات ثابتة ومحددة مسبقاً في مصفوفة:
   <?php
       $languages = [
           'en' => 'languages/en.php',
           'es' => 'languages/es.php',
           'fr' => 'languages/fr.php'
       ];
       $lang = $_GET['lang'] ?? 'en';
       if (array_key_exists($lang, $languages)) {
           include($languages[$lang]);
       } else {
           include('languages/en.php');
       }
   ?>

2. تحصين إعدادات ملف php.ini:
   - تعطيل التضمين البعيد نهائياً: `allow_url_include = Off`
   - تعطيل فتح الروابط عبر دوال الملفات إذا لم تكن مطلوبة: `allow_url_fopen = Off`
   - إخفاء رسائل الأخطاء في بيئات الإنتاج: `display_errors = Off`
   - تفعيل تسجيل الأخطاء في سجل داخلي آمن: `log_errors = On`

3. دوال تنقية وفحص المسارات:
   - استخدام دالة `basename()` لتجريد وتطهير أي تتابعات اجتياز للمجلدات (`../` أو `..\`):
     $file = basename($_GET['file']);
   - استخدام دالة `realpath()` بالتكامل مع فحص البادئة للتأكد من أن المسار النهائي يقع حصراً داخل المجلد المصرح به.

السؤال:
- يمكنني الآن استغلال وتحليل ثغرات LFI بنجاح!
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. The Secure Path Resolution Blueprint
```python
# Secure Canonical Path Validation (Python Example)
import os

BASE_DIR = "/var/www/app/uploads"

def get_secure_file(user_input):
    # Resolve the absolute canonical path
    resolved_path = os.path.realpath(os.path.join(BASE_DIR, user_input))
    # Enforce that resolved path starts strictly with BASE_DIR
    if not resolved_path.startswith(BASE_DIR):
        raise PermissionError("Access Denied: Path Traversal Detected!")
    return open(resolved_path, "rb").read()
```

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):** المهمة الختامية تلخص استراتيجيات الدفاع ولا تتطلب إدخال نصوص أو حل ألغاز.
>
> **المستوى الثاني (توجيهي):** اضغط على زر `Completed` لإنهاء الغرفة والانتقال بنجاح للغرفة التالية.
>
> **المستوى الثالث (الحل المباشر):** لا توجد إجابة نصية مطلوبة (`No answer needed`).

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Defense-in-Depth Checklist for File Inclusion
1. **الكود البرمجي (Application Layer):**
   - الاعتماد الحصري على Allowlist Mapping.
   - التحقق من الامتدادات عبر قوائم بيضاء صارمة.
2. **الخادم ومحرك التشغيل (Runtime Layer):**
   - تشغيل خادم الويب بمستخدم محدود الصلاحيات (`www-data`) لا يملك صلاحية القراءة على مجلدات حساسة كـ `/etc/shadow` أو مجلدات مستخدمي النظام الآخرين.
   - استخدام تقنيات العزل (Chroot Jails, Docker Containers, AppArmor, SELinux) لحصر وصول التطبيق في نطاق ضيق جداً.
3. **طبقة الحماية الشبكية (WAF Layer):**
   - قواعد ModSecurity / Cloudflare WAF لحظر أنماط `../` وتشفيراتها في ترويسات HTTP ومعلمات الاستعلام.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال / المتطلب | الإجابة النموذجية |
| :--- | :--- |
| **I can now exploit LFI vulnerabilities!** | `No answer needed` (تم إكمال الغرفة بنجاح) |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من استيعاب معايير التحصين وإنهاء الغرفة بنسبة 100% على منصة TryHackMe.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **القوائم البيضاء هي الحل الوحيد غير القابل للاختراق:** محاولات استبدال أو تنقية الرموز المشبوهة (Blacklisting كـ `str_replace`) تفشل دائماً أمام تقنيات الالتفاف، بينما القوائم البيضاء الصارمة (Allowlisting) تحسم المشكلة جذرياً.
2. **الانتقال المنطقي التالي:** ننتقل في الغرفة القادمة إلى **Command Injection (حقن أوامر نظام التشغيل)** لنرى كيف يمكن للمهاجم إجبار الخادم على تنفيذ أوامر الشل مباشرة عبر استغلال دوال استدعاء النظام غير الآمنة.
