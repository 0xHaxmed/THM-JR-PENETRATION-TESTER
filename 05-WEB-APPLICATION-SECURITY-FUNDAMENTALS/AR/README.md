# الوحدة 05: أساسيات أمان تطبيقات وخوادم الويب (Web Application Security Fundamentals)

---

### 📌 مدخل إلى الوحدة الخامسة
تمثل تطبيقات الويب اليوم واجهة التفاعل الأساسية لكافة الخدمات الرقمية في العالم، ولكنها في الوقت نفسه تمثل **سطح الهجوم الأوسع والأكثر استهدافاً** من قبل المخترقين والباحثين الأمنيين. فبينما تحمي الجدران النارية منافذ الشبكة الداخلية، تظل منافذ الويب (80 و 443) مفتوحة إلزامياً للعالم أجمع للتفاعل مع التطبيقات.

في هذه الوحدة، ننتقل من فحص الشبكات والمنافذ إلى **تشريح طبقة التطبيقات (Application Layer)**، حيث نتعلم كيف يفكر مطورو الويب، وأين تكمن الأخطاء الشائعة في كتابة كود الواجهة الأمامية والخلفية، وكيف نكتشف الملفات والمسارات الإدارية المنسية، ونستغل الثغرات الشهيرة في برمجيات خوادم الويب (Web Servers).

---

### 🗂️ فهرس غرف الموديول (Module Rooms Index)

1. [الغرفة 01: التجول داخل كود التطبيق (Walking An Application)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/01-WALKING-AN-APPLICATION/README_AR.md)
   * الفحص الأمني للواجهة الأمامية عبر أدوات المطور (DevTools)، استخراج التعليقات، فحص ملفات الجافاسكربت، فحص الشبكة، وتعديل الكوكيز والتخزين المحلي.
2. [الغرفة 02: استكشاف المحتوى والمسارات (Content Discovery)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/02-CONTENT-DISCOVERY/README_AR.md)
   * كشف المسارات الخفية والصفحات الإدارية عبر الطرق اليدوية (robots.txt, favicon hashing) والتخمين الآلي عبر أداة Gobuster.
3. [الغرفة 03: مكدسات الويب الحديثة (Modern Web Stacks)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/03-MODERN-WEB-STACKS/README_AR.md)
   * تشريح بنية المكدسات المعاصرة (MERN, Next.js, Django, LAMP) والتعامل مع واجهات الـ API وتقنيات الـ SSR و CSR.
4. [الغرفة 04: هجمات خوادم الويب - الجزء الأول (Web Server Attacks - I)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/04-WEB-SERVER-ATTACKS-I/README_AR.md)
   * استغلال ثغرات وأخطاء تكوين خوادم الويب مفتوحة المصدر: Apache Path Traversal و Nginx misconfigurations.
5. [الغرفة 05: هجمات خوادم الويب - الجزء الثاني (Web Server Attacks - II)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/05-WEB-SERVER-ATTACKS-II/README_AR.md)
   * اختراق خوادم Microsoft IIS عبر تعداد ملفات Tilde القصيرة (8.3 filenames) واستغلال امتداد WebDAV لرفع الشل.
6. [الغرفة 06: تقييم ختام الوحدة (Topic Transition Recap)](file:///e:/THM-JR-PENETRATION-TESTER/05-WEB-APPLICATION-SECURITY-FUNDAMENTALS/06-TOPIC-TRANSITION-RECAP/README_AR.md)
   * مراجعة توليفية واختبار تقييمي لمفاهيم أمان الويب تمهيداً للانتقال إلى أداة Burp Suite الاحترافية.

---

### 🛠️ الأدوات والتقنيات المغطاة (Tools & Methodologies)
* **أدوات الفحص والتحليل:** Browser Developer Tools (Inspector, Console, Debugger, Storage, Network), View Source, Curl.
* **أدوات التخمين والتعداد:** Gobuster, Wfuzz, Dirb, Feroxbuster.
* **خوادم الويب المستهدفة:** Apache HTTP Server, Nginx, Microsoft IIS, Python SimpleHTTP, Node.js Express.
