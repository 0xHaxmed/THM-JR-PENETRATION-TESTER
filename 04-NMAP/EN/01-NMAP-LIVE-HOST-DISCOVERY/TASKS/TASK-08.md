# المهمة 08: استعلامات نظام أسماء النطاقات العكسية (Task 08: Using Reverse-DNS Lookup)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تتناول هذه المهمة دور استعلامات الـ DNS العكسية **Reverse-DNS Lookup (PTR Records)** أثناء مرحلة استكشاف الأجهزة في Nmap.

* في الوضع الافتراضي، يقوم Nmap بإجراء استعلام DNS عكسي (Reverse Lookup) **فقط للأجهزة التي يثبت أنها نشطة وحية (Online Hosts)** بعد اكتمال مرحلة الـ Host Discovery.
* يوفر Nmap خيارين أساسيين للتحكم في هذا السلوك:
  1. **`-n` (Never do reverse DNS resolution):** يُعطّل تماماً الاستعلامات العكسية لكافة العناوين، مما يرفع سرعة الفحص بدرجة كبيرة ويمنع إرسال طلبات DNS تفضح عنوان فحص المختبر لمسؤولي خوادم الـ DNS.
  2. **`-R` (Always resolve reverse DNS):** يفرض على Nmap إرسال استعلام DNS عكسي لكل عنوان IP في النطاق المستهدف، **حتى للأجهزة التي لم تستجب لفحص الاستكشاف وتبدو غير متصلة (Offline / Unresponsive Hosts)**.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)
* **القيمة الاستخباراتية لسجلات PTR للأجهزة الصامتة (OSINT via rDNS):**
  * في كثير من شبكات المؤسسات الكبرى، تكون الخوادم الحساسة (مثل وحدات التحكم في النطاق Domain Controllers، أو خوادم قواعد البيانات، أو بوابات الدخول الإدارية) محمية بجدران نارية صارمة تُسقط (Drop) كافة حزم ICMP و TCP/UDP pings، فتبدو لـ Nmap كأنها ميتة أو غير موجودة.
  * ولكن، قد يكون مهندس الشبكة قد سجل مسبقاً أسماء هذه الأجهزة في سجلات الـ PTR لخادم أسماء النطاقات الداخلي أو الخارجي (مثل `dc01.corp.local` أو `payroll-db.internal.net` أو `legacy-vpn.bank.com`).
  * باستخدام المعامل **`-R`**، يجبر المختبر Nmap على استجواب خادم الـ DNS عن كل عنوان في النطاق. حتى لو كان الخادم لا يستجيب على الشبكة، فإن الـ DNS يكشف اسمه ووظيفته ودوره في البنية التحتية، مما يوفر معلومات استخباراتية ذهبية لمرحلة الهجوم اللاحقة.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* ابحث عن الخيار المكون من حرف واحد مسبوقاً بشرطة (`-`) والمشتق من كلمة "Reverse".
* تذكر الفرق: `-n` يمنع الاستعلام نهائياً، بينما `-R` يفرضه على الجميع بلا استثناء.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### صياغة الأوامر في الاختبارات الميدانية:
```bash
# فرض الاستعلام العكسي لكافة الأجهزة حتى الخاملة منها
nmap -R -sn 10.10.210.0/24

# تحديد خادم DNS مخصص للاستعلامات العكسية لتجاوز الـ DNS المحلي
nmap -R -sn --dns-servers 10.10.10.1 10.10.210.0/24

# في المقابل: تسريع الفحص والعمل في وضع التخفي بتعطيل الـ DNS
nmap -n -sn 10.10.210.0/24
```

---

### 5. الإجابة النموذجية المعتمدة (Stage E: Final Answer)
* **السؤال:** We want Nmap to issue a reverse DNS lookup for every address in the target subnet, including hosts that don't respond to host discovery, hoping the names reveal something about each system. Which option forces this? (Answer format: one Nmap option, including its leading dash.)
* **الإجابة:** `-R`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* توثيق الخيار في توثيق Nmap الرسمي تحت تصنيف: `DNS RESOLUTION (-n, -R, --dns-servers)`.
* مطابقة الإجابة بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* سجلات الـ DNS العكسية تمثل تسريباً معلوماتياً قيماً لا يمكن تجاهله. جمع الأسماء عبر `-R` يتيح رسم خرائط تنظيمية للأنظمة الحساسة وتحديد بنية التسمية (Naming Conventions) للمؤسسة حتى قبل إرسال حزمة اختراق واحدة.
