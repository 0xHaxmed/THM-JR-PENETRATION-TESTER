# Task 02: Passive Versus Active Recon
## المهمة 02: التمييز العملي بين الاستطلاع السلبي والاستطلاع النشط

---

### Stage A — Original Task | تفاصيل المهمة الأصلية
* **Task Index:** Task 02
* **Original Title:** Passive Versus Active Recon
* **Questions:**
  1. *You visit the Facebook page of the target company, hoping to get some of their employee names. What kind of reconnaissance activity is this? (A for active, P for passive)*
  2. *You ping the IP address of the company webserver to check if ICMP traffic is blocked. What kind of reconnaissance activity is this? (A for active, P for passive)*
  3. *You happen to meet the IT administrator of the target company at a party. You try to use social engineering to get more information about their systems and network infrastructure. What kind of reconnaissance activity is this? (A for active, P for passive)*
* **Objective:** Clearly classify reconnaissance activities into Passive (no direct target interaction) or Active (direct interaction with target systems or personnel).
* **Core Concepts:** Passive vs. Active Boundaries, Direct Network Probing, Social Engineering Interaction.

---

### Stage B — Independent Thinking | التفكير المنهجي المستقل
* **تحليل السيناريو الأول (زيارة صفحة فيسبوك للشركة):**
  أنت تتصل بخوادم شركة Meta/Facebook لقراءة صفحة عامة. خوادم الشركة المستهدفة لم تستقبل أي حزمة منك، ولم تسجل عنوان IP الخاص بك في سجلاتها.
  * هذا استطلاع **سلبي (Passive - ويرمز له بـ P)**.
* **تحليل السيناريو الثاني (إرسال Ping لخادم الويب):**
  أنت ترسل حزم ICMP Echo Request مباشرة من جهازك إلى عنوان IP التابع لخادم الشركة المستهدفة. يستقبل جدار الحماية الحزم ويسجل عنوان مصدرك في سجلاته.
  * هذا استطلاع **نشط (Active - ويرمز له بـ A)**.
* **تحليل السيناريو الثالث (التحدث مع مسؤول تكنولوجيا المعلومات ومحاولة استدراجه):**
  هنا يوجد تفاعل وتواصل بشري مباشر وموجه ضد أحد موظفي الشركة المستهدفة لاستخراج معلومات سرية. هذا ليس قراءة لمعلومات عامة منشورة، بل تفاعل نشط يستهدف أصل بشري.
  * هذا استطلاع **نشط (Active - ويرمز له بـ A)**.

---

### Stage C — Guided Hints | التلميحات الإرشادية المتدرجة
* **Hint 1 (للسيناريو 1):** لم تتصل بأنظمة الهدف ⟶ حرف `P`.
* **Hint 2 (للسيناريو 2):** أرسلت حزم بيانات مباشرة لخوادم الهدف ⟶ حرف `A`.
* **Hint 3 (للسيناريو 3):** تفاعلت مباشرة مع الموظف لاستدراجه ⟶ حرف `A`.

---

### Stage D — Complete Practical Solution | الحل العملي والشرح التفصيلي
1. **معيار التمييز الذهبي:**
   * **Passive Recon (P):** إذا تعطلت أنظمة الشركة المستهدفة أو أغلقت خوادمها، يمكنك الاستمرار في جمع هذه البيانات لأنها مخزنة لدى أطراف ثالثة (Social Media, WHOIS, DNS Resolvers, Shodan).
   * **Active Recon (A):** إذا تعطلت أنظمة الشركة أو فُصل الاتصال بالهدف، لن تستطيع جمع هذه البيانات لأنها تتطلب إرسال حزم مباشرة (Ping, Port Scan) أو تفاعلاً بشرياً مباشراً (Social Engineering).
2. **الإجابات:**
   * السؤال 1: `P`
   * السؤال 2: `A`
   * السؤال 3: `A`

---

### Stage E — Final Answer | الإجابة النموذجية
* **Question 1:** *You visit the Facebook page of the target company...* ⟶ **Answer:** `P`
* **Question 2:** *You ping the IP address of the company webserver...* ⟶ **Answer:** `A`
* **Question 3:** *You happen to meet the IT administrator...* ⟶ **Answer:** `A`

---

### Stage F — Practical Verification & Evidence | التحقق العملي والأدلة
```text
Classification Verification:
[+] Scenario 1: Third-party web ingestion (Facebook) -> P (Passive)
[+] Scenario 2: Direct transport layer ICMP probe -> A (Active)
[+] Scenario 3: Direct interpersonal social engineering -> A (Active)
```

---

### Stage G — Key Educational Takeaway | الفائدة التعليمية المستدامة
> **قاعدة ذهبية في تصنيف الاستطلاع:**
> *"If your packets or your voice reach the target directly, you are active; if you consult a third party, you are passive."*
> إذا وصلت حزم بياناتك أو صوتك إلى الهدف مباشرة، فأنت في استطلاع نشط؛ وإذا استعلمت جهة ثالثة، فأنت في استطلاع سلبي.
