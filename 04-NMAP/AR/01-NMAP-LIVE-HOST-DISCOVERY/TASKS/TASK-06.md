# المهمة 06: اكتشاف الأجهزة عبر بروتوكول ICMP في Nmap (Task 06: Nmap Host Discovery Using ICMP)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستكشف هذه المهمة كيفية استخدام أداة **Nmap** لبروتوكول رسائل التحكم في الإنترنت **ICMP (Internet Control Message Protocol)** لاكتشاف الأجهزة الحية على الشبكات الموجهة (Routed Networks / Remote Targets).

يعرّف معيار RFC 792 أنواعاً متعددة لرسائل ICMP الاستعلامية، وتتيح Nmap لمختبر الاختراق توظيف ثلاثة خيارات مركزية منها:
1. **ICMP Echo Request (`-PE`):**
   * ترسل حزمة ICMP Type 8 (Echo Request).
   * يستجيب الجهاز الحي بحزمة ICMP Type 0 (Echo Reply).
2. **ICMP Timestamp Request (`-PP`):**
   * ترسل حزمة ICMP Type 13 (Timestamp Request) تطلب التوقيت الحالي للنظام بالمللي ثانية منذ منتصف الليل بتوقيت UTC.
   * يستجيب الجهاز الحي بحزمة ICMP Type 14 (Timestamp Reply).
3. **ICMP Address Mask Request (`-PM`):**
   * ترسل حزمة ICMP Type 17 (Address Mask Request) تطلب قناع الشبكة الفرعية للجهاز.
   * يستجيب الجهاز الحي بحزمة ICMP Type 18 (Address Mask Reply).

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)
* **المعضلة الأمنية في فحص ICMP التقليدي:**
  * معظم مسؤولي الشبكات والجدران النارية الحديثة يقومون بحجب فحص الـ Ping التقليدي (ICMP Echo - Type 8) لمنع الغرباء من استكشاف الشبكة وجعل الأجهزة تبدو وكأنها غير متصلة (Offline).
* **الحيلة الالتفافية لمختبر الاختراق (Firewall Evasion via Non-Standard ICMP):**
  * الكثير من الجدران النارية تُعِدّ قواعد حجب تستهدف تحديداً `ICMP Type 8 (Echo)` دون حجب بقية أنواع الـ ICMP.
  * هنا تبرز القوة التكتيكية لـ **ICMP Timestamp (`-PP`)** و **ICMP Address Mask (`-PM`)**:
    * عند إرسال طلب Timestamp Type 13، قد يمر الطلب عبر الجدار الناري بسلاسة إذا كان المشرف قد حظر Echo فقط.
    * يقوم نظام تشغيل الهدف بالرد بـ Type 14، مما يُثبت لـ Nmap أن الجهاز نشط وحي 100% دون الحاجة إلى Echo Ping!

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* كل معامل في Nmap يبدأ بشرطة وحرف `P` (مختصر Ping) متبوعاً بحرف يرمز لنوع الحزمة:
  * `E` = Echo (`-PE`)
  * `P` = Timestamp (`-PP`)
  * `M` = Address Mask (`-PM`)

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### صياغة وتجربة أوامر فحص ICMP المختلفة:
```bash
# 1. فحص الأجهزة باستخدام ICMP Echo Request
sudo nmap -PE -sn 10.10.210.100

# 2. فحص الأجهزة باستخدام ICMP Timestamp Request لتجاوز حجب الـ Echo
sudo nmap -PP -sn 10.10.210.100

# 3. فحص الأجهزة باستخدام ICMP Address Mask Request
sudo nmap -PM -sn 10.10.210.100
```

#### تحليل الحزم عبر Wireshark / Tcpdump:
عند مراقبة الحزم أثناء تنفيذ `-PP`:
```text
IP 10.10.14.5 > 10.10.210.100: ICMP time stamp query id 49132 seq 0, length 20
IP 10.10.210.100 > 10.10.14.5: ICMP time stamp reply id 49132 seq 0: 0x82b24370, length 20
```
تؤكد استجابة الـ `time stamp reply` نشاط الهدف المباشر.

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** What is the option required to tell Nmap to use ICMP Timestamp to discover live hosts?
   * **الإجابة:** `-PP`
2. **السؤال 2:** What is the option required to tell Nmap to use ICMP Address Mask to discover live hosts?
   * **الإجابة:** `-PM`
3. **السؤال 3:** What is the option required to tell Nmap to use ICMP Echo to discover live hosts?
   * **الإجابة:** `-PE`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* خيارات Nmap الثلاثة موثقة في الدليل الرسمي (`man nmap`) تحت قسم `HOST DISCOVERY: ICMP Pings`.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* لا تستسلم أبداً عندما لا يستجيب الهدف لحزم الـ Ping العادية؛ فغياب استجابة ICMP Echo لا يعني إطلاقاً أن الخادم غير متصل.
* التنويع بين `-PE` و `-PP` و `-PM` يمنح مختبر الاختراق القدرة على اكتشاف الأنظمة الحية المخفية خلف جدران نارية مهيأة بشكل سطحي.
