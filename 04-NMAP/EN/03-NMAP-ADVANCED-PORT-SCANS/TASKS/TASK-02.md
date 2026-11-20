# المهمة 02: فحوصات Null و FIN و Xmas الشاذة (Task 02: TCP Null Scan, FIN Scan, and Xmas Scan)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة ثلاثة أنواع كلاسيكية من فحوصات المنافذ غير المعيارية في Nmap:
1. **فحص الحزمة الصفرية (TCP Null Scan `-sN`):** إرسال حزم TCP لا تحتوي على أي راية مفعلة على الإطلاق (عدد الرايات = $0$).
2. **فحص راية الإنهاء (TCP FIN Scan `-sF`):** إرسال حزم TCP مفعل فيها راية `FIN` فقط دون راية `ACK` ودون وجود اتصال سابق (عدد الرايات = $1$).
3. **فحص شجرة عيد الميلاد (TCP Xmas Scan `-sX`):** إرسال حزم مشتعلة برايات متعددة ومتناقضة في نفس اللحظة: `FIN` و `PSH` و `URG` (عدد الرايات = $3$).

تعتمد هذه الفحوصات الثلاثة على ثغرة تفسيرية ذكية في معيار بروتوكول TCP الصادر عن معهد مهندسي الكهرباء والإلكترونيات ومجموعة هندسة الإنترنت (RFC 793).

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### التشريح الرياضي والبروتوكولي لمعيار RFC 793 (الصفحة 65):
* ينص معيار RFC 793 حرفياً على القواعد التالية عند استلام حزمة شاذة لا تحتوي على رايات `SYN` أو `RST` أو `ACK`:
  1. **إذا كان المنفذ مغلقاً (CLOSED):**
     > "If the [destination] port state is CLOSED ... an incoming segment not containing a RST causes a RST to be sent in response."
     * يلتزم نظام تشغيل الهدف بإرسال حزمة **`RST`** فوراً.
  2. **إذا كان المنفذ مفتوحاً ويستمع (LISTEN / OPEN):**
     > "If the [destination] port state is LISTEN ... an incoming segment not containing a SYN, RST, or ACK is discarded."
     * يلتزم نظام التشغيل بـ **تجاهل الحزمة وإسقاطها بصمت تام (Discard / Drop)** دون إرجاع أي رد!

#### لماذا تظهر النتيجة في Nmap كـ `open|filtered`؟
* عندما يرسل Nmap حزمة Null أو FIN أو Xmas إلى منفذ:
  * إذا استلم حزمة `RST` $\rightarrow$ يعلم Nmap يقيناً أن المنفذ مغلق (`closed`).
  * إذا لم يستلم أي رد على الإطلاق (الصمت التام) $\rightarrow$ يعجز Nmap عن الجزم: هل المنفذ مفتوح وأسقط الحزمة تنفيذاً للمعيار RFC 793؟ أم أن هناك جدار حماية (Firewall) في الطريق قام بإسقاط الحزمة؟
  * لذلك يطلق Nmap الحالة المركبة: **`open|filtered`**.

#### التوافقية مع أنظمة التشغيل (The Microsoft Windows Flaw):
* الأنظمة المتوافقة مع RFC 793 (مثل Linux, FreeBSD, NetBSD, macOS) تتصرف بالطريقة السابقة بدقة.
* أنظمة **Microsoft Windows** وأنظمة أجهزة سيسكو (Cisco) **لا تلتزم بهذا المعيار**؛ حيث ترد أنظمة ويندوز بحزمة `RST` لكافة المنافذ سواء كانت مفتوحة أو مغلقة! ولذلك تفشل فحوصات Null/FIN/Xmas ضد أجهزة ويندوز وتُظهر جميع المنافذ كـ `closed`.

#### نتائج الفحص العملي في المعمل:
* عند فحص الآلة الهدف بنظام لينكس باستخدام فحص FIN:
  `sudo nmap -sF MACHINE_IP`
  تظهر **`9`** منافذ في حالة `open|filtered`.
* عند تكرار الفحص باستخدام فحص Null:
  `sudo nmap -sN MACHINE_IP`
  تظهر نفس الـ **`9`** منافذ في حالة `open|filtered`.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* تذكر أسماء الفحوصات: Null يعني لا شيء ($0$), FIN راية واحدة ($1$), Xmas ثلاث رايات مشتعلة كالأضواء ($3$).
* نفّذ أوامر الفحص ولاحظ عدد الأسطر التي تحمل حالة `open|filtered`.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### تنفيذ الفحوصات عبر الطرفية:
```bash
# 1. تنفيذ فحص FIN Scan
sudo nmap -sF MACHINE_IP

# مخرجات الفحص:
# PORT    STATE         SERVICE
# 21/tcp  open|filtered ftp
# 22/tcp  open|filtered ssh
# 53/tcp  open|filtered domain
# 80/tcp  open|filtered http
# ... (إجمالي 9 منافذ)

# 2. تنفيذ فحص Null Scan
sudo nmap -sN MACHINE_IP
# تظهر نفس النتيجة: 9 منافذ بحالة open|filtered
```

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** In a null scan, how many flags are set?
   * **الإجابة:** `0`
2. **السؤال 2:** In a FIN scan, how many flags are set?
   * **الإجابة:** `1`
3. **السؤال 3:** In a Xmas scan, how many flags are set?
   * **الإجابة:** `3`
4. **السؤال 4:** Launch a FIN scan against the target VM. How many ports appear as open|filtered?
   * **الإجابة:** `9`
5. **السؤال 5:** Repeat your scan launching a null scan against the target VM. How many ports appear as open|filtered?
   * **الإجابة:** `9`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* مطابقة السلوك لنصوص RFC 793 صفحة 65.
* مطابقة نتائج الفحص العملي للآلة المعملية وظهور 9 منافذ بحالة open|filtered.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* فحوصات Null و FIN و Xmas فعالة جداً في تجاوز الجدران النارية غير المعتمدة على الحالة (Stateless Firewalls) التي تركز فقط على حظر حزم `SYN`، ولكنها لا تميز بدقة بين المنفذ المفتوح والمفلتر بسبب تشابه استجابة الصمت التام.
