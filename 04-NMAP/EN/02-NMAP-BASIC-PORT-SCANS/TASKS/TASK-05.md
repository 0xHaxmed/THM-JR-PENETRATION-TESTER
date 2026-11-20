# المهمة 05: فحص التخفي نصف المفتوح (Task 05: TCP SYN Scan)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة تقنية **فحص التخفي نصف المفتوح (TCP SYN Scan / Stealth Scan)** والتي تُفعّل في Nmap عبر المعامل **`-sS`**.

يمثل هذا الفحص الوضع الافتراضي لـ Nmap عندما يتم تشغيله بصلاحيات مميزة (`sudo` أو `root`). تعتمد هذه التقنية على كسر قواعد المصافحة الثلاثية عمداً:
1. يرسل Nmap حزمة `SYN` أولى إلى المنفذ الهدف.
2. إذا كان المنفذ مفتوحاً، يرد خادم الهدف بحزمة `SYN, ACK`.
3. بمجرد أن يستلم Nmap حزمة `SYN, ACK`، يتأكد أن المنفذ مفتوح، ولكنه **لا يكمل المصافحة الثلاثية ولا يرسل حزمة ACK نهائية**؛ بل يرسل بدلاً منها فوراً حزمة **`RST` (Reset)** لإلغاء الجلسة في مهدها (Half-Open Connection).

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### لماذا يُسمى هذا الفحص بفحص التخفي (Stealth Scan)؟
* في المعمارية الكلاسيكية لأنظمة التشغيل، لا تقوم الخدمات التطبيقية (مثل خادم الويب أو خادم البريد) بتسجيل أي محاولة اتصال في ملفات السجلات (`access.log`) إلا بعد أن تكتمل المصافحة الثلاثية وتنتقل الجلسة إلى حالة `ESTABLISHED` وتسلمها النواة للتطبيق عبر دالة `accept()`.
* ونظراً لأن حزمة `RST` تقتل الاتصال وهو ما يزال في حالة "نصف مفتوح" (SYN-RECEIVED) على مستوى النواة، فإن التطبيق لا يشعر بالاتصال مطلقاً ولا يسجله في ملفات سجلات الخدمة!
* *(ملاحظة أمنية معاصرة: ترصد الجدران النارية الحديثة وأجهزة كشف التسلل IDS/IPS مثل Snort و Suricata حزم SYN Scans بسهولة لأن نمط إرسال SYN متبوعاً بـ RST يُعد توقيعاً شهيراً للفحص الأمني).*

#### تحليل نتائج المعمل والتقاط الحزم:
* في الآلة المعملية الهدف، المنافذ المفتوحة هي: المنفذ 21 (FTP)، 22 (SSH)، 53 (DNS)، و 80 (HTTP).
* لذلك، عندما يقوم Nmap بإرسال حزم SYN إلى جميع المنافذ:
  * المنافذ المغلقة ترد بحزم `RST`.
  * المنافذ الأربعة المفتوحة فقط هي التي تستجيب بحزم `SYN, ACK`.
  * إجمالي حزم `SYN, ACK` المستلمة في AttackBox = **`4`**.
  * إجمالي المنافذ المفتوحة = **`4`**.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* نفّذ الأمر بصلاحيات الجذر: `sudo nmap -sS MACHINE_IP`.
* يمكنك استخدام أداة `tcpdump` لمراقبة حزم `[S.]` (التي تمثل SYN-ACK) الواردة إلى محطتك وحساب عددها.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ فحص TCP SYN ومراقبة الحزم:
```bash
# تنفيذ فحص SYN السريع بصلاحيات root
sudo nmap -sS -F MACHINE_IP
```

#### مخرجات الفحص المسجلة في المعمل:
```text
Starting Nmap 7.9X ( https://nmap.org )
Nmap scan report for MACHINE_IP
Host is up (0.00038s latency).
Not shown: 96 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
53/tcp open  domain
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.12 seconds
```

#### تتبع الحزم عبر tcpdump (Counting SYN-ACK Packets):
```bash
# فلترة الحزم الواردة الحاملة لرايتي SYN و ACK معاً
sudo tcpdump -i eth0 'tcp[13] = 18' and src host MACHINE_IP
```
يُظهر التقاط الحزم استقبال 4 حزم `SYN-ACK` بدقة قادمة من المنافذ: 21, 22, 53, 80.

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** After launching a TCP SYN scan, how many SYN-ACK packets are successfully received in AttackBox?
   * **الإجابة:** `4`
2. **السؤال 2:** How many ports are open on the target machine?
   * **الإجابة:** `4`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استلام 4 حزم SYN-ACK يطابق عدد المنافذ الأربعة المفتوحة (21, 22, 53, 80).
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* يُعد فحص TCP SYN (`-sS`) المعيار الذهبي المفضل لمختبري الاختراق؛ فهو أسرع بكثير من Connect Scan، يقلل من تشويش سجلات التطبيقات، ويمنح دقة بالغة في تحديد المنافذ المفتوحة.
