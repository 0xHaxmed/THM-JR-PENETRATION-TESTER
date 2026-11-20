# المهمة 03: كشف نظام التشغيل وتتبع المسار (Task 03: OS Detection and Traceroute)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة تقنية **تحديد بصمة نظام التشغيل (Operating System Detection)** في أداة Nmap عبر المعامل **`-O`**، بالإضافة إلى تتبع مسار الحزم عبر الشبكة باستخدام **`--traceroute`**.

يعتمد كشف نظام التشغيل على حقيقة هندسية أساسية: على الرغم من أن كافة أنظمة التشغيل تتواصل وفقاً لنفس معايير الإنترنت القياسية (RFCs)، إلا أن كل فريق تطوير (سواء في مايكروسوفت أو لينكس أو آبل أو سيسكو) يقوم ببرمجة مكدس TCP/IP بطريقة برمجية مختلفة قليلاً، مما يترك "بصمة جينية فريدة" في الحزم المتبادلة.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### ميكانيكية أخذ البصمة الشبكية (TCP/IP Stack Fingerprinting):
* يرسل Nmap ما يصل إلى 16 حزمة استعلامية مصممة خصيصاً لاستفزاز مكدس الشبكة، وتشمل:
  1. حزم TCP SYN مع خيارات متنوعة لمنافذ مفتوحة ومغلقة.
  2. فحص نمط توليد الأرقام التسلسلية (Initial Sequence Number - ISN Predictability).
  3. فحص قيمة وقت البقاء الافتراضي (Time to Live - TTL) وقيمة حجم نافذة TCP (Window Size).
  4. فحص استجابة النظام لحزم ICMP وحزم UDP الشاذة.
* يقوم Nmap بتجميع الردود ومقارنتها مع قاعدة بيانات ضخمة تضم آلاف البصمات في ملف `nmap-os-db`.
* **الخيارات التكتيكية المساعدة:**
  * `--osscan-limit`: تخطي محاولة كشف نظام التشغيل إذا لم يتوفر على الأقل منفذ واحد مفتوح ومنفذ واحد مغلق (لأن غياب أحدهما يقلل دقة البصمة).
  * `--osscan-guess` أو `--fuzzy`: إجبار Nmap على تقديم أفضل تخمين تقريبي عند عدم وجود تطابق تام بنسبة 100%.

#### تحليل نتائج المعمل:
* عند فحص الآلة المعملية الهدف عبر الأمر:
  `sudo nmap -O MACHINE_IP`
* يُظهر التقرير في حقل `Running`:
  `Running: Linux 4.X|5.X`
  `OS details: Linux 4.15 - 5.6`
* أقرب نظام تشغيل متطابق وفقاً لتقرير Nmap هو نظام **`Linux`** (وليس Windows).

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* نفّذ الأمر بصلاحيات الجذر: `sudo nmap -O MACHINE_IP`.
* راجع أسطر `Device type`, `Running`, و `OS details` في مخرجات الفحص لاختيار النظام المطابق (Windows أم Linux).

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ فحص بصمة نظام التشغيل:
```bash
sudo nmap -O MACHINE_IP
```

#### مخرجات الفحص النموذجية المسجلة:
```text
Starting Nmap 7.9X ( https://nmap.org )
Nmap scan report for MACHINE_IP
Host is up (0.00035s latency).
Not shown: 96 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
111/tcp open  rpcbind
143/tcp open  imap
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6, Linux 5.0 - 5.4
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.48 seconds
```

---

### 5. الإجابة النموذجية المعتمدة (Stage E: Final Answer)
* **السؤال:** Per nmap, which one is the closest OS match after running with the nmap with -O option against MACHINE_IP ? (Windows/Linux)
* **الإجابة:** `Linux`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استخراج البصمة `Linux 4.X|5.X` وتأكيدها عبر مخرجات Nmap.
* مطابقة الإجابة بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* تحديد نظام التشغيل بدقة يحسم مسار الهجوم؛ فاختيار حمولات الاستغلال (Payloads)، وتجميع البرمجيات الخبيثة، وأوامر تصعيد الصلاحيات (Privilege Escalation) تختلف جذرياً بين أنظمة لينكس وأنظمة ويندوز.
