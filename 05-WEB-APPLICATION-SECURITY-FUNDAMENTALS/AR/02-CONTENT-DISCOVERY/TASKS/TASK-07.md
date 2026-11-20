# المهمة 07: الاستكشاف الآلي - النطاقات الفرعية والمجالات الافتراضية (Task 07: Automated Discovery - Subdomains & Virtual Hosts)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة التمييز الدقيق بين نوعين متقدمين من أهداف الاستكشاف الشبكي:
1. **النطاقات الفرعية (Subdomains):**
   * نطاقات مسجلة في خوادم نظام أسماء النطاقات (DNS) وتشير إلى عناوين IP مستقلة أو مشتركة (مثل `admin.example.com` أو `mail.example.com`).
   * تُكتشف باستخدام وضع DNS في أداة Gobuster (`gobuster dns`).
2. **المجالات الافتراضية (Virtual Hosts - VHosts):**
   * مواقع متعددة ومستقلة يستضيفها خادم ويب واحد بنفس عنوان الـ IP الواحد!
   * يعتمد خادم الويب (Apache أو Nginx) في التوجيه الداخلي على قيمة **ترويسة المضيف (`Host: Header`)** في طلب HTTP.
   * تُكتشف باستخدام وضع VHost في أداة Gobuster (`gobuster vhost`).

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)

#### السؤال 1: المعامل المختصر الإلزامي في وضع `gobuster dns`
* عند تشغيل وضع استكشاف النطاقات الفرعية عبر الـ DNS:
  `gobuster dns -d DOMAIN_NAME -w WORDLIST`
* المعامل المختصر المطلوب لتحديد النطاق الأساسي المستهدف هو **`-d`** (مختصر Domain).

#### السؤال 2: عدد المجالات الافتراضية المستجيبة بكود 200 على `acmeitsupport.thm`
* في سيناريو المعمل، تمت تهيئة خادم الويب على النطاق المحلي `acmeitsupport.thm` لاستضافة عدة مواقع افتراضية مخفية.
* عند تشغيل فحص الـ VHost عبر Gobuster مع ترشيح النتائج وتضمين النطاق:
  `gobuster vhost -u http://acmeitsupport.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain`
* يكتشف Gobuster وجود **`3`** مجالات افتراضية نشطة تستجيب بكود النجاح **`200 OK`** (مثل `admin`, `delta`, `switch`).

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* ما هو الحرف المختصر لتحديد النطاق في وضع `gobuster dns`؟ الحرف هو `-d`.
* نفّذ فحص الـ VHost واحسب عدد المجالات التي ترجع كود الحالة (Status: 200).

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### الخطوة 1: فحص النطاق المختصر في وضع DNS
```bash
# صيغة استكشاف النطاقات الفرعية بالـ DNS
gobuster dns -d example.com -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```
المعامل المطلوب للنطاق هو **`-d`**.

#### الخطوة 2: فحص المجالات الافتراضية (VHost Scan)
تأكد أولاً من إضافة اسم النطاق `acmeitsupport.thm` إلى ملف `/etc/hosts`:
```bash
echo "MACHINE_IP acmeitsupport.thm" | sudo tee -a /etc/hosts
```
ثم نفّذ أمر فحص الـ VHosts:
```bash
gobuster vhost -u http://acmeitsupport.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```
مخرجات الفحص تسجل استجابة 3 مجالات افتراضية بكود 200:
```text
Found: admin.acmeitsupport.thm Status: 200 [Size: 286]
Found: delta.acmeitsupport.thm Status: 200 [Size: 512]
Found: switch.acmeitsupport.thm Status: 200 [Size: 348]
```
إجمالي عدد المجالات الافتراضية المستجيبة بكود 200 هو **`3`**.

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** Apart from dns and -w , which shorthand flag is required for dns mode?
   * **الإجابة:** `-d`
2. **السؤال 2:** How many virtual hosts on acmeitsupport.thm respond with status code 200?
   * **الإجابة:** `3`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* توثيق خيار `-d` في الدليل المرجعي لأداة Gobuster.
* مطابقة المجالات الافتراضية الثلاثة المكتشفة مع السجل المعملي الحقيقي.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* فحص الـ Virtual Hosts هو أحد أسرار مختبري الاختراق المحترفين؛ فكثير من المنظمات تشغل بيئات تطوير واختبار داخلية غير محمية على نفس خادم الإنتاج دون تسجيلها في الـ DNS العام، وتغيير ترويسة `Host:` هو السبيل الوحيد لاصطيادها واختراقها.
