# المهمة 09: الخلاصة وجدول المعاملات التكتيكية (Task 09: Summary)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تختتم هذه المهمة الغرفة بتقديم جدول مرجعي وتلخيصي شامل لكافة تقنيات ومعاملات اكتشاف الأجهزة الحية (Live Host Discovery) في أداة Nmap، لتمكين المختبر من اختيار الاستراتيجية المثلى وفقاً لطبيعة بيئة الفحص والجدران النارية المعترضة.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)
* **المصفوفة التكتيكية لاكتشاف الأجهزة الحية (Host Discovery Tactical Matrix):**

| الخيار في Nmap | نوع الحزمة المرسلة | الطبقة الشبكية | البيئة المثالية للاستخدام | متطلبات الصلاحيات |
|---|---|---|---|---|
| `-sn` | فحص استكشافي فقط (No Port Scan) | عام | جميع عمليات الاستكشاف الأولية لتوفير الوقت | متاح للجميع |
| `-PR` | ARP Request | Layer 2 (Link) | الشبكات المحلية الإيثرنت (Local Subnet) | يتطلب Root / تلقائي |
| `-PE` | ICMP Echo Request (Type 8) | Layer 3 (Network) | الشبكات المفتوحة الموجهة دون جدران نارية صارمة | يتطلب Raw Sockets |
| `-PP` | ICMP Timestamp (Type 13) | Layer 3 (Network) | تجاوز الجدران النارية التي تحظر Echo فقط | يتطلب Raw Sockets |
| `-PM` | ICMP Address Mask (Type 17) | Layer 3 (Network) | تجاوز الجدران النارية الإرثية القديمة | يتطلب Raw Sockets |
| `-PS[ports]` | TCP SYN Packet | Layer 4 (Transport) | اكتشاف الأجهزة عبر منافذ الويب والخدمات الشائعة | يرسل SYN بحساب عادي أو root |
| `-PA[ports]` | TCP ACK Packet | Layer 4 (Transport) | تجاوز الجدران النارية غير المعتمدة على الحالة (Stateless) | يتطلب Root حصرياً |
| `-PU[ports]` | UDP Packet | Layer 4 (Transport) | استكشاف الأجهزة عبر منافذ UDP المغلقة (ICMP Unreachable) | يتطلب Raw Sockets لـ ICMP |
| `-n` | تعطيل استعلامات DNS | Application | تسريع الفحص والتخفي من سجلات الـ DNS | متاح للجميع |
| `-R` | فرض استعلامات DNS العكسية | Application | جمع الاستخبارات وتحديد مسميات الأجهزة الصامتة | متاح للجميع |

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* راجع الجدول التلخيصي واحفظ معاملات الـ Host Discovery الأساسية.
* استعد للانتقال إلى الغرفة التالية: `Nmap Basic Port Scans` للتعمق في فحص المنافذ.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)
تأكيد استيعاب المفاهيم وإتمام الغرفة بنجاح:
```bash
# أمر استكشاف شامل يجمع أفضل الممارسات على شبكة الهدف
sudo nmap -sn -PE -PP -PS22,80,443 -PA80,443 -PU53 -n 10.10.210.0/24
```

---

### 5. الإجابة النموذجية المعتمدة (Stage E: Final Answer)
* **السؤال:** Ensure you have noted all the Nmap options explained in this room. To continue learning about Nmap, please join the room Nmap Basic Port Scans, which introduces the basic types of port scans.
* **الإجابة:** `No answer needed`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استكمال 100% من مهام الغرفة التسع على منصة TryHackMe.
* جاهزية تامة للانتقال للغرفة الثانية `nmap02`.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* لم يعد استكشاف الأجهزة مجرد إرسال حزمة Ping عشوائية؛ بل أصبح علماً تكتيكياً يعتمد على اختيار البروتوكول المناسب (ARP محلياً، ICMP متعدداً، TCP/UDP عند الحظر) للتأكد من عدم تفويت أي خادم حي على الشبكة.
