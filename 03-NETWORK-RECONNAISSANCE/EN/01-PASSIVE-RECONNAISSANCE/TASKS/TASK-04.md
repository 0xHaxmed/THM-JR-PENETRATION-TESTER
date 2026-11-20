# Task 04: nslookup and dig
## المهمة 04: استعلام خوادم وسجلات الـ DNS بأداتي nslookup و dig

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 04
* **Original Title:** nslookup and dig
* **Questions:**
  1. *Check the TXT records of thmlabs.com. What is the flag there?*
* **Objective:** Master DNS interrogation utilities (`nslookup` and `dig`), query different DNS record types (A, AAAA, MX, TXT, CNAME, SOA), and extract hidden verification flags stored in TXT records.
* **Core Concepts:** Domain Name System (DNS), Record Types (TXT, MX, A), Recursive Resolvers, nslookup vs. dig Syntax.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السؤال:**
  المطلوب استعلام سجلات النص (TXT Records) الخاصة بالنطاق التابع للمعمل `thmlabs.com` واستخراج الراية الأمنية المخبأة داخله.
* **الفرق بين `nslookup` و `dig`:**
  * **`nslookup` (Name Server Lookup):** أداة كلاسيكية تعمل في نظامي ويندوز ولينكس، تتيح العمل في الوضع التفاعلي (Interactive Mode) أو سطر الأوامر.
  * **`dig` (Domain Information Groper):** أداة لينكس المفضلة لدى مختبري الاختراق ومهندسي الشبكات؛ توفر مخرجات دقيقة وتفصيلية وقابلة للمعالجة البرمجية عبر السكربتات (Parsing).
* **طرق استعلام سجلات الـ TXT:**
  1. عبر أداة `dig`:
     ```bash
     dig thmlabs.com TXT
     ```
  2. عبر أداة `nslookup`:
     ```bash
     nslookup -type=TXT thmlabs.com
     ```

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1:** استخدم خيار `-t TXT` مع dig أو `nslookup -type=TXT`.
* **Hint 2:** الراية تبدأ بـ `THM{a5b8392` وتتكون من تجزئة MD5 بالداخل.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **تنفيذ استعلام dig لاستخراج سجلات الـ TXT:**
   ```bash
   dig thmlabs.com TXT +short
   ```
2. **تحليل مخرجات سجلات الـ TXT للنطاق `thmlabs.com`:**
   ```text
   "THM{a5b83929888ed36acb0272971e438d78}"
   "v=spf1 -all"
   ```
3. **تفسير سجلات الـ TXT في أمن المعلومات:**
   * تُستخدم سجلات TXT عادة لإثبات ملكية النطاق لمحركات البحث، وتخزين سجلات مكافحة تزوير البريد الإلكتروني (SPF, DKIM, DMARC).
   * في هذا التمرين، استغل مسؤولو المعمل سجل TXT لتخزين راية التحقق الرسمية.

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *Check the TXT records of thmlabs.com. What is the flag there?*
  * **Answer:** `THM{a5b83929888ed36acb0272971e438d78}`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```bash
nslookup -type=TXT thmlabs.com
# Server:         127.0.0.53
# Address:        127.0.0.53#53
# Non-authoritative answer:
# thmlabs.com     text = "THM{a5b83929888ed36acb0272971e438d78}"
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في استعلامات DNS:**
> *"Never overlook TXT and SPF records; organizations frequently leave administrative tokens, API verification hashes, and internal server routing clues in plain sight."*
> لا تتجاهل أبداً سجلات TXT وسجلات SPF؛ فالشركات تترك فيها أحياناً رموز التحقق الإدارية ومفاتيح الربط السحابي ومسارات خوادم البريد الداخلية بشكل علني ومكشوف.
