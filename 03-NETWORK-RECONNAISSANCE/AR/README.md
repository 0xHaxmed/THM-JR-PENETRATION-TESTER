# الموديول 03: استطلاع واستكشاف الشبكات (Network Reconnaissance)

---

## 📌 الفهرس العام للموديول

أهلاً بك في التوثيق الشامل للوحدة الثالثة من مسار **Junior Penetration Tester**.
تعد مرحلة استطلاع الشبكات (Reconnaissance) حجر الأساس الذي يُبنى عليه نجاح أي اختبار اختراق أمني. تمنحك هذه الوحدة الأدوات النظرية والعملية لرسم خريطة الهدف بدقة متناهية، والتعرف على خوادمه ومسارات اتصاله، وفهم البروتوكولات الأساسية التي تحرك الإنترنت وثغراتها الكلاسيكية.

---

### 🏛️ غرف الموديول (Rooms Breakdown)

1. [01. الاستطلاع السلبي (Passive Reconnaissance)](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/01-PASSIVE-RECONNAISSANCE/README_AR.md)
   * استخراج معلومات النطاقات وسجلات الملكية عبر `whois`.
   * استعلام خوادم الأسماء وسجلات الـ DNS المختلفة (A, AAAA, MX, TXT, CNAME, SOA) باستخدام `nslookup` و `dig`.
   * تقنيات الاستخبارات المفتوحة (OSINT) دون لمس الهدف.

2. [02. الاستطلاع النشط (Active Reconnaissance)](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/02-ACTIVE-RECONNAISSANCE/README_AR.md)
   * فحص الاتصال وقيم الـ TTL لتحديد نوع نظام التشغيل عبر `ping`.
   * تتبع مسار الحزم والموجهات عبر الشبكة باستخدام `traceroute` و `tracert`.
   * التفاعل اليدوي واستخراج الرايات الترحيبية للخدمات عبر `telnet` و `nc`.
   * الفحص الأولي لخوادم الويب عبر أدوات المطورين وسطر الأوامر.

3. [03. البروتوكولات والخوادم - الجزء الأول (Protocols and Servers)](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/03-PROTOCOLS-AND-SERVERS/README_AR.md)
   * تشريح بروتوكول نقل النص الفائق (HTTP) وأكواد الحالة.
   * بروتوكول نقل الملفات (FTP) ومخاطر الدخول المجهول (Anonymous Login).
   * بروتوكولات البريد الإلكتروني الثلاثة: SMTP للإرسال، و POP3 و IMAP للاستقبال.

4. [04. البروتوكولات والخوادم - الجزء الثاني (Protocols and Servers 2)](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/04-PROTOCOLS-AND-SERVERS-2/README_AR.md)
   * مخاطر حركة المرور غير المشفرة (Cleartext Traffic) واعتراضها عبر هجمات رجل في المنتصف (MitM).
   * التحول الآمن نحو التشفير: بروتوكول Secure Shell (SSH) واستبدال Telnet.
   * بروتوكولات الأمان SSL و TLS، الشهادات الرقمية، وتشفير الاتصالات الحساسة.

5. [05. تقييم الانتقال المعرفي (Topic Transition Recap)](file:///e:/THM-JR-PENETRATION-TESTER/03-NETWORK-RECONNAISSANCE/05-TOPIC-TRANSITION-RECAP/README_AR.md)
   * بنك أسئلة توليفي لاختبار الجاهزية قبل الانتقال إلى الموديول الرابع: فحص المنافذ المتخصص بأداة Nmap.
