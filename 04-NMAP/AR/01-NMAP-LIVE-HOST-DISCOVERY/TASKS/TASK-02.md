# المهمة 02: الشبكات الفرعية (Task 02: Subnetworks)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة مبادئ تقسيم الشبكات (Subnetting) وكيف تؤثر حدود الشبكة الفرعية ومجال البث (Broadcast Domain) على بروتوكول استبانة العناوين **ARP (Address Resolution Protocol)**.

تحتوي الغرفة على محاكي شبكة تفاعلي (`View Site`) يوضح هيكلية شبكتين فرعيتين تفصل بينهما بوابة افتراضية (Router / Default Gateway):
* **الشبكة الفرعية الأولى (Subnet A):** تضم الأجهزة `computer1`, `computer2`, `computer3` بالإضافة إلى المنفذ الداخلي للموجه `router`.
* **الشبكة الفرعية الثانية (Subnet B):** تضم الأجهزة `computer4`, `computer5`, `computer6` بالإضافة إلى المنفذ الآخر للموجه `router`.

الهدف هو إرسال حزم ARP استعلامية تفاعلية وملاحظة أين تقف حزم البث العام (Broadcast) ولماذا لا تستطيع عبور الموجه (Router).

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)
* **المفهوم النظري لـ ARP:**
  * يعمل بروتوكول ARP في طبقة ربط البيانات (Data Link Layer / Layer 2).
  * عندما يحتاج جهاز لمعرفة عنوان الـ MAC لجهاز آخر على نفس الشبكة، يرسل طلب **ARP Request** بصيغة بث عام (Broadcast MAC: `ff:ff:ff:ff:ff:ff`).
  * تكمن القاعدة الشبكية الأساسية في: **"الموجهات (Routers) لا تعيد توجيه حزم البث العام (Routers do not forward Layer 2 broadcasts)"**.
* **التحليل التجريبي عبر المحاكي:**
  1. عندما يرسل `computer1` (في Subnet A) طلب ARP بحثاً عن `computer6` (الموجود في Subnet B):
     * تصل حزمة البث لجميع الأجهزة داخل نفس مجال البث (Subnet A): `computer1`, `computer2`, `computer3`, والـ `router`. المجموع = 4 أجهزة.
     * يتوقف البث عند الموجه ولا يعبر إلى Subnet B، وبالتالي **لا يستلم `computer6` الطلب مطلقاً (`nay`)**.
  2. عندما يرسل `computer4` (في Subnet B) طلب ARP بحثاً عن `computer6` (الموجود معه في نفس Subnet B):
     * تصل حزمة البث لجميع الأجهزة داخل نفس مجال البث (Subnet B): `computer4`, `computer5`, `computer6`, والـ `router`. المجموع = 4 أجهزة.
     * نظراً لأن `computer6` يقع في نفس مجال البث، فإنه يستلم الطلب ويقوم بالرد فوراً بـ **ARP Reply** (`yea`).

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* اضغط على زر `View Site` لتشغيل المحاكي الشبكي التفاعلي.
* تذكر أن مجالات البث (Broadcast Domains) محصورة داخل الشبكة الفرعية الواحدة، ولا يمكن لحزم ARP L2 تجاوز واجهة الموجه إلى شبكة فرعية أخرى.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### الخطوة 1: اختبار الاستعلام من الشبكة الفرعية الأولى A
* من واجهة المحاكي، قم بتعيين الخيارات:
  * **From:** `computer1`
  * **To:** `computer1` (بث عام Broadcast داخل الشبكة)
  * **Packet Type:** `ARP Request`
  * **Data:** `computer6`
* انقر **Send Packet**:
  * راقب تدفق الحزمة: تمر الحزمة عبر المحول (Switch) وتصل إلى: `computer1`, `computer2`, `computer3` ومنفذ الموجه `router` (إجمالي 4 أجهزة تشاهد الحزمة).
  * الحزمة تتلاشى عند الموجه، وبالتالي لم تصل إلى `computer6`.

#### الخطوة 2: اختبار الاستعلام من نفس الشبكة الفرعية B
* من واجهة المحاكي، قم بتعيين الخيارات:
  * **From:** `computer4`
  * **To:** `computer4` (بث عام Broadcast داخل الشبكة)
  * **Packet Type:** `ARP Request`
  * **Data:** `computer6`
* انقر **Send Packet**:
  * راقب تدفق الحزمة: تمر عبر المحول الخاص بـ Subnet B وتصل إلى: `computer4`, `computer5`, `computer6` ومنفذ الموجه `router` (إجمالي 4 أجهزة).
  * يستقبل `computer6` الطلب بنجاح ويرد بـ ARP Reply unicast إلى `computer4`.

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** Send a packet with the following: From computer1 To computer1, Packet Type: “ARP Request”, Data: computer6. How many devices can see the ARP Request?
   * **الإجابة:** `4`
2. **السؤال 2:** Did computer6 receive the ARP Request? (yea/nay)
   * **الإجابة:** `nay`
3. **السؤال 3:** Send a packet with the following: From computer4 To computer4, Packet Type: “ARP Request”, Data: computer6. How many devices can see the ARP Request?
   * **الإجابة:** `4`
4. **السؤال 4:** Did computer6 reply to the ARP Request? (yea/nay)
   * **الإجابة:** `yea`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* محاكاة الحركة في المعمل أظهرت توقف حزم البث العام بدقة عند المنفذ `eth0/eth1` للراوتر.
* توثيق الإجابات بنسبة تطابق 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* **القاعدة الجوهرية لفحص ARP في Nmap:**
  * خيار فحص الـ ARP (`nmap -PR` أو الفحص الافتراضي للمستخدم ذي الصلاحيات `root`) **فعال فقط على نفس الشبكة المحلية (Same Subnet / Local Ethernet)**.
  * إذا كان الهدف يقع خارج الشبكة المحلية (عبر راوتر أو عبر الإنترنت)، فإن Nmap لا يستطيع استخدام ARP للوصول إليه، ويجب أن ينتقل تلقائياً لاستخدام بروتوكولات الطبقات الأعلى (ICMP, TCP, UDP).
