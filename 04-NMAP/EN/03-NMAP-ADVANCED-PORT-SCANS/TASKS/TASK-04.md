# المهمة 04: فحوصات ACK و Window والفحوصات المخصصة (Task 04: TCP ACK, Window, and Custom Scan)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة مجموعة من الفحوصات المتخصصة في كشف وتحليل قواعد الجدران النارية وهندسة الحزم المخصصة:
1. **فحص تأكيد الاستلام (TCP ACK Scan `-sA`):**
   * إرسال حزم تحمل راية `ACK` وحدها.
   * لا يهدف الفحص لمعرفة هل المنفذ مفتوح أم مغلق، بل يهدف إلى **رسم خريطة قواعد الجدار الناري (Firewall Rule Mapping)** واكتشاف المنافذ المسموح بالمرور إليها عبر الجدران النارية غير المعتمدة على الحالة (Stateless Firewalls).
2. **فحص نافذة بروتوكول TCP (TCP Window Scan `-sW`):**
   * يرسل نفس الحزمة (راية `ACK` واحدة)، ولكنه يستغل شذوذاً في بعض الأنظمة القديمة عبر فحص قيمة حقل **TCP Window** في حزمة `RST` المستلمة (إذا كانت النافذة موجبة يُصنف كـ Open، وإذا كانت صفر يُصنف كـ Closed).
3. **الفحص المخصص بالرايات اليدوية (Custom Scan `--scanflags`):**
   * يتيح للمختبر دمج أي تشكيلة من الرايات بحرية تامة مثل `--scanflags RST` أو `--scanflags SYNFIN`.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### تحليل ميكانيكية فحص ACK ورسم قواعد الجدران النارية:
* وفقاً لمعيار RFC 793، فإن أي مكدس TCP يستلم حزمة `ACK` لاتصال غير مسجل لديه يجب أن يرد بـ **`RST`**.
* ماذا يحدث عندما يفحص Nmap خادماً محمياً بجدار ناري؟
  * إذا كان الجدار الناري يحجب المنفذ (Blocked) $\rightarrow$ يسقط الجدار الحزمة (Timeout) أو يرد بخطأ ICMP $\rightarrow$ يصنف Nmap المنفذ كـ **`Filtered`**.
  * إذا كان الجدار الناري يسمح للحزم بالمرور (Allowed / Passed) $\rightarrow$ تصل الحزمة للخادم ويرد بـ `RST` $\rightarrow$ يصنف Nmap المنفذ كـ **`Unfiltered`** (أي لا يوجد جدار ناري يعترضه).

#### مقارنة نتائج المعمل واكتشاف المنفذ الخفي:
* في المهمة السابقة (Task 2)، أظهر فحص المنافذ 4 خدمات رئيسية تقليدية (21, 22, 53, 80).
* عند تفعيل الجدار الناري وتنفيذ فحص ACK Scan:
  `sudo nmap -sA MACHINE_IP`
  تظهر **`5`** منافذ في حالة **`unfiltered`**.
* بمقارنة المنافذ الخمسة بالمنافذ الأربعة السابقة، نجد ظهور منفذ جديد يسمح الجدار الناري بمرور الحزم إليه وهو المنفذ **`443`** (HTTPS).
* عند فحص هذا المنفذ بـ SYN Scan (`sudo nmap -sS -p 443 MACHINE_IP`)، يؤكد استجابة الخادم بـ `SYN-ACK` وجود خدمة نشطة بالفعل خلفه (**`yea`**).

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* كم راية مستخدمة في فحص TCP Window؟ راية واحدة فقط وهي ACK ($1$).
* لتحديد راية Reset في خيار `--scanflags`، اكتب اسم الراية بالأحرف الكبيرة: `RST`.
* راجع قائمة المنافذ المفتوحة السابقة (21, 22, 53, 80) وقارنها بالمنفذ الخامس الذي ظهر في فحص ACK وهو منفذ الويب المشفر الشهير 443.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ فحص ACK واكتشاف المنافذ غير المفلترة:
```bash
# تنفيذ فحص ACK على الهدف لرسم الجدار الناري
sudo nmap -sA MACHINE_IP
```
مخرجات الفحص تُظهر:
```text
PORT    STATE      SERVICE
21/tcp  unfiltered ftp
22/tcp  unfiltered ssh
53/tcp  unfiltered domain
80/tcp  unfiltered http
443/tcp unfiltered https
```
عدد المنافذ الظاهرة كـ unfiltered هو **`5`** منافذ، والمنفذ الجديد المكتشف هو **`443`**.

#### التحقق من وجود خدمة حقيقية على منفذ 443:
```bash
sudo nmap -sS -p 443 MACHINE_IP
# PORT    STATE SERVICE
# 443/tcp open  https
```
يؤكد وجود خادم ويب آمن نشط بالفعل (**`yea`**).

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** In TCP Window scan, how many flags are set?
   * **الإجابة:** `1`
2. **السؤال 2:** You decided to experiment with a custom TCP scan that has the reset flag set. What would you add after --scanflags?
   * **الإجابة:** `RST`
3. **السؤال 3:** Launch an ACK scan against the target VM with the firewall enabled. How many ports appear unfiltered?
   * **الإجابة:** `5`
4. **السؤال 4:** What is the new port number that appeared? To determine the new port you need to compare the scan results of Task 2 to the ones of this task.
   * **الإجابة:** `443`
5. **السؤال 5:** Is there any service behind the newly discovered port number? (yea/nay)
   * **الإجابة:** `yea`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استجابة المنفذ 443 بـ RST في فحص ACK ثم بـ SYN-ACK في فحص SYN أثبتت وجود الخدمة بدقة.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* فحص ACK Scan هو الأداة المثالية لتقييم قواعد الجدران النارية وتحديد ما إذا كان الجدار الناري معتمداً على الحالة (Stateful) أو غير معتمد عليها (Stateless)، وتحديد المنافذ المسموح بمرور الحزم من خلالها.
