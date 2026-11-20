# Task 03: Whois
## المهمة 03: استعلام سجلات ملكية وتسجيل النطاقات بروتوكول WHOIS

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 03
* **Original Title:** Whois
* **Questions:**
  1. *When was TryHackMe.com registered?*
  2. *What is the registrar of TryHackMe.com?*
  3. *Which company is TryHackMe.com using for name servers?*
* **Objective:** Understand the WHOIS protocol (RFC 3912, TCP port 43), query regional internet registries (RIRs) and domain registrars, and extract critical administrative metadata.
* **Core Concepts:** WHOIS Protocol, Registrars, Registration Dates, Name Servers, Privacy Protections (WhoisGuard).

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل الغرض من أداة Whois:**
  يقوم بروتوكول WHOIS بالاتصال بخوادم السجلات المركزية (Registrars مثل Namecheap, GoDaddy) على المنفذ `43/TCP` لجلب بيانات تسجيل النطاق.
* **تحليل الأسئلة حول النطاق `tryhackme.com`:**
  1. تاريخ التسجيل (Creation Date / Registered on): متى تم حجز النطاق لأول مرة؟
  2. مسجل النطاق (Registrar): ما هي الشركة التجارية التي تم شراء النطاق من خلالها؟
  3. خوادم الأسماء (Name Servers): ما هي الشركة المزودة لخدمات استضافة الـ DNS (NS) التي تدير سجلات الموقع؟

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسؤال 1):** التاريخ بصيغة أرقام متصلة YYYYMMDD: السنة 2018، الشهر 07، واليوم 05: `20180705`.
* **Hint 2 (للسؤال 2):** شركة مسجل النطاقات الشهيرة ومقرها أمريكا: `namecheap.com`.
* **Hint 3 (للسؤال 3):** شركة خدمات السحابة وحماية الـ CDN الشهيرة: `cloudflare.com`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **تنفيذ أمر الاستعلام عبر الطرفية:**
   ```bash
   whois tryhackme.com
   ```
2. **تحليل مخرجات سجلات WHOIS الحقيقية:**
   ```text
   Domain Name: TRYHACKME.COM
   Registry Domain ID: 2282767073_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.namecheap.com
   Registrar URL: http://www.namecheap.com
   Updated Date: 2023-06-05T14:41:40Z
   Creation Date: 2018-07-05T19:46:15Z
   Registry Expiry Date: 2028-07-05T19:46:15Z
   Registrar: NameCheap, Inc.
   Registrar IANA ID: 1068
   Name Server: ARCHER.NS.CLOUDFLARE.COM
   Name Server: DINA.NS.CLOUDFLARE.COM
   ```
3. **استخراج الإجابات النموذجية المعتمدة:**
   * **تاريخ التسجيل:** 5 يوليو 2018 ⟶ بصيغة الأرقام المعتمدة في THM: `20180705`.
   * **مسجل النطاق (Registrar):** `namecheap.com`.
   * **شركة خوادم الأسماء (Name Servers):** `cloudflare.com` (مشغلة عبر `cloudflare.com`).

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *When was TryHackMe.com registered?*
  * **Answer:** `20180705`
* **Question 2:** *What is the registrar of TryHackMe.com?*
  * **Answer:** `namecheap.com`
* **Question 3:** *Which company is TryHackMe.com using for name servers?*
  * **Answer:** `cloudflare.com`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```bash
whois tryhackme.com | grep -E -i "Creation Date|Registrar:|Name Server:"
# Output:
# Creation Date: 2018-07-05T19:46:15Z
# Registrar: NameCheap, Inc.
# Name Server: ARCHER.NS.CLOUDFLARE.COM
# Name Server: DINA.NS.CLOUDFLARE.COM
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استعلام WHOIS:**
> *"WHOIS reveals the administrative ecosystem; identifying Cloudflare as the nameserver tells the tester immediately that direct IP discovery will be required to bypass the WAF."*
> استعلام WHOIS يكشف المنظومة الإدارية للنطاق؛ ومعرفة أن الهدف يستخدم Cloudflare كخوادم أسماء ينبه المختبر فوراً إلى أن الهجمات ستصطدم بجدار الحماية، وأن عليه البحث عن عنوان الـ IP الحقيقي للخادم الأصلي (Origin IP).
