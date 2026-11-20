# المهمة 01: المقدمة (Task 01: Introduction)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تتناول هذه المهمة مدخل مرحلة **ما بعد فحص المنافذ (Nmap Post Port Scans)**. بعد أن يحدد مختبر الاختراق الأجهزة النشطة (Live Hosts) والمنافذ المفتوحة (Open Ports)، لا يكفي مجرد معرفة أن المنفذ 80 أو 22 مفتوح؛ بل تقتضي المنهجية الاحترافية تحديد:
1. **الخدمة الدقيقة ورقم الإصدار (Service & Version Detection `-sV`):** هل الخادم يشغل Apache 2.4.49 أم Nginx 1.18 أم IIS 10.0؟ تحديد الإصدار بدقة هو المدخل للبحث عن الثغرات المعروفة (CVEs) وثغرات الـ RCE.
2. **بصمة نظام التشغيل (OS Detection `-O`):** هل النظام الهدف هو Debian Linux أم Windows Server 2019 أم FreeBSD؟
3. **أتمتة الفحص الأمني عبر محرك السكربتات (Nmap Scripting Engine - NSE `-sC`):** فحص الإعدادات الخاطئة، وسحب العناوين الحساسة، واكتشاف الثغرات.
4. **تصدير وحفظ الأدلة والتقارير بمختلف الصيغ (`-oA`):** XML, Grepable, و Normal.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)
* **المسار الانتقالي في اختبار الاختراق (The Penetration Testing Pipeline):**
  $$\text{Host Discovery (-sn)} \longrightarrow \text{Port Scanning (-sS)} \longrightarrow \text{Service & OS Detection (-sV -O)} \longrightarrow \text{Vulnerability Auditing (--script)}$$
* مجرد فتح المنفذ لا يمنحك هدفاً واضحاً للاستغلال؛ معرفة أن المنفذ 143 يشغل خادم بريد `Dovecot imapd` يحدد على الفور بروتوكول الهجوم ونوع الاختبارات الأمنية اللازمة.
* فحص الخدمات (`-sV`) يتطلب بالضرورة **إكمال المصافحة الثلاثية كاملة (Full Handshake)** والتخاطب مع الخدمة ببروتوكول التطبيق وإرسال استعلامات مصاغة مسبقاً (Service Probes) لمقارنة الردود مع قاعدة بيانات `nmap-service-probes`.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* ابدأ تشغيل الآلة المعملية الهدف من خلال زر "Start Machine" وانتظر الحصول على عنوان IP.
* تأكد من جاهزية محطة الهجوم (AttackBox أو Kali Linux) لبدء إرسال الفحوصات المتقدمة.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)
تشغيل بيئة المعمل والتأكد من الاتصال بالهدف:
```bash
# التأكد من عمل الاتصال بالهدف
ping -c 2 MACHINE_IP
```

---

### 5. الإجابة النموذجية المعتمدة (Stage E: Final Answer)
* **السؤال:** I have started the machine successfully.
* **الإجابة:** `No answer needed`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* تشغيل الآلة المعملية وظهور الـ Target IP على المنصة.
* تأكيد اكتمال المهمة بنسبة 100% في سجلات المستخدم.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* معرفة رقم المنفذ هي نصف الحقيقة؛ معرفة اسم البرنامج وإصداره بدقة هو المفتاح الحقيقي الذي يفتح أبواب الثغرات والاستغلال (Exploitation).
