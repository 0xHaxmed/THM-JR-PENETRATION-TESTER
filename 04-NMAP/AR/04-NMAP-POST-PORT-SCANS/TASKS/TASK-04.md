# المهمة 04: محرك سكربتات Nmap البرمجية (Task 04: Nmap Scripting Engine - NSE)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة واحدة من أقوى ميزات أداة Nmap على الإطلاق: **محرك البرمجة النصية Nmap Scripting Engine (NSE)**.

يتيح محرك NSE للمستخدمين والباحثين كتابة وأتمتة مجموعة واسعة من المهام الأمنية المعقدة باستخدام لغة البرمجة الخفيفة **Lua**. تخزن جميع سكربتات Nmap في المسار القياسي بأنظمة لينكس:
`/usr/share/nmap/scripts/`
ويتم فهرستها في ملف قاعدة البيانات:
`/usr/share/nmap/scripts/script.db`

تنقسم سكربتات NSE إلى 14 تصنيفاً رئيسياً، أشهرها:
* `default` (أو `-sC`): السكربتات الافتراضية السريعة والآمنة التي تجمع معلومات أساسية.
* `discovery`: استكشاف معلومات إضافية عن النظام والخدمات (مثل قراءة عناوين الويب وعناوين NetBIOS).
* `vuln`: فحص واكتشاف الثغرات الأمنية المعروفة (CVEs).
* `auth`: فحص آليات التوثيق وبيانات الاعتماد الافتراضية.
* `safe`: سكربتات آمنة تماماً ولا تسبب أي انهيار للخدمات المستهدفة في بيئات الإنتاج.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### السؤال 1: وظيفة سكربت `http-robots.txt`:
* عند فحص توثيق السكربت الداخلي عبر الأمر `nmap --script-help http-robots.txt`:
  > "Checks for disallowed entries in robots.txt on a web server."
* وظيفة السكربت هي قراءة ملف `robots.txt` واستخراج المسارات الممنوعة على محركات البحث (**`disallowed entries`**)، والتي عادة ما تخفي مسارات حساسة مثل لوحات التحكم والمجلدات الإدارية.

#### السؤال 2: اسم السكربت المسؤول عن ثغرة MS15-034 (CVE-2015-1635):
* ثغرة MS15-034 هي ثغرة تنفيذ أوامر عن بعد (RCE) شهيرة في برنامج تشغيل HTTP بميكروسوفت IIS (`HTTP.sys`).
* بالبحث في قاعدة بيانات سكربتات Nmap باستخدام `grep -i "cve-2015-1635" /usr/share/nmap/scripts/script.db`، نجد أن السكربت المخصص لها يسمى:
  **`http-vuln-cve2015-1635`**.

#### السؤال 3: عنوان الصفحة المستخرج بـ `http-title` على منفذ 80:
* عند تشغيل السكربتات الافتراضية `nmap -sC -p 80 MACHINE_IP`، ينفذ Nmap سكربت `http-title` الذي يرسل طلب GET ويستخرج وسم `<title>` من صفحة الويب الرئيسية.
* العنوان الظاهر في صفحة الخادم الافتراضية هو:
  **`Welcome to nginx on Debian!`**.

#### السؤال 4: خوارزمية مفتاح خادم SSH المعتمدة على SHA2-512:
* عند فحص منفذ SSH (22) باستخدام سكربت تعداد الخوارزميات:
  `nmap --script ssh2-enum-algos -p 22 MACHINE_IP`
* يستعرض السكربت قائمة خوارزميات المفاتيح المدعومة في خادم الـ SSH (`server_host_key_algorithms`).
* الخوارزمية المعتمدة على خوارزمية التجزئة SHA2-512 هي:
  **`rsa-sha2-512`**.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* ابحث في توثيق `http-robots.txt` عن الكلمتين الموصفتين للمسارات المحظورة.
* ابحث في مجلد السكربتات عن اسم معرف الثغرة `cve2015-1635`.
* نفّذ الأمر `nmap -sC MACHINE_IP` ولاحظ مخرجات `http-title` أسفل المنفذ 80.
* نفّذ الأمر `nmap --script ssh2-enum-algos -p 22 MACHINE_IP` واستخرج الخوارزمية التي تنتهي بـ `sha2-512`.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ أوامر الفحص والبحث في السكربتات:
```bash
# 1. الاستعلام عن سكربت http-robots.txt
nmap --script-help http-robots.txt | grep -i "checks for"
# Description: Checks for disallowed entries in robots.txt on a web server.

# 2. البحث عن سكربت ثغرة MS15-034
ls /usr/share/nmap/scripts/ | grep -i "cve2015-1635"
# الناتج: http-vuln-cve2015-1635.nse

# 3. تشغيل السكربتات الافتراضية على منفذ الويب
nmap -sC -p 80 MACHINE_IP
# مخرجات السكربت:
# 80/tcp open  http
# |_http-title: Welcome to nginx on Debian!

# 4. تشغيل سكربت تعداد خوارزميات SSH
nmap --script ssh2-enum-algos -p 22 MACHINE_IP
# مخرجات السكربت:
# | ssh2-enum-algos:
# |   server_host_key_algorithms: (5)
# |     rsa-sha2-512
# |     rsa-sha2-256
# |     ssh-rsa
# |     ecdsa-sha2-nistp256
# |     ssh-ed25519
```

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** Knowing that Nmap scripts are saved in /usr/share/nmap/scripts on the AttackBox. What does the script http-robots.txt check for?
   * **الإجابة:** `disallowed entries`
2. **السؤال 2:** Can you figure out the name for the script that checks for the remote code execution vulnerability MS15-034 (CVE-2015-1635)?
   * **الإجابة:** `http-vuln-cve2015-1635`
3. **السؤال 3:** On the AttackBox, run Nmap with the default scripts -sC against MACHINE_IP . You will notice that a page is hosted on port 80. What is the http-title value?
   * **الإجابة:** `Welcome to nginx on Debian!`
4. **السؤال 4:** Based on its description, the script ssh2-enum-algos “reports the number of algorithms (for encryption, compression, etc.) that the target SSH2 server offers.” What is the name of the server host key algorithm that relies on SHA2-512 and is supported by MACHINE_IP ?
   * **الإجابة:** `rsa-sha2-512`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استخراج عنوان الصفحة وخوارزميات SSH مباشرة من نتائج تنفيذ السكربتات على الآلة المعملية.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* محرك NSE ينقل Nmap من مجرد ماسح منافذ إلى منصة تدقيق أمني متكاملة (Vulnerability Scanner) قادرة على اكتشاف الثغرات وتدقيق الشهادات والتشفير وسحب البيانات الحساسة بلمسة زر واحدة.
