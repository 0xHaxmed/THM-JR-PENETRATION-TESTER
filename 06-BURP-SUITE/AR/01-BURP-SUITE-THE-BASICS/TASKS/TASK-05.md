# Task 05: The Dashboard (لوحة التحكم ومتابعة مهام وسجلات النظام)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The Burp Suite Dashboard serves as the central command console. It aggregates real-time task execution, scanner results, and diagnostic telemetry across four distinct quadrants (arranged counter-clockwise from the top-left):

1. **Tasks:**
   Displays automated background processes currently running. In Burp Community, this defaults to the **Live Passive Crawl from Proxy** task. In Burp Professional, this section monitors active vulnerability scans, crawler threads, and scheduled auditing pipelines.
2. **Event log:**
   Records diagnostic and operational events executed by Burp Suite:
   - Proxy listener initialization (`Proxy service started on 127.0.0.1:8080`).
   - Extensions loaded and API events.
   - Outbound connections made through upstream proxies or upstream SOCKS proxies.
   - Network connection errors and TLS handshake failures.
3. **Issue Activity:**
   *(Burp Professional Feature)* Displays live security vulnerabilities identified by the automated scanner, categorized by severity (High, Medium, Low, Informational) and confidence (Certain, Firm, Tentative).
4. **Advisory:**
   *(Burp Professional Feature)* Provides an in-depth vulnerability encyclopedia detailing remediation steps, vulnerable request/response proofs of concept, and external CVE/CWE references for the issue selected in Issue Activity.

#### الأسئلة المطروحة (Questions):
1. What menu provides information about the actions performed by Burp Suite, such as starting the proxy, and details about connections made through Burp? (Answer Format: Menu Name)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **استكشاف أخطاء الشبكة عبر الـ `Event log` (Troubleshooting Connection Failures):**
  * عندما يفشل متصفحك في فتح موقع معين عبر البروكسي (مثل ظهور خطأ `Burp proxy error: received fatal alert: certificate_unknown` أو `connection reset by peer`)، لا تضيع الوقت في تخمين السبب؛ بل توجه فوراً إلى تبويب **Event log**.
  * يسجل هذا السجل تفاصيل انهيار المصافحة، أو حظر المنفذ، أو فشل الوكيل العكسي، مما يمنح المختبر سبباً دقيقاً للمشكلة وكيفية معالجتها.
* **مراقبة مهام الزحف السلبي (Passive Crawl Monitoring):**
  * حتى في الإصدار المجاني، يقوم Burp بتحليل حركة المرور المارة عبر البروكسي سلبياً (Passive Auditing) لاكتشاف الروابط وملفات الجافا سكريبت وتحديث شجرة الموقع (Site Map) دون إرسال طلب فحص واحد للهدف.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * السجل المسؤول عن رصد أحداث النظام والاتصالات في أسفل يسار لوحة التحكم يتكون من كلمتين بالإنجليزية: `Event log`.
2. **التلميح الثاني (المستوى العملي):**
   * راجع أقسام لوحة التحكم الموضحة في شرح المهمة ستجد أن القسم الثاني يعرض حالة تشغيل البروكسي وتفاصيل الاتصالات.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الإجابة المعتمدة هي: `Event log`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشريح مخرجات سجل الأحداث (Event Log Output Breakdown):
```text
Time          Source      Level     Event
03:00:01      Proxy       Info      Proxy service started on 127.0.0.1:8080
03:00:15      Proxy       Info      Browser connected: 127.0.0.1:54321
03:00:18      Target      Info      Added new host to site map: http://10.10.x.x
03:01:05      Proxy       Error     Failed to establish TLS connection with remote host (Handshake failed)
```
* يوضح السجل متى تم تشغيل الخدمة، متى اتصل المتصفح، وأي أخطاء شبكية تطرأ أثناء جلسة الاختبار.

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Event Logging Menu):** `Event log`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تحديد قائمة `Event log` كمركز لتتبع سجلات تشغيل البروكسي والاتصالات.
* تم اعتماد الإجابة بنجاح 100% على منصة TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
لوحة التحكم وسجل الأحداث (Event log) هما عين المختبر على الصحة التشغيلية لمنصة Burp Suite؛ فالرجوع للسجل هو الخطوة التشخيصية الأولى عند تعطل اتصال البروكسي أو فشل مصافحات شهادات الـ TLS مع الخادم الهدف.
