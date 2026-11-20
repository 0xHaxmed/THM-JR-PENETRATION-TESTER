# المهمة 08: الخلاصة والجدول المرجعي للمفاهيم الأساسية (Task 08: Summary)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تختتم هذه المهمة الغرفة بتقديم جدول مرجعي وتلخيصي شامل لكافة تقنيات فحص المنافذ الأساسية في أداة Nmap، والمعاملات المسؤولة عن التحكم في نطاق المنافذ وأداء التوقيت، تمهيداً للانتقال إلى تقنيات فحص المنافذ المتقدمة والتخفي (Nmap Advanced Port Scans).

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)
* **المصفوفة التكتيكية لفحص المنافذ الأساسي (Basic Port Scanning Matrix):**

| نوع الفحص | معامل Nmap | البروتوكول | متطلبات الصلاحية | الاستجابة عند الفتح (Open) | الاستجابة عند الإغلاق (Closed) | البصمة في السجلات |
|---|---|---|---|---|---|---|
| **TCP Connect Scan** | `-sT` | TCP | متاح للجميع (Unprivileged) | يكمل المصافحة الثلاثية (`SYN-ACK` ثم `ACK`) | يستلم `RST, ACK` | عالية جداً (يسجل في Application Logs) |
| **TCP SYN / Stealth** | `-sS` | TCP | يتطلب Root / Sudo | يستلم `SYN, ACK` ويرد بـ `RST` | يستلم `RST, ACK` | متوسطة / منخفضة (يتفادى Application Logs) |
| **UDP Port Scan** | `-sU` | UDP | يتطلب Root / Sudo | استجابة UDP أو لا استجابة (`open\|filtered`) | خطأ `ICMP Type 3 Code 3 (Port Unreachable)` | منخفضة ولكن فحص بطيء بسبب ICMP Limiting |

* **جدول خيارات التحكم في النطاق والتوقيت:**
  * `-p-`: فحص كافة المنافذ (1-65535).
  * `-p 22,80,443`: فحص منافذ محددة.
  * `-p 5000-5500`: فحص نطاق متصل.
  * `-F`: فحص سريع لأشهر 100 منفذ.
  * `-r`: فحص المنافذ بالتسلسل بدلاً من الترتيب العشوائي الافتراضي.
  * `-T0` إلى `-T5`: قوالب التوقيت من الارتياب الشديد حتى السرعة الفائقة.
  * `--min-parallelism <num>`: الحد الأدنى للعمليات المتوازية.
  * `--min-rate <num>`: الحد الأدنى لمعدل إرسال الحزم في الثانية.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* دوّن جدول المقارنة بين Connect Scan و SYN Scan و UDP Scan.
* استعد للانتقال إلى الغرفة الثالثة: `Nmap Advanced Port Scans` لمعرفة كيفية التلاعب برايات Null و FIN و Xmas وفحص الخمول.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)
تأكيد استيعاب المفاهيم واستكمال الغرفة بنجاح:
```bash
# أمر فحص قياسي يجمع بين الكفاءة والتوقيت المناسب
sudo nmap -sS -sU -p T:21-25,80,443,U:53,161 -T4 MACHINE_IP
```

---

### 5. الإجابة النموذجية المعتمدة (Stage E: Final Answer)
* **السؤال:** Ensure you have noted all the scan options covered in this room. It is time to learn advanced port-scanning techniques by joining the Nmap Advanced Port Scans room.
* **الإجابة:** `No answer needed`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استكمال كافة مهام الغرفة الثماني بنجاح 100% على منصة TryHackMe.
* الجاهزية التامة للغرفة التالية `nmap03`.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* أصبح لديك الآن الأساس الراسخ لفهم كيف "يفكر" Nmap أثناء فحص المنافذ، وماذا يعني كل رد شبكي، وكيف تختار نوع الفحص المناسب لبيئة العمل وصلاحياتك المتاحة.
