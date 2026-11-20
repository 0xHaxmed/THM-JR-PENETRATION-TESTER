# Module 04: Topic Transition Recap
## أسئلة المراجعة الشاملة والتقييم الانتقالي للوحدة الرابعة: فحص الشبكات واستكشاف المنافذ (Nmap)

* **Module:** Module 04: Nmap
* **Recap URL:** `https://tryhackme.com/recap/6afe01d5f306fc3f65927f20`
* **Objective:** دمج وتقييم المعارف المكتسبة عبر غرف Nmap الأربع (Live Host Discovery, Basic Port Scans, Advanced Port Scans, Post Port Scans) قبل الانتقال إلى الوحدة الخامسة: أساسيات أمان تطبيقات الويب (Web Application Security Fundamentals).

---

### 📋 أسئلة التقييم الانتقالي الرسمي من TryHackMe (Topic Transition Assessment)

#### السؤال 1: الترتيب المنهجي لتعداد الأهداف وتوثيق نتائج سكربتات NSE
* **السؤال:** Order these steps for enumerating a target and documenting a service-specific NSE finding.
* **العناصر المراد ترتيبها:**
  * `Save the script output to a file for the report`
  * `Run version detection on the discovered open ports`
  * `Port scan the target to list its open ports`
  * `Run an NSE script matching the identified service`
* **الترتيب المنهجي الصحيح والمؤكد:**
  1. **`Port scan the target to list its open ports`** (فحص منافذ الهدف لحصر المنافذ المفتوحة).
  2. **`Run version detection on the discovered open ports`** (تشغيل كشف الخدمات والإصدارات على المنافذ المفتوحة المكتشفة).
  3. **`Run an NSE script matching the identified service`** (تشغيل سكربت NSE مخصص يطابق الخدمة المحددة).
  4. **`Save the script output to a file for the report`** (حفظ مخرجات السكربت في ملف لدعم التقرير النهائي).
* **الشرح الفني والأكاديمي:**
  * يتبع اختبار الاختراق الاحترافي خط أنابيب تصاعدي دقيق: لا يمكنك فحص إصدارات قبل معرفة المنافذ المفتوحة، ولا يمكنك اختيار سكربت استغلال أو تدقيق متخصص (مثل سكربتات HTTP أو SMB) قبل معرفة البرنامج المشغل وإصداره، وأخيراً يجب أرشفة المخرجات وحفظها كأدلة جنائية للتقرير النهائي.

---

#### السؤال 2: معضلة بروتوكول فحص خدمة `rpcbind`
* **السؤال:** A pentester runs `nmap -sV --version-light MACHINE_IP` against a target hosting rpcbind on port 111, but Nmap fails to identify the version. Which explanation best accounts for this failure?
* **الخيارات المتاحة:**
  * `-sV --version-light is too conservative; increase to --version-all.`
  * `rpcbind requires probing on UDP; -sV default uses TCP only.`
  * `The pentester omitted -Pn, so host discovery aborted the scan.`
  * `rpcbind always encrypts its banner, preventing pattern matching.`
* **الإجابة النموذجية:** `rpcbind requires probing on UDP; -sV default uses TCP only.`
* **الشرح الفني والأكاديمي:**
  * على الرغم من أن خيار `--version-light` يقلل من عدد الاستعلامات، إلا أن السبب الجذري للفشل ليس عدد الاستعلامات بل عدم تطابق بروتوكول طبقة النقل (Protocol Mismatch).
  * خدمة ربط نداء الإجراءات البعيدة (RPCBind / Portmapper) تعتمد هيكلياً وبشكل أساسي على بروتوكول **UDP** لتقديم معلومات برامج RPC، بينما فحص `-sV` الافتراضي يفحص منافذ TCP فقط ما لم يُحدد خيار UDP صراحة (`-sU`). لذلك يستحيل كشف تفاصيل الخدمة دون استجواب مكدس UDP.

---

#### السؤال 3: حتمية المصافحة الثلاثية في كشف الإصدارات
* **السؤال:** Service version detection requires Nmap to complete the TCP three-way handshake with the open port. (True / False)
* **الإجابة النموذجية:** `True`
* **الشرح الفني والأكاديمي:**
  * لا يمكن لأي أداة فحص استخراج بنرات الخدمات (Application Banners) أو تبادل استعلامات بروتوكول التطبيق (مثل HTTP GET أو SSH identification string) عبر فحص نصف مفتوح (Half-Open SYN Scan)؛ لأن التطبيق المستمع في نظام الهدف لن يستلم الحزمة ولن يفتح جلسة تخاطب إلا بعد اكتمال المصافحة الثلاثية (`SYN -> SYN/ACK -> ACK`) وانتقال المقبس إلى حالة `ESTABLISHED`. لذلك يجبر خيار `-sV` أداة Nmap على إتمام الاتصال كاملاً.

---

#### السؤال 4: تصنيفات سكربتات NSE الأكثر خطورة على بيئات الإنتاج
* **السؤال:** Which NSE script category is most likely to knock a production service offline during testing?
* **الخيارات المتاحة:**
  * `discovery`
  * `vuln`
  * `safe`
  * `dos`
* **الإجابة النموذجية:** `dos` (Denial of Service)
* **الشرح الفني والأكاديمي:**
  * صُممت فئة سكربتات **`dos`** لاختبار ما إذا كانت الخدمة تنهار أو تتوقف عن العمل عند إرسال حزم مشوهة أو أحمال زائدة. تشغيل هذه السكربتات في بيئة إنتاجية حقيقية للعميل قد يؤدي إلى إسقاط الخادم فعلياً وتعطيل الأعمال، ولذلك تحظر قواعد الاشتباك (Rules of Engagement) تشغيلها دون إذن كتابي صريح، ويُفضل استخدام فئة `safe` للتدقيق الآمن.

---

#### السؤال 5: الهدف من إضافة خيار `--traceroute` في Nmap
* **السؤال:** What is the purpose of adding `--traceroute` to an Nmap scan?
* **الخيارات المتاحة:**
  * `Identify the operating system of each intermediary router`
  * `Determine whether the target is filtering specific TCP flags`
  * `Map the routers on the path to the target`
  * `Calculate the round-trip time for each open port`
* **الإجابة النموذجية:** `Map the routers on the path to the target` (رسم وتحديد أجهزة التوجيه في المسار نحو الهدف)
* **الشرح الفني والأكاديمي:**
  * يقوم المعامل `--traceroute` في Nmap بتحديد طوبولوجيا الشبكة ورسم المسار الفيزيائي والمنطقي للحزم بين جهاز الفاحص والهدف، عبر تتبع القفزات الوسيطة (Hops) وأجهزة التوجيه (Routers) المارة بها الحزم باستخدام تقنية تقليل قيمة وقت البقاء (TTL) في ترويسة IP.

---

#### السؤال 6: طبيعة فحص منافذ UDP عديمة الاتصال
* **السؤال:** UDP is connectionless, so a UDP port scan cannot rely on a handshake to confirm that a port is open. (True / False)
* **الإجابة النموذجية:** `True`
* **الشرح الفني والأكاديمي:**
  * بروتوكول UDP هو بروتوكول عديم الاتصال (Connectionless / Stateless)؛ لا توجد فيه مصافحة ثلاثية ولا رايات تأكيد. لذلك يعتمد Nmap على استنتاج الفتح عبر الصمت أو استلام رد تطبيقي مخصص، بينما يستنتج الإغلاق عبر استقبال رسائل أخطاء `ICMP Port Unreachable (Type 3 Code 3)`.

---

### 💡 الخلاصة الربطية للوحدة الرابعة
تتوج هذه الأسئلة الستة مخرجات موديول Nmap، مؤكدة على:
1. فهم خط الأنابيب المنطقي للاستكشاف والتدقيق الأمني.
2. الموازنة بين متطلبات البروتوكولات (TCP vs UDP) والصلاحيات والمصافحات.
3. تقدير مخاطر الأدوات البرمجية وتجنب فئات الـ DoS في اختبارات الاختراق الميدانية، والتوثيق المنظم تمهيداً للانتقال إلى عالم تطبيقات الويب في **Module 05: Web Application Security Fundamentals**.
