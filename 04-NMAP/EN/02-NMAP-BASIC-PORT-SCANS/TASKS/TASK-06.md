# المهمة 06: فحص بروتوكول بيانات المستخدم (Task 06: UDP Scan)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستكشف هذه المهمة تقنية **فحص بروتوكول UDP (UDP Port Scan)** والتي تُفعّل في Nmap عبر المعامل **`-sU`**.

على النقيض من بروتوكول TCP المعتمد على الاتصال والمصافحة، فإن بروتوكول **UDP (User Datagram Protocol)** هو بروتوكول عديم الاتصال (Connectionless / Stateless)؛ لا توجد فيه رايات تحكم (SYN, ACK, RST) ولا يتم فتح جلسات أو إغلاقها.

لذلك، يعتمد Nmap في فحص منافذ UDP على سلوك مكدس بروتوكول الإنترنت (IP Stack) الخاص بالنواة:
1. يرسل Nmap حزمة UDP فارغة (أو مصاغة خصيصاً للخدمة كطلب DNS استعلامي).
2. **إذا كان المنفذ مغلقاً (Closed Port):** يُجبر مكدس الهدف على توليد رسالة خطأ ICMP:
   $$\text{ICMP Type 3, Code 3: Destination Unreachable (Port Unreachable)}$$
3. **إذا كان المنفذ مفتوحاً وتطبيق الخدمة يستمع (Open Port):** قد يستجيب التطبيق بحزمة UDP تحمل رداً، أو يتجاهل الحزمة بصمت.
4. **حالة `Open|Filtered`:** إذا لم يستلم Nmap أي رد على الإطلاق، ولم يستلم خطأ ICMP، فإنه يعجز عن الجزم هل المنفذ مفتوح ويتجاهل الحزمة أم أن جدار الحماية أسقطها، فيصنف المنفذ كـ `open|filtered`.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### لماذا يعتبر فحص UDP بطيئاً جداً في بيئات العمل الحقيقية؟
* تنص معايير شبكات الإنترنت (RFC 1812) على ضرورة قيام أنظمة التشغيل بـ **تقييد معدل إرسال رسائل أخطاء ICMP (ICMP Rate Limiting)** لمنع هجمات حجب الخدمة (DoS).
* على سبيل المثال، يحدد نظام لينكس في ملف `/proc/sys/net/ipv4/icmp_ratelimit` إرسال رسالة ICMP واحدة فقط كل ثانية (1 packet/sec) كحد أقصى للأخطاء.
* فإذا قمت بفحص 1,000 منفذ UDP على خادم لينكس وكانت معظمها مغلقة، فسيحتاج الخادم إلى 1,000 ثانية (أكثر من 16 دقيقة) فقط لإرجاع رسائل Port Unreachable!
* **الحل التكتيكي للمختبر:** حصر فحص UDP على أهم 20 أو 50 منفذاً شائعاً فقط (`nmap -sU --top-ports 20 TARGET`).

#### تحليل مخرجات المعمل للمنفذ 161:
* يُظهر جدول فحص الـ UDP في الآلة المعملية استجابة المنفذ 161 برسالة `ICMP Type 3 Code 3`، وبالتالي تصنيفه كـ **`closed`**.
* الخدمة المعيارية المرتبطة بمنفذ 161 وفقاً لقاموس Nmap هي خدمة إدارة الشبكات البسيطة **`snmp` (Simple Network Management Protocol)**.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* نفّذ الأمر `sudo nmap -sU -F MACHINE_IP` لمسح أشهر 100 منفذ UDP سريعاً.
* ابحث عن السطر الخاص بالمنفذ `161/udp` في جدول النتائج ولاحظ عمودي الحالة والخدمة.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ فحص UDP في المعمل:
```bash
# تنفيذ فحص UDP سريع على الآلة المعملية
sudo nmap -sU -F MACHINE_IP
```

#### مخرجات الفحص المسجلة:
```text
PORT     STATE  SERVICE
53/udp   open   domain
67/udp   closed dhcps
123/udp  closed ntp
135/udp  closed msrpc
137/udp  closed netbios-ns
138/udp  closed netbios-dgm
161/udp  closed snmp
445/udp  closed microsoft-ds
631/udp  closed ipp
1434/udp closed ms-sql-m
```

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** What is the state of port number 161 over UDP in the target machine?
   * **الإجابة:** `closed`
2. **السؤال 2:** What is the service name according to Nmap on port 161?
   * **الإجابة:** `snmp`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استخراج اسم خدمة SNMP وحالتها المغلقة مباشرة من مخرجات فحص UDP للآلة المعملية.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* لا تتجاهل خدمات UDP في اختبارات الاختراق؛ فثغرات خدمات مثل DNS (53), SNMP (161), NTP (123), و TFTP (69) غالباً ما تمنح وصولاً استخباراتياً بالغ الخطورة أو إمكانية تنفيذ هجمات تضخيم حجب الخدمة (Amplification DDoS).
