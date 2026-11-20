# Task 01: Introduction (المقدمة ومنهجية استكشاف وتحديد أطر الويب الحديثة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
In modern penetration testing, blind vulnerability scanning is slow, noisy, and easily blocked by Web Application Firewalls (WAFs). Instead, professional security testers use **stack fingerprinting**: rapidly identifying the underlying web framework, language runtime, server software, and component versions from passive HTTP signals.

Once a specific web stack and version are identified, you can pivot directly to known Common Vulnerabilities and Exposures (CVEs) and architecture-specific misconfigurations, reducing reconnaissance time from hours to seconds.

This room deploys an Ubuntu 22.04 target machine running four independent, modern web stacks on dedicated ports:
1. **Port 3000:** MERN Stack (Node.js / Express.js / React)
2. **Port 3001:** Next.js (React Server Components / App Router)
3. **Port 8000:** Django (Python WSGIServer / MySQL)
4. **Port 8080:** LAMP Stack (Apache 2.4.49 with `mod_cgi`)

**Three-Step Workflow:**
1. *Fingerprint the stack* from passive HTTP response signals (headers, cookies, error pages, DOM markers).
2. *Confirm the version* and identify the applicable CVE.
3. *Execute the exploit chain* and understand the root cause.

#### الأسئلة المطروحة (Questions):
1. I have started the machine and am ready to begin. (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الفحص الصامت وبناء الملف التعريفي للهدف (Passive Stack Profiling):**
  * إطلاق ماسحات الثغرات العمياء مثل Nessus أو Acunetix على خوادم حديثة يؤدي إلى إطلاق مئات الإنذارات لدى أنظمة كشف التسلل (IDS/IPS) ومركز العمليات الأمنية (SOC)، مع احتمال حظر عنوان الـ IP المهاجم فوراً.
  * في المقابل، إرسال طلب HTTP مفرد (`curl -I`) وقراءة الترويسات وملفات تعريف الارتباط (Cookies) وتنسيق صفحات الخطأ (Error Pages) يعتبر استطلاعاً سلبياً غير ملفت تماماً (0% Noise)، لكنه يكشف النواة البرمجية بالكامل.
* **الانتقال من التخمين إلى الاستغلال الموجه (Targeted Exploitation):**
  * معرفة أن الخادم يعمل بـ Next.js App Router توجه انتباه المختبر مباشرة إلى ثغرات التوجيه والـ Middleware مثل CVE-2025-29927.
  * معرفة أن خادم Apache يعمل بإصدار `2.4.49` توجه الاختبار مباشرة لاجتياز المسارات عبر `.%2e/` (CVE-2021-41773).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة مدخل تمهيدي ومراجعة لمفاهيم المعمل. اضغط على زر تشغيل الآلة وانتظر حتى يكتمل إقلاعها وتخصيص عنوان الـ IP.
2. **التلميح الثاني (المستوى العملي):**
   * تأكد من اتصالك بشبكة TryHackMe VPN أو استخدام AttackBox. المنافذ الأربعة المستهدفة هي 3000 و 3001 و 8000 و 8080.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * السؤال الأول لا يتطلب أي قيمة نصية؛ بمجرد الضغط على "Submit" يتم اعتماده.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### التحقق من الاتصال واستكشاف المنافذ الأربعة المستهدفة:
```bash
# 1. فحص المنافذ الأربعة المحددة للمعمل باستخدام Nmap السريع
nmap -p 3000,3001,8000,8080 -sS -n -T4 MACHINE_IP

# 2. فحص أولي لترويسات كل منفذ لتأكيد الجاهزية
curl -I http://MACHINE_IP:3000/  # MERN / Express
curl -I http://MACHINE_IP:3001/  # Next.js
curl -I http://MACHINE_IP:8000/  # Django
curl -I http://MACHINE_IP:8080/  # Apache 2.4.49
```

#### بنية الأنظمة والخدمات المشغلة في المعمل:
| المنفذ (Port) | المكدس البرمجي (Web Stack) | لغة البرمجة وبيئة التشغيل | الثغرة الأمنية المستهدفة (Target CVE) |
| :--- | :--- | :--- | :--- |
| **3000** | MERN (Express.js) | JavaScript / Node.js V8 | Prototype Pollution (تلوث النموذج الأولي) |
| **3001** | Next.js App Router | TypeScript / React RSC | CVE-2025-29927 (تجاوز مصادقة Middleware) |
| **8000** | Django / Python | Python 3.10 / WSGIServer | CVE-2021-35042 (حقن SQL عبر ORDER BY) |
| **8080** | LAMP / Apache | Linux / C / Docker / CGI | CVE-2021-41773 (تخطي المسارات وتنفيذ أوامر RCE) |

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم التأكد من إقلاع الآلة والاستجابة على كافة المنافذ الأربعة المحددة.
* تم اعتماد السؤال بنجاح على منصة TryHackMe بنسبة إنجاز صحيحة ومؤكدة.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
البصمة التقنية السلبية (Passive Stack Fingerprinting) هي السلاح الأقوى لمختبر الاختراق في بيئات الويب؛ فبدلاً من مهاجمة الخادم بملايين الحمولات غير المناسبة، يقودك فحص بضعة ترويسات إلى الإصدار المحدد والثغرة القاتلة التي تمكنك من السيطرة الكاملة على النظام في بضع دقائق.
