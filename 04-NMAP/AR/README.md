# الوحدة 04: فحص الشبكات واستكشاف المنافذ المتقدم (Nmap)

---

### 📌 مدخل إلى الوحدة الرابعة
تُعد أداة **Nmap (Network Mapper)** التي صممها *Gordon Lyon (Fyodor)* عام 1997 الأداة القياسية الأهم في ترسانة أي مختبر اختراق أو باحث أمني. لا تقتصر قوة Nmap على معرفة ما إذا كان المنفذ مفتوحاً أم مغلقاً، بل تمتد لتشريح سلوك مكدس بروتوكولات TCP/IP لكل نظام تشغيل، والتحايل على الجدران النارية، واكتشاف الثغرات المعقدة.

في هذه الوحدة، ننتقل من مرحلة الاستطلاع العام (Network Reconnaissance) إلى مرحلة الفحص المنهجي الدقيق لأهداف الشبكة (Vulnerability & Port Scanning)، وهو ما يمثل الخطوة التمهيدية الإلزامية قبل الشروع في استغلال الثغرات وتثبيت الأقدام في الأنظمة.

---

### 🗂️ فهرس غرف الموديول (Module Rooms Index)

1. [الغرفة 01: اكتشاف الأجهزة النشطة (Nmap Live Host Discovery)](../01-NMAP-LIVE-HOST-DISCOVERY/README_AR.md)
   * استكشاف الأجهزة الحية على الشبكة المحلية (ARP/ND) والشبكات الموجهة (ICMP Echo, Timestamp, Netmask, TCP SYN/ACK, UDP).
2. [الغرفة 02: فحص المنافذ الأساسي (Nmap Basic Port Scans)](../02-NMAP-BASIC-PORT-SCANS/README_AR.md)
   * الفحص بالمصافحة الثلاثية الكاملة (TCP Connect `-sT`)، فحص التخفي نصف المفتوح (TCP SYN `-sS`)، وفحص بروتوكول البيانات عديم الاتصال (UDP Port Scan `-sU`).
3. [الغرفة 03: فحص المنافذ المتقدم والتخفي (Nmap Advanced Port Scans)](../03-NMAP-ADVANCED-PORT-SCANS/README_AR.md)
   * الفحوصات غير المعيارية (Null, FIN, Xmas)، استغلال قواعد الجدران النارية عبر TCP ACK Scan، وفحص الخمول المتستر (Idle / Zombie Scan `-sI`).
4. [الغرفة 04: ما بعد فحص المنافذ ومحرك السكربتات (Nmap Post Port Scans)](../04-NMAP-POST-PORT-SCANS/README_AR.md)
   * تحديد إصدارات الخدمات بدقة عالية (`-sV`)، اكتشاف بصمة نظام التشغيل (`-O`)، استغلال سكربتات محرك NSE، وتصدير التقارير بجميع الصيغ (`-oA`).
5. [الغرفة 05: تقييم ختام الوحدة (Topic Transition Recap)](../05-TOPIC-TRANSITION-RECAP/README_AR.md)
   * مراجعة توليفية شاملة واختبار تقييمي للانتقال نحو موديول أمان تطبيقات الويب (Web Application Security).

---

### 🛠️ الأدوات والتقنيات المغطاة (Tools & Methodologies)
* **الأداة المركزية:** `nmap`
* **محللات الحزم المساعدة:** `wireshark`, `tcpdump`
* **المفاهيم الشبكية:** RFC 793 (TCP Control Bits), RFC 792 (ICMP), ARP Resolution, IP ID Increments, Raw Sockets, Firewall Traversal.
