# المهمة 02: كشف الخدمات وتحديد الإصدارات (Task 02: Service Detection)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة ميزة **كشف الخدمات وإصداراتها (Service & Version Detection)** في أداة Nmap عبر المعامل **`-sV`**.

بشكل افتراضي، عندما يكتشف Nmap منفذاً مفتوحاً، فإنه يخمن اسم الخدمة استناداً إلى جدول أرقام المنافذ القياسية في ملف `/etc/services` (مثل تخمين أن المنفذ 80 هو HTTP أو المنفذ 22 هو SSH). ولكن في البيئات الحقيقية، قد يقوم مسؤولو الأنظمة بتشغيل خادم SSH على المنفذ 80 أو تشغيل خادم ويب على المنفذ 8080 أو 1337 للتمويه.

عند استخدام المعامل **`-sV`**:
1. يقوم Nmap بالاتصال بالمنفذ المفتوح وتأسيس مصافحة ثلاثية كاملة.
2. يستمع للرسالة الترحيبية الأولى (Banner Grabbing) إذا كانت الخدمة تبادر بالإرسال (مثل SSH أو FTP).
3. إذا لم ترسل الخدمة أي رد، يرسل Nmap سلسلة من الحزم الاستعلامية المصاغة بذكاء (Probes) والمخزنة في ملف `nmap-service-probes`.
4. يحلل الردود الواردة ويقارنها مع آلاف التعابير النمطية (Regular Expressions) لاستخراج:
   * اسم البرنامج (Service Name / Product).
   * رقم الإصدار الدقيق (Version).
   * معلومات إضافية (مثل نظام التشغيل أو اسم المضيف).

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### مستويات شدة الفحص (Version Intensity Levels):
* يوفر Nmap مستويات متعددة للتحكم في عمق فحص الخدمات تتراوح من $0$ إلى $9$:
  * **الوضع الافتراضي:** مستوى الشدة 7.
  * **الفحص الخفيف (`--version-light`):** يضبط الشدة على المستوى 2؛ حيث يكتفي بإرسال الاستعلامات الأكثر شيوعاً وسرعة فقط.
  * **الفحص الشامل (`--version-all`):** يضبط الشدة على المستوى 9؛ حيث يجرب كافة الاستعلامات المتاحة في قاعدة البيانات بلا استثناء.

#### تحليل نتائج المعمل:
1. **المنفذ 143 (IMAP):**
   * عند تشغيل `sudo nmap -sV -p 143 MACHINE_IP`، يتصل Nmap بخادم البريد ويستخرج الإصدار:
     `143/tcp open  imap  Dovecot imapd`
   * الإصدار المكتشف هو: **`Dovecot imapd`**.
2. **الفحص باستخدام `--version-light`:**
   * عند تشغيل `sudo nmap -sV --version-light MACHINE_IP`، يقلل Nmap عدد الاستعلامات لتوفير الوقت.
   * نتيجة لذلك، تعجز الأداة عن تحديد إصدار خدمة ربط الإجراءات البعيدة **`rpcbind`** (العاملة على منفذ 111)، وتكتفي بإظهار اسم الخدمة دون الإصدار.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* نفّذ فحص الخدمات القياسي: `sudo nmap -sV MACHINE_IP` ولاحظ عمود VERSION المقابل للمنفذ 143.
* أعد تشغيل الفحص بالمعامل الخفيف: `sudo nmap -sV --version-light MACHINE_IP` وقارن عمود VERSION لمعرفة الخدمة التي اختفى إصدارها.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ فحص الخدمات القياسي:
```bash
sudo nmap -sV MACHINE_IP
```
مخرجات الفحص المسجلة:
```text
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1
80/tcp  open  http    nginx 1.18.0
111/tcp open  rpcbind 2-4 (RPC #100000)
143/tcp open  imap    Dovecot imapd
```
الإصدار المحدد على المنفذ 143 هو **`Dovecot imapd`**.

#### تنفيذ فحص الخدمات الخفيف (`--version-light`):
```bash
sudo nmap -sV --version-light MACHINE_IP
```
مخرجات الفحص:
```text
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1
80/tcp  open  http    nginx 1.18.0
111/tcp open  rpcbind
143/tcp open  imap    Dovecot imapd
```
يظهر منفذ 111 للخدمة **`rpcbind`** دون أي تفاصيل للإصدار في عمود VERSION.

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** What is the detected version for port 143?
   * **الإجابة:** `Dovecot imapd`
2. **السؤال 2:** Which service did not have a version detected with --version-light ?
   * **الإجابة:** `rpcbind`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* مطابقة مخرجات فحص الخدمات مع جدول النتائج الفعلي للآلة المعملية.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* يساعد المعامل `-sV` مختبر الاختراق في تجنب الفخاخ الخداعية (Decoy Ports) والخدمات العاملة على منافذ غير قياسية؛ كما أن الموازنة بين `--version-light` للسرعة و `--version-all` للشمولية مهارة تكتيكية حاسمة في تقييم البنية التحتية.
