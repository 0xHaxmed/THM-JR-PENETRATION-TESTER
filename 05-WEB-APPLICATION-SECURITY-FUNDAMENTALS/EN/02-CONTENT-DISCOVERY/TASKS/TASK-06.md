# المهمة 06: الاستكشاف الآلي - أساسيات أداة Gobuster (Task 06: Automated Discovery - Gobuster Fundamentals)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة تقنية **التخمين الآلي للمسارات (Automated Directory Brute-Forcing)** باستخدام الأداة القياسية **Gobuster** المطورة بلغة Go لسرعتها الفائقة وكفاءتها في إرسال آلاف الطلبات في ثوانٍ معدودة.

يقوم وضع فحص المسارات في Gobuster (`gobuster dir`) بإرسال طلبات HTTP متتالية لمسارات مأخوذة من قواميس مخصصة (Wordlists مثل مستودع `SecLists`) ومراقبة كود استجابة الخادم:
* كود `200 OK`: الصفحة أو الملف موجود ومتاح للقراءة.
* كود `301 / 302 Redirect`: المسار يمثل مجلداً حقيقياً يُعيد التوجيه لمسار مائل `/`.
* كود `401 / 403 Forbidden`: المسار موجود لكنه محمي بصلاحيات وصول.
* كود `404 Not Found`: المسار غير موجود.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### تشريح معاملات أمر `gobuster dir`:
* `-u`: تحديد عنوان URL المستهدف (مثل `http://MACHINE_IP/`).
* `-w`: مسار القاموس المستخدم (مثل `/usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt`).
* `-x`: تحديد الامتدادات المطلوب إلحاقها بكل كلمة (مثل `-x php,txt,html,log`).
* `-t`: عدد مسارات المعالجة المتزامنة (Threads - الافتراضي 10).
* `-o`: حفظ النتائج في ملف خارجي.

#### تحليل نتائج فحص المعمل:
* عند تشغيل Gobuster ضد موقع الآلة المعملية باستخدام قاموس `common.txt` وامتدادات الملفات:
  1. يكتشف Gobuster مجلداً يبدأ بالحرفين `/mo` وهو مجلد: **`/monthly`** (بكود حالة 301).
  2. يكتشف Gobuster ملف سجلات حساساً بامتداد `.log` وهو ملف: **`/development.log`** (بكود حالة 200).

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* نفّذ الأمر: `gobuster dir -u http://MACHINE_IP -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -x log,txt`.
* ابحث في النتائج عن المجلد المكتشف الذي يبدأ بـ `/mo`.
* ابحث عن الملف الذي ينتهي بامتداد `.log`.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ أمر Gobuster في الطرفية:
```bash
gobuster dir -u http://MACHINE_IP -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -x log,txt -t 20
```

#### مخرجات الفحص النموذجية المسجلة:
```text
===============================================================
Gobuster v3.X
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://MACHINE_IP
[+] Method:                  GET
[+] Threads:                 20
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] Extensions:              log,txt
===============================================================
/assets               (Status: 301) [Size: 178] [--> /assets/]
/contact              (Status: 200) [Size: 3108]
/customers            (Status: 301) [Size: 178] [--> /customers/]
/development.log      (Status: 200) [Size: 456]
/monthly              (Status: 301) [Size: 178] [--> /monthly/]
/news                 (Status: 200) [Size: 4522]
/robots.txt           (Status: 200) [Size: 46]
/sitemap.xml          (Status: 200) [Size: 320]
===============================================================
```

#### تحليل المخرجات:
1. المجلد الذي يبدأ بـ `/mo` هو **`/monthly`**.
2. ملف السجلات المكتشف هو **`/development.log`**.

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** What is the name of the directory beginning with /mo that was discovered?
   * **الإجابة:** `/monthly`
2. **السؤال 2:** What is the name of the log file that was discovered?
   * **الإجابة:** `/development.log`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استخراج المجلد `/monthly` والملف `/development.log` مباشرة من مخرجات أداة Gobuster.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* ملفات السجلات التنموية (مثل `development.log`) تمثل تسريباً معلوماتياً خطيراً يكشف مسارات قواعد البيانات ومفاتيح التشفير ورسائل تتبع الأخطاء البرمجية (Stack Traces)، ويجب دائماً منع الوصول إليها أو نقلها خارج بيئات الإنتاج.
