# المهمة 04: فحص الاتصال الكامل عبر بروتوكول TCP (Task 04: TCP Connect Scan)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة تقنية **فحص الاتصال الكامل عبر TCP (TCP Connect Scan)** والتي تُفعّل في Nmap عبر المعامل **`-sT`**.

يعتبر هذا الفحص هو الوضع الافتراضي لـ Nmap عندما يقوم مستخدم عادي (بدون صلاحيات `sudo` أو `root`) بفحص شبكة معينة. يعتمد الفحص كلياً على استدعاء دالة النواة القياسية `connect()` لإتمام المصافحة الثلاثية كاملة (Full Three-Way Handshake) مع كل منفذ مستهدف:
1. يرسل Nmap حزمة `SYN`.
2. إذا كان المنفذ مفتوحاً، يرد الهدف بـ `SYN, ACK`.
3. يكمل نظام تشغيل الفاحص المصافحة بإرسال `ACK` لتصبح الجلسة `ESTABLISHED`.
4. بمجرد تأكد Nmap من فتح المنفذ، يرسل فوراً حزمة `RST, ACK` لقطع الاتصال حتى لا يترك الجلسة معلقة.

إذا كان المنفذ مغلقاً، يرد الهدف بـ `RST, ACK`، ويصنف Nmap المنفذ كـ `closed`.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### المزايا والعيوب التكتيكية لـ TCP Connect Scan (`-sT`):
* **الميزة الأساسية:**
  * لا يتطلب أي صلاحيات خاصة (`root`); لأنه يعتمد على استدعاءات مآخذ النظام العادية المتاحة لأي تطبيق.
* **العيوب والمخاطر في اختبارات الاختراق الحقيقية:**
  1. **الضجيج العالي وتسجيل الحوادث في سجلات الخوادم (Noisy & Heavily Logged):**
     * نظراً لأن الاتصال يكتمل (Full Handshake)، تقوم معظم الخدمات التطبيقية (مثل خوادم الويب Apache/Nginx وخوادم الـ FTP و SSH) بتسجيل حدث الاتصال كاملاً في سجلات الوصول (`access.log` أو `auth.log`).
     * يظهر في السجلات عنوان IP للمختبر متصلاً ومنقطعاً فجأة في جزء من الثانية عبر مئات المنافذ، مما يؤدي إلى رصده فوراً عبر أنظمة SIEM و IDS/IPS وحظره بـ Fail2ban.
  2. **البطء النسبي:**
     * استهلاك وقت وموارد إضافية في إرسال 3 إلى 4 حزم لكل منفذ مقارنة بالفحص نصف المفتوح (SYN Scan).

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* نفّذ الأمر `nmap -sT MACHINE_IP` ولاحظ مخرجات الفحص لجدول المنافذ.
* راجع عمود `STATE` للمنفذ 21 وعمود `SERVICE` للمنفذ 53.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ الفحص الميداني عبر محطة الهجوم:
```bash
# تنفيذ فحص TCP Connect على الآلة المعملية الهدف
nmap -sT MACHINE_IP
```

#### مخرجات الفحص المسجلة في المعمل:
```text
Starting Nmap 7.9X ( https://nmap.org )
Nmap scan report for MACHINE_IP
Host is up (0.00045s latency).
Not shown: 996 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
53/tcp open  domain
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.18 seconds
```

#### تحليل المخرجات:
1. المنفذ `21/tcp` حالته **`open`** ويشغل خدمة نقل الملفات (ftp).
2. المنفذ `53/tcp` حالته `open` وتخمين Nmap للخدمة العاملة عليه وفقاً لملف `/etc/services` هو **`domain`** (DNS).

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** What is the state of the FTP service running on port 21?
   * **الإجابة:** `open`
2. **السؤال 2:** What is Nmap’s guess about the service running on port 53?
   * **الإجابة:** `domain`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* مطابقة جدول مخرجات الفحص الفعلي للآلة المعملية.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* على الرغم من أن فحص `-sT` هو الخيار الأسهل لعدم حاجته لصلاحيات `root`، إلا أنه يجب تجنبه في عمليات الفريق الأحمر (Red Teaming) والتخفي الصارم بسبب تركه لآثار واضحة في سجلات التطبيقات للأنظمة المستهدفة.
