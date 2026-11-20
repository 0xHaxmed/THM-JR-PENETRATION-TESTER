# المهمة 03: فهم اكتشاف الأجهزة عبر طبقات مكدس TCP/IP (Task 03: Understanding Hosts Discovery through TCP/IP Layer)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تتعمق هذه المهمة في تشريح آلية عمل استكشاف الأجهزة الحية على مستوى طبقات مكدس بروتوكولات TCP/IP، وتحديداً التفاعل بين:
1. **بروتوكول ARP (الطبقة الثانية - Data Link Layer):** المسؤول عن إيجاد عنوان MAC الفيزيائي المقابل لعنوان IP المنطقي.
2. **بروتوكول ICMP (الطبقة الثالثة - Network Layer):** بروتوكول التحكم ورسائل الإنترنت، المسؤول عن إرسال حزم الاستعلام `Echo Request` (Type 8) واستقبال الاستجابة `Echo Reply` (Type 0).
3. **دور الذاكرة المؤقتة (ARP Cache):** كيف تحتفظ الأجهزة بروابط (IP-to-MAC) في جدول ARP لتجنب تكرار البث العام مع كل حزمة جديدة.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)
* **المعضلة الفيزيائية:** لا يمكن لأي كارت شبكة (NIC) إرسال حزمة IP (مثل Ping) إلى جهاز آخر على الشبكة إلا بعد تغليفها داخل إطار إيثرنت (Ethernet Frame) يحتوي على عنوان الـ MAC للهدف.
* **السيناريو الأول (اتصال محلي داخل نفس الشبكة: `computer1` إلى `computer3`):**
  * جدول ARP لدى `computer1` فارغ في البداية.
  * لذلك، قبل أن تنطلق حزمة الـ Ping، يُجبر نظام التشغيل على تعليق حزمة الـ Ping مؤقتاً، وإرسال **ARP Request** أولاً لمعرفة MAC الخاص بـ `computer3`.
  * يرد `computer3` بحزمة **ARP Response (Reply)** موجهة مباشرة (Unicast).
  * الآن بعد اكتمال العنوان، يرسل `computer1` حزمة **ICMP Echo Request**، ويستجيب `computer3` وحده بحزمة **ICMP Echo Reply** (جهاز واحد فقط يستجيب).
* **السيناريو الثاني (اتصال عبر الموجه إلى شبكة أخرى: `computer2` في Subnet A إلى `computer5` في Subnet B):**
  * يعلم `computer2` عبر قناع الشبكة (Subnet Mask) أن `computer5` يقع في شبكة فرعية أخرى بعيدة.
  * لذلك، لا يسأل `computer2` عن MAC الخاص بـ `computer5`، بل يسأل عن MAC الخاص ببوابته الافتراضية (**router**).
  * الموجه (**router**) هو أول جهاز يرد على طلب الـ ARP الأول.
  * بعد ذلك، يستلم الموجه حزمة الـ Ping وينظر في جدول التوجيه ليجد أن `computer5` متصل بواجهته الأخرى (Subnet B). يقوم الراوتر بإرسال طلب ARP ثانٍ في Subnet B سائلاً عن MAC الخاص بـ `computer5`.
  * يرد **computer5** على طلب الـ ARP الثاني.
  * في المحاولات التالية لنفس الاتصال، تكون كافة العناوين مخزنة في الـ **ARP Cache** لجميع الأطراف، فلا حاجة لإرسال أي طلبات ARP جديدة (**`nay`**).

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* راقب في المحاكي تسلسل الحزم بدقة: لن تخرج أي حزمة Ping زرقاء اللون إلا بعد اكتمال تبادل حزم ARP الوردية/الخضراء.
* تذكر أن الجهاز عندما يريد إرسال بيانات خارج شبكته المحلية فإنه يستعلم دائماً عن عنوان الـ MAC للراوتر (Default Gateway) وليس للهدف النهائي.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### التجربة 1: فحص Ping المحلي (`computer1` إلى `computer3`)
1. في المحاكي، اختر:
   * **From:** `computer1`
   * **To:** `computer3`
   * **Packet Type:** `Ping Request`
2. اضغط **Send Packet**:
   * لاحظ أن أول حزمة غادرت `computer1` هي: `ARP Request`.
   * استلم `computer1` حزمة: `ARP Response` من `computer3`.
   * بعد ذلك انطلقت حزمة ICMP واستجاب جهاز واحد فقط وهو `computer3` (عدد الأجهزة المستجيبة = 1).

#### التجربة 2: فحص Ping عبر الشبكات الفرعية (`computer2` إلى `computer5`)
1. في المحاكي، اختر:
   * **From:** `computer2`
   * **To:** `computer5`
   * **Packet Type:** `Ping Request`
2. اضغط **Send Packet**:
   * يرسل `computer2` طلب ARP إلى البوابة: الجهاز الأول المستجيب هو `router`.
   * يقوم الراوتر بإرسال ARP Request داخل Subnet B سائلاً عن `computer5`: الجهاز المستجيب لطلب الـ ARP الثاني هو `computer5`.
   * تكتمل حزمة Ping بنجاح.

#### التجربة 3: إرسال Ping للمرة الثانية بعد ملء الـ Cache
1. أعد إرسال Ping Request من `computer2` إلى `computer5` مرة أخرى فوراً.
2. لاحظ تدفق الحزم: تنطلق حزمة Ping وتعود فوراً عبر الموجه دون أي ظهور لحزم ARP (`nay`)، وذلك بفضل وجود الإدخالات في الـ ARP Table.

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** Send a packet with the following: From computer1 To computer3, Packet Type: “Ping Request”. What type of packet did computer1 send before the ping?
   * **الإجابة:** `ARP Request`
2. **السؤال 2:** What type of packet did computer1 receive before it was able to send the ping?
   * **الإجابة:** `ARP Response`
3. **السؤال 3:** How many computers responded to the ping request?
   * **الإجابة:** `1`
4. **السؤال 4:** Send a packet with the following: From computer2 To computer5, Packet Type: “Ping Request”. What is the name of the first device that responded to the first ARP Request?
   * **الإجابة:** `router`
5. **السؤال 5:** What is the name of the first device that responded to the second ARP Request?
   * **الإجابة:** `computer5`
6. **السؤال 6:** Send another Ping Request. Did it require new ARP Requests? (yea/nay)
   * **الإجابة:** `nay`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* محاكاة تبادل الحزم تطابق بنسبة 100% معيار RFC 826 لبروتوكول ARP ومعيار RFC 792 لبروتوكول ICMP.
* تأكيد الإجابات من السجل المكتمل للمستخدم على المنصة.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* أجهزة التوجيه تعمل كحاجز حاسم بين الطبقة الثانية والثالثة؛ عنوان الـ IP يبقى ثابتاً من المصدر حتى الهدف النهائي (End-to-End)، بينما عنوان الـ MAC يتغير عند كل قفزة راوتر (Hop-by-Hop).
* الذاكرة المؤقتة (ARP Cache) تقلل الضغط على الشبكة، وفهم دورها يساعد مختبر الاختراق في تشخيص سلوك أدوات الفحص مثل Nmap وتأثير هجمات التسميم مثل ARP Spoofing.
