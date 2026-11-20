# Module 03: Topic Transition Recap
## أسئلة المراجعة الشاملة والتقييم الانتقالي للوحدة الثالثة: استطلاع الشبكات (Network Reconnaissance)

* **Module:** Module 03: Network Reconnaissance
* **Recap URL:** `https://tryhackme.com/recap/6afe01d5f306fc3f65927f02`
* **Objective:** دمج وتقييم المعارف المكتسبة عبر غرف استطلاع الشبكات الأربع (Passive Reconnaissance, Active Reconnaissance, Protocols & Servers, Protocols & Servers 2) قبل الانتقال إلى الوحدة الرابعة المتخصصة بأداة Nmap.

---

### 📋 أسئلة التقييم الانتقالي الرسمي من TryHackMe (Topic Transition Assessment)

#### السؤال 1: سجلات نظام أسماء النطاقات لخوادم البريد (DNS MX Records)
* **السؤال:** Which DNS record type identifies the servers responsible for handling a domain's email?
* **الخيارات المتاحة:** `MX`, `CNAME`, `TXT`, `SOA`, `A`
* **الإجابة النموذجية:** `MX`
* **الشرح الفني والأكاديمي:**
  * سجل **MX (Mail Exchanger)** هو السجل المخصص في خوادم الـ DNS لتحديد خوادم البريد الإلكتروني المسؤولة عن استقبال الرسائل للنطاق المحدد، ويرتبط بكل خادم رقم أولوية (Preference / Priority)؛ حيث يفضل الخادم ذو الرقم الأقل (مثل Priority 10 يسبق Priority 20).
  * سجل `A` يربط الاسم بعنوان IPv4.
  * سجل `CNAME` يمثل اسماً مستعاراً (Alias) يشير لاسم نطاق آخر.
  * سجل `TXT` يحمل بيانات نصية عشوائية وتُستخدم عادة لسياسات الأمان مثل SPF و DKIM و DMARC.
  * سجل `SOA` (Start of Authority) يحدد المعلمات الأساسية لمنطقة الـ DNS الإدارية.

---

#### السؤال 2: انتهاك سرية البيانات عبر التقاط الحزم بنص صريح (Cleartext Sniffing & CIA Triad)
* **السؤال:** Capturing cleartext credentials from a network with a packet sniffer primarily violates which CIA property?
* **الخيارات المتاحة:** `Non-repudiation`, `Availability`, `Accountability`, `Confidentiality`, `Integrity`
* **الإجابة النموذجية:** `Confidentiality` (السرية)
* **الشرح الفني والأكاديمي:**
  * يرتكز أمن المعلومات على ثالوث CIA: **السرية (Confidentiality)**، **السلامة والتكامل (Integrity)**، و**التوافر (Availability)**.
  * عند قيام مهاجم بالتنصت السلبي على حركة المرور (Packet Sniffing) والتقاط بيانات اعتماد غير مشفرة (مثل بيانات تسجيل الدخول في بروتوكولات Telnet, FTP, HTTP, IMAP غير المشفر)، فإنه يطّلع على بيانات غير مصرح له برؤيتها، وهذا يمثل انتهاكاً مباشراً وصريحاً لخاصية **السرية (Confidentiality)** دون التأثير الفوري على سلامة البيانات المرسلة (Integrity) أو تعطيل عمل الخادم (Availability).

---

#### السؤال 3: الفارق الجوهري بين الاستطلاع النشط والسلبي (Active vs Passive Reconnaissance)
* **السؤال:** What is the primary difference between active and passive reconnaissance?
* **الخيارات المتاحة:**
  1. Passive reconnaissance requires specialized hardware; active reconnaissance does not
  2. Active sends packets to the target; passive gathers public data only
  3. Passive reconnaissance only targets web servers; active reconnaissance targets all ports
  4. Active reconnaissance is always automated; passive reconnaissance is always manual
* **الإجابة النموذجية:** `Active sends packets to the target; passive gathers public data only`
* **الشرح الفني والأكاديمي:**
  * **الاستطلاع السلبي (Passive Reconnaissance):** يعتمد حصرياً على جمع المعلومات المتاحة علناً من مصادر وسيطة خارجية ومستودعات عامة (مثل Whois, DNSDumpster, Shodan, Censys, VirusTotal) دون إرسال حزمة بيانات واحدة مباشرة إلى خوادم أو بنية الضحية التحتية، مما يجعل اكتشافه مستحيلاً في سجلات الهدف.
  * **الاستطلاع النشط (Active Reconnaissance):** يتطلب الاتصال المباشر بنظام الهدف عبر إرسال حزم استعلامية (مثل ICMP Echo Requests, TCP SYN packets, HTTP requests, Traceroute UDP/ICMP)، مما يولّد أثراً رقمياً وسجلات اتصال (Logs) وتنبيهات محتملة في أجهزة كشف التسلل (IDS/IPS).

---

#### السؤال 4: مستوى أمان بروتوكول التشفير العتيق SSL 3.0
* **السؤال:** SSL 3.0 is still an acceptable choice today provided strong cipher suites are configured. (True / False)
* **الإجابة النموذجية:** `False`
* **الشرح الفني والأكاديمي:**
  * بروتوكول SSL 3.0 (وكذلك SSL 2.0 و TLS 1.0 و TLS 1.1) عفا عليه الزمن ومصنف رسمياً كبروتوكول غير آمن وممنوع الاستخدام (Deprecated) بموجب RFC 7568 في عام 2015 نتيجة احتوائه على ثغرات هيكلية قاتلة مثل هجوم POODLE (Padding Oracle On Downgraded Legacy Encryption - CVE-2014-3566).
  * لا يمكن لأي أجنحة تشفير (Cipher Suites) مهما بلغت قوتها حماية SSL 3.0 لأن الضعف كامن في التصميم البنائي لآلية حشو الـ CBC الخاصة بالبروتوكول نفسه. المعايير المعاصرة تشترط استخدام TLS 1.2 أو TLS 1.3 كحد أدنى.

---

#### السؤال 5: مقاومة سياسة HSTS لهجمات تجريد التشفير (SSL Stripping Defeat)
* **السؤال:** A browser that has recorded an HSTS policy for a site refuses plain HTTP, defeating which attack?
* **الخيارات المتاحة:** `Denial of service`, `Credential stuffing`, `Offline hash cracking`, `Account lockout abuse`, `SSL stripping`
* **الإجابة النموذجية:** `SSL stripping`
* **الشرح الفني والأكاديمي:**
  * هجوم **SSL Stripping** (الذي ابتكره Moxie Marlinspike) يعتمد على قيام وكيل وسيط (MitM Proxy) باعتراض طلبات الترقية أو الروابط الموجهة عبر HTTPS بين المستخدم والخادم، واستبدالها بنسخ HTTP بنص صريح لخداع الضحية ومصادرة بياناته.
  * عندما يعلن الموقع عن سياسة **HSTS (HTTP Strict Transport Security)** عبر ترويسة `Strict-Transport-Security: max-age=...`، يقوم المتصفح بتسجيل هذا النطاق ويرفض رفضاً قاطعاً إرسال أي طلبات HTTP بنص صريح ويقوم بالترقية محلياً وتلقائياً إلى HTTPS (Internal 307 Redirect) قبل خروج الحزمة للشبكة، مما يُبطل هجوم SSL Stripping تماماً.

---

#### السؤال 6: منهجية عمل منصة DNSDumpster
* **السؤال:** DNSDumpster finds subdomains by brute-forcing candidate names against the target's DNS servers. (True / False)
* **الإجابة النموذجية:** `False`
* **الشرح الفني والأكاديمي:**
  * منصة **DNSDumpster** هي أداة استطلاع سلبي (Passive Reconnaissance Tool) تعتمد على الاستعلام من قواعد البيانات التاريخية، واستخراج السجلات من مصادر شهادات الأمان (Certificate Transparency Logs)، وفهارس محركات البحث، وجداول الـ DNS المجمعة مسبقاً.
  * لا تقوم DNSDumpster مطلقاً بتخمين النطاقات الفرعية (Brute-forcing) ضد خوادم الضحية المباشرة؛ لأن ذلك يمثل نشاطاً نشطاً (Active Reconnaissance) ينتهك طبيعة المنصة السلبية ويسبب حظراً لعنوان الـ IP الخاص بها.

---

### 💡 الخلاصة الربطية للوحدة الثالثة
تؤكد هذه الأسئلة الستة التزام الوحدة بتمكين الدارس من:
1. التمييز الدقيق بين متى يكون الاستطلاع غير مرئي (Passive) ومتى يكون نشطاً وصاخباً (Active).
2. فحص البنية التحتية والبروتوكولات القديمة (HTTP, FTP, Telnet, POP3, IMAP) التي تكشف أسرار الشبكات للمتنصتين.
3. تقدير آليات الحماية المعاصرة (TLS 1.3, HSTS) في حماية خصوصية وسرية الاتصالات الرقمية، مما يمهد للانتقال إلى أقوى أداة فحص شبكات في العالم: **Nmap**.
