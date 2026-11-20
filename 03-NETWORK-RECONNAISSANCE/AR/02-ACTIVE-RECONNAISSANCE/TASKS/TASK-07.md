# Task 07: Putting It All Together
## المهمة 07: تكامل أدوات الاستطلاع النشط والجاهزية للمراحل المتقدمة

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 07
* **Original Title:** Putting It All Together
* **Questions:**
  1. *Ensure that you gain mastery over the different basic yet essential tools we presented in this room before moving on to more sophisticated tools.*
* **Objective:** Synthesize the active reconnaissance workflow combining web browser inspection, ICMP ping sweeps, traceroute topology discovery, and raw socket banner grabbing with Telnet/Netcat.
* **Core Concepts:** Active Recon Pipeline, Tool Synthesis, Operational Readiness for Port Scanning (Nmap).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **خط أنابيب الاستطلاع النشط (Active Recon Pipeline):**
  1. **الخطوة 1 (Host Availability):** استخدام `ping` للتأكد السريع من استجابة الهدف (مع مراعاة إمكانية حظر ICMP).
  2. **الخطوة 2 (Path Mapping):** استخدام `traceroute` لمعرفة عدد الموجهات الوسيطة، كشف مزودي الخدمة، وتحديد أجهزة الحماية الوسيطة.
  3. **الخطوة 3 (Web Inspection):** استخدام المتصفح وأدوات المطور `DevTools` لفحص التطبيقات السطحية والأكواد المصدرية.
  4. **الخطوة 4 (Manual Service Probing):** استخدام `telnet` و `nc` للاتصال المباشر بالمنافذ المفتوحة واستخراج الرايات الصامتة قبل تشغيل أدوات الفحص الآلية المعقدة.
* **الانتقال للمرحلة القادمة:**
  هذه الأدوات البسيطة تشكل اللبنات الأساسية لكل ماسحات المنافذ المتقدمة مثل Nmap. ففهم كيفية عمل حزمة ICMP ومقبس الـ TCP يضمن فهم كيف تفكر أداة Nmap برمجياً تحت الغطاء.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** اضغط على زر `Completed` لإنهاء الغرفة بنجاح وتحقيق نسبة 100%.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
* جدول المقارنة الوظيفية للأدوات:
  | الأداة | البروتوكول المستخدم | الغرض الاستطلاعي الرئيسي | السمة المميزة |
  |---|---|---|---|
  | **Browser DevTools** | HTTP / HTTPS | فحص واجهات الويب وأكواد الجافاسكريبت | سهولة الوصول وتحليل كود العميل |
  | **Ping** | ICMP (Echo 8/0) | التحقق من اتصال الهدف وقياس الـ RTT | بساطة وسرعة التحقق من الشبكة |
  | **Traceroute** | UDP / ICMP / TCP | رسم المسار وقياس عدد القفزات ومواقع الموجهات | استغلال حقل الـ TTL ورسالة Type 11 |
  | **Telnet** | TCP (Raw) | الاتصال بمنافذ النصوص واستخراج ترويسة HTTP | مرونة إرسال الطلبات يدوياً |
  | **Netcat** | TCP / UDP (Raw) | استخراج رايات الخدمات (FTP, SMTP) والاتصال الخام | غياب أي بروتوكولات تفاوض ثانوية |

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *Ensure that you gain mastery over the different basic yet essential tools...*
  * **Answer:** `No answer needed`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
* تم إكمال الغرفة بنسبة 100% في حساب المستخدم المعتمد وتأكيد كافة الأسئلة والرايات.

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في منهجية الاستطلاع:**
> *"Automated scanners like Nmap and Nessus are simply automated scripts wrapping ping, traceroute, and raw TCP sockets; master the fundamentals by hand, and you will never be confused by a scanner's output."*
> ماسحات الشبكات الآلية مثل Nmap و Nessus ليست سوى سكريبتات آلية تغلف أوامر ping و traceroute ومقابس TCP الخام؛ أتقن هذه الأساسيات يدوياً ولن تعجزك أي مخرجات تصدرها أعتى أدوات الفحص الآلي.
