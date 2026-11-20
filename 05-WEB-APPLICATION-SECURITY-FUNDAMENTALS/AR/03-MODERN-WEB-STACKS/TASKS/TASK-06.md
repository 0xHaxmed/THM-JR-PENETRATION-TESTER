# Task 06: Automation (الأتمتة واستخدام أداة Nikto لفحص البصمات والثغرات الشائعة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
While manual fingerprinting is quiet and provides deep architectural comprehension, automated vulnerability scanners can rapidly cross-reference hundreds of server signatures and known security headers in seconds.

**Automated Stack Scanning with Nikto:**
Nikto is an open-source web server scanner that tests web servers for dangerous files, outdated program versions, and platform-specific configuration items.

Running Nikto across our target ports demonstrates how automated tooling flags these fingerprint signals:
1. **Port 3000 (Express):**
   ```bash
   nikto -h http://MACHINE_IP:3000
   ```
   * Identifies `x-powered-by: Express`.
   * Notes cookie `connect.sid` created without the `HttpOnly` flag.
2. **Port 3001 (Next.js):**
   ```bash
   nikto -h http://MACHINE_IP:3001
   ```
   * Flags `x-powered-by: Next.js`.
   * Maps modern App Router response headers (`x-nextjs-cache`, `x-nextjs-prerender`, `x-nextjs-stale-time`).
3. **Port 8000 (Django):**
   ```bash
   nikto -h http://MACHINE_IP:8000
   ```
   * Identifies `Server: WSGIServer/0.2 CPython/3.10.12`.
   * Confirms `referrer-policy: same-origin` and `x-content-type-options: nosniff`.
4. **Port 8080 (Apache 2.4.49):**
   ```bash
   nikto -h http://MACHINE_IP:8080
   ```
   * Flags `Server: Apache/2.4.49 (Unix)`.
   * Immediately triggers vulnerability alerts for **CVE-2021-41773** (Path Traversal and RCE).

#### الأسئلة المطروحة (Questions):
1. I have practiced that automation techniques with Nikto! (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الموازنة بين السرعة والضجيج (Speed vs. Stealth Trade-off):**
  * Nikto أداة صاخبة جداً؛ إذ ترسل آلاف الطلبات في دقائق معدودة، وتحتوي معظم طلباتها على بصمة `User-Agent: Mozilla/5.00 (Nikto/2.x)`.
  * في اختبارات الاختراق المصرح بها ذات النطاق الزمني الضيق، تفيد أدوات الأتمتة في تحديد الثغرات السطحية بسرعة. ولكن في عمليات الفرق الحمراء (Red Teaming) والتخفي، يفضل دائماً الفحص اليدوي المخصص والسكربتات الخفيفة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة تطبيقية لممارسة أوامر الفحص التلقائي. تأكد من تشغيل أمر Nikto على المنافذ المختلفة لمعاينة تقاريرها.
2. **التلميح الثاني (المستوى العملي):**
   * قارن بين المخرجات اليدوية التي استخرجناها بـ curl ومخرجات فحص Nikto.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * السؤال لا يتطلب أي قيمة نصية؛ اضغط على Submit مباشرة لاعتماده.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشغيل Nikto ضد المنافذ الأربعة ومقارنة المخرجات:
```bash
# 1. فحص خادم Express على المنفذ 3000
nikto -h http://MACHINE_IP:3000

# 2. فحص خادم Next.js على المنفذ 3001
nikto -h http://MACHINE_IP:3001

# 3. فحص خادم Django على المنفذ 8000
nikto -h http://MACHINE_IP:8000

# 4. فحص خادم Apache على المنفذ 8080 والتقاط تحذير الثغرة
nikto -h http://MACHINE_IP:8080
```

#### مقتطف من تقرير فحص Nikto للمنفذ 8080:
```text
+ Target IP:          MACHINE_IP
+ Target Hostname:    MACHINE_IP
+ Target Port:        8080
+ Server: Apache/2.4.49 (Unix)
+ Server may leak inodes via ETags, header found with file /, inode: 45, size: 5d023450e1b40, mtime: gzip
+ The anti-clickjacking X-Frame-Options header is not present.
+ OSVDB-0: Apache/2.4.49 is vulnerable to Path Traversal and Remote Code Execution (CVE-2021-41773).
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تشغيل الفحص وملاحظة دقة الأداة في رصد بصمات الخوادم وتحديد ثغرة Apache الحرجة فورياً.
* تم اعتماد السؤال بنجاح على منصة TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
توفر أدوات الأتمتة مثل Nikto تحققاً سريعاً وشاملاً من التهيئة الأمنية والإصدارات القديمة، لكن الفهم العميق لنتائج الفحص اليدوي هو ما يمكّن المختبر المحترف من كتابة حمولات استغلال مخصصة تتجاوز الحمايات وقيود البيئة.
