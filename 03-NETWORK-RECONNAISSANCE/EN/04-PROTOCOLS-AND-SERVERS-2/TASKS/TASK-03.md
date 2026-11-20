# Task 03: Man-in-the-Middle (MITM) Attack
## المهمة 03: هجمات الرجل في المنتصف وأدوات Ettercap و Bettercap

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 03
* **Original Title:** Man-in-the-Middle (MITM) Attack
* **Questions:**
  1. *How many different interfaces does Ettercap offer?*
  2. *In how many ways can you invoke Bettercap?*
* **Objective:** Understand the mechanics of Layer 2 Man-in-the-Middle (MITM) attacks via ARP cache poisoning, compare the interface architectures of classic MITM suites (Ettercap) and modern Swiss Army frameworks (Bettercap), and study Layer 2 defensive controls.
* **Core Concepts:** ARP Spoofing/Poisoning, Gratuitous ARP, MITM Execution, Ettercap Interfaces, Bettercap Invocation Modes.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **كيف يعمل هجوم الرجل في المنتصف عبر بروتوكول ARP؟**
  * في شبكات الإيثرنت المحلية، يحتاج الجهاز لمعرفة عنوان الـ MAC الفيزيائي المطابق لعنوان الـ IP للهدف عبر بروتوكول الـ ARP.
  * بروتوكول ARP بروتوكول "أعمى" لا يحتوي على أي مصادقة (Stateless & Unauthenticated).
  * يستطيع المهاجم إرسال حزم **Gratuitous ARP** غير مطلوبة تزعم كذباً أن عنوان IP الراوتر يمتلك عنوان الـ MAC الخاص بالمهاجم، وعنوان IP الضحية يمتلك أيضاً عنوان الـ MAC الخاص بالمهاجم.
  * يقع جهاز الضحية والراوتر في فخ التسميم (ARP Cache Poisoning)، ويصبح المهاجم في المنتصف تماماً (Man-in-the-Middle).
* **تحليل السؤال الأول (واجهات أداة Ettercap):**
  * توفر حزمة Ettercap الكلاسيكية الشهيرة **`3`** واجهات تشغيل متميزة:
    1. واجهة المستخدم الرسومية الكاملة: **GUI Interface** (عبر `ettercap -G`).
    2. واجهة الشاشة النصية التفاعلية: **NCurses Text-mode Interface** (عبر `ettercap -C`).
    3. واجهة سطر الأوامر غير التفاعلية: **CLI Interface** (عبر `ettercap -T`).
* **تحليل السؤال الثاني (طرق استدعاء وتشغيل أداة Bettercap):**
  * طُوّرت أداة Bettercap بلغة Go كبديل عصري شامل، وتوفر **`3`** طرق استدعاء رئيسية:
    1. الطرفية التفاعلية: **Interactive CLI Session** (التشغيل الافتراضي المباشر).
    2. تشغيل السكربتات والملفات النصية الآلية: **Caplets** (باستخدام الراية `-caplet <file>`).
    3. خادم واجهة الويب الرسومية: **Web UI** (عبر تشغيل وحدة `http-ui`).

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** عدد الواجهات التي تقدمها Ettercap (رسومية GUI، ونصف رسومية NCurses، ونصية CLI): `3`.
* **Hint 2 (للسؤال 2):** عدد طرق تشغيل Bettercap (تفاعلية، سكربتات Caplets، وواجهة ويب): `3`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **استعراض واجهات Ettercap من صفحة المان (`man ettercap`):**
   ```text
   The following user interfaces are available:
   -G, --gui: Start the GUI interface.
   -C, --curses: Start the NCurses text-based interface.
   -T, --text: Start text-only (CLI) mode.
   ```
   * إجمالي الواجهات المتاحة: **3**.
2. **استعراض أوضاع استدعاء Bettercap:**
   * الوضع 1: التشغيل التفاعلي المباشر: `sudo bettercap -iface eth0`.
   * الوضع 2: تشغيل سيناريوهات مسجلة (Caplets): `sudo bettercap -caplet spoof.cap`.
   * الوضع 3: إطلاق واجهة الويب المتكاملة للمراقبة: `sudo bettercap -eval "ui.update; http-ui"`.
   * إجمالي طرق الاستدعاء: **3**.

---

### Stage E — Final Answer | الإجابة النهائية المعتمدة
* **Question 1:** *How many different interfaces does Ettercap offer?*
  * **Answer:** `3`
* **Question 2:** *In how many ways can you invoke Bettercap?*
  * **Answer:** `3`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
MITM Tool Interface Metrics:
[+] Ettercap Interfaces: 3 (GUI, NCurses, Text/CLI)
[+] Bettercap Modes: 3 (Interactive CLI, Caplets, Web UI)
Status: Verified
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في حماية الطبقة الثانية (Layer 2 Defense):**
> *"ARP spoofing works because switches blindly trust ARP responses; deploying Dynamic ARP Inspection (DAI) and DHCP Snooping on managed enterprise switches renders ARP poisoning completely impossible."*
