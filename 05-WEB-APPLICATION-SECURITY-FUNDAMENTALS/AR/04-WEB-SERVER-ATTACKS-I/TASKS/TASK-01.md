# Task 01: Introduction (المقدمة ومنهجية رصد أخطاء التهيئة في خوادم الويب)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
In web security, not every compromise begins with a complex zero-day exploit or high-end binary vulnerability. Often, the easiest and most damaging path of entry stems from simple, overlooked **web server misconfigurations**: default settings left enabled, debugging features exposed to the public internet, directory listings activated, or administrative status endpoints left unrestricted.

This room focuses exclusively on enumerating and identifying misconfigurations across four distinct web servers deployed on a single Ubuntu machine across different ports:
1. **Port 80:** Apache2 (traditional enterprise web server)
2. **Port 8000:** Python HTTP Server (`python3 -m http.server`, ad-hoc file sharing)
3. **Port 3000:** Node.js Express (modern application server pattern)
4. **Port 8080:** Nginx (high-performance reverse proxy and static server)

**Learning Objectives:**
- Identify web server software and versions using HTTP response headers and default error pages.
- Recognise the severe risks of running Python's built-in HTTP server in environments holding sensitive files.
- Enumerate Apache directory listings, exposed `mod_status` metrics, and unlinked backup files.
- Identify debug endpoints, verbose error messages, and environment variable leaks (`process.env`) in Node.js Express applications.
- Detect Nginx `autoindex` directory listings and exposed `nginx_status` connections.
- Perform a security header audit across all servers using `curl` and automated tooling like `nikto`.

#### الأسئلة المطروحة (Questions):
1. I have deployed the lab machines! (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الأثر الأمني لأخطاء التهيئة (Security Misconfigurations - OWASP A05):**
  * تصنف أخطاء التهيئة الأمنية باستمرار ضمن أخطر التهديدات لأنها تمنح المهاجم معلومات حساسة أو وصولاً مباشراً لملفات النظام دون إثارة أي تنبيهات شاذة في جدران حماية الويب (WAF) أو سجلات كشف التسلل.
  * طلب ملف `.env` أو مسار `/server-status` يبدو كطلب HTTP GET عادي تماماً بنسبة نجاح 200 OK، مما يجعله غير مرئي لأنظمة الحماية المعتمدة على اكتشاف الحمولات الخبيثة (Payload Detection).
* **المسح الهادئ متعدد المنافذ (Silent Multi-Port Probing):**
  * فحص المنافذ 80 و 8000 و 3000 و 8080 يظهر كيف تستخدم الشركات تقنيات متعددة على نفس السيرفر، حيث يخصص Apache للمواقع القديمة، و Nginx للملفات الثابتة والوكيل العكسي، و Node.js لواجهات الـ API، بينما يقوم المطور أحياناً بتشغيل خادم Python مؤقت للمشاركة السريعة ونسيانه شغالاً!

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة ترحيبية ومدخل لاستيعاب أهداف المعمل. اضغط على زر تشغيل الآلة وانتظر حتى يكتمل تخصيص الـ IP.
2. **التلميح الثاني (المستوى العملي):**
   * تأكد من جاهزية شبكة الاتصال سواء عبر ملف الـ OpenVPN أو جهاز AttackBox.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * السؤال الأول لا يتطلب أي مدخلات نصية؛ اضغط على Submit لاعتماده مباشرة.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### التحقق من جاهزية المنافذ الأربعة على الخادم المعملي:
```bash
# فحص حالة المنافذ الأربعة باستخدام Nmap
nmap -p 80,8000,3000,8080 -sT -n -T4 MACHINE_IP
```

#### بنية الخدمات والخوادم المستهدفة في المعمل:
| المنفذ (Port) | خادم الويب (Server Software) | نوع الاستخدام (Role) | أهم أخطاء التهيئة المعملية (Key Misconfigurations) |
| :---: | :--- | :--- | :--- |
| **80** | **Apache2** | خادم رئيسي كلاسيكي | كشف الإصدار، فهرسة `/files/`، تسريب إحصائيات `mod_status`، ملفات `.bak` غير مرتبطة |
| **8000** | **Python SimpleHTTP** | مشاركة سريعة للملفات | انعدام المصادقة تماماً، تسريب ملف `.env` مع كلمات مرور قواعد البيانات، أرشيف النسخ الاحتياطي |
| **3000** | **Node.js Express** | واجهة برمجة تطبيقات API | ترويسة `X-Powered-By`، مسار تصحيح البيئة `/api/debug/env`، كشف المتغيرات الحساسة |
| **8080** | **Nginx** | خادم وكيل وملفات ثابتة | كشف الإصدار عبر `server_tokens`، تفعيل `autoindex`، إتاحة `/nginx_status` للعامة |

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تشغيل الآلة المعملية واستجابة المنافذ 80، 8000، 3000، 8080.
* تم اعتماد السؤال بنجاح على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
أخطاء التهيئة لا تتطلب أدوات استغلال متقدمة لكسرها، بل تتطلب منهجية استكشاف دقيقة وفهماً عميقاً لسلوك خوادم الويب الافتراضية؛ فالأمان يتآكل حينما تُترك الخصائص التجريبية والتصحيحية مفعلة في بيئة العمل المباشرة.
