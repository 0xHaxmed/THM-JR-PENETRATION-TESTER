# Task 04: Installation (تثبيت وتشغيل منصة Burp Suite)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Burp Suite is a cross-platform Java application compatible with Linux, Windows, and macOS:

- **Pre-Installed Environments:**
  Burp Suite Community Edition comes pre-installed on Kali Linux, Parrot OS, and the TryHackMe AttackBox. It can be launched directly from the terminal via the `burpsuite` command or through the desktop application menu under Web Application Analysis.
- **Manual Installation:**
  Installers can be downloaded directly from the official PortSwigger website (`https://portswigger.net/burp/communitydownload`).
  - On Windows: standard executable setup wizard (`.exe`).
  - On Linux: shell script installer (`.sh`), which typically installs into `/opt/burpsuitecommunity` (if run as root) or `~/BurpSuiteCommunity/BurpSuiteCommunity` (if run as a regular user).
  - On macOS: DMG disk image installer compatible with Intel and Apple Silicon (M-series).
- **Java Runtime Requirements:**
  Modern Burp Suite installers bundle an embedded OpenJDK Java Runtime Environment (JRE), eliminating the need to manually install or configure separate Java dependencies on the host system.

#### الأسئلة المطروحة (Questions):
1. If you have chosen not to use the AttackBox, ensure that you have a copy of Burp Suite installed before proceeding. (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تخصيص موارد الذاكرة للبرنامج (Java Heap Memory Allocation):**
  * نظراً لأن Burp Suite مكتوب بلغة جافا، فإنه قد يستهلك ذاكرة وصول عشوائي (RAM) كبيرة عند تصفح مواقع ضخمة تحتوي على آلاف الموارد.
  * في المشاريع الكبيرة، يفضل تشغيل البرنامج مع تخصيص ذاكرة إضافية (Java Heap) عبر المعامل:
    `java -Xmx4g -jar burpsuite.jar` لمنع انهيار البرنامج بسبب نفاد الذاكرة (Out of Memory).
* **إدارة مسارات التثبيت:**
  * تشغيل Burp Suite كمستخدم عادي (Non-root) هو الأسلوب الآمن هندسياً لتفادي تشغيل متصفح Chromium المدمج بصلاحيات root، وهو ما يتطلب تعطيل ميزات الحماية (Sandbox).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة تأكيدية للتحقق من وجود البرنامج على بيئة العمل الخاصة بك أو استخدام AttackBox.
2. **التلميح الثاني (المستوى العملي):**
   * إذا كنت تعمل على نظام لينكس، افتح الطرفية واكتب `burpsuite` للتأكد من تشغيله.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * السؤال لا يتطلب أي مدخلات نصية؛ اضغط على Submit للمتابعة.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشغيل Burp Suite في بيئات مختلفة:
```bash
# 1. تشغيل Burp Suite من الطرفية في كالي لينكس أو AttackBox
burpsuite &

# 2. التحقق من مسار التثبيت المحلي على لينكس
which burpsuite
# المخرجات: /usr/bin/burpsuite
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تأكيد تشغيل البرنامج بنجاح.
* تم اعتماد السؤال بنجاح على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
توفر حزم التثبيت الحديثة لمنصة Burp Suite بيئة جافا مدمجة بالكامل، مما يسهل تشغيلها ونشرها عبر مختلف منصات التشغيل (ويندوز، لينكس، ماك) دون مواجهة تعارضات في حزم النظام.
