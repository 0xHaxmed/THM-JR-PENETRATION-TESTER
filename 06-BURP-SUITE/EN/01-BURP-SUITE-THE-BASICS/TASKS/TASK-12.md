# Task 12: Scoping and Targeting (تحديد نطاق الهدف وعزل حركة المرور الخارجة عن النطاق)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
In real-world security assessments, your testing scope is strictly governed by Rules of Engagement (RoE). Intercepting, logging, or scanning traffic belonging to out-of-scope third parties (such as analytics scripts, Google APIs, advertising networks, or CDN providers) clutters your workspace and can even violate authorization boundaries.

Burp Suite addresses this through **Target Scope**:
- Setting a target in scope instructs Burp Suite to prioritize and filter traffic strictly belonging to your authorized application.
- It enables precise logging, cleaner site maps, and allows the proxy to silently ignore third-party requests.

**How to Configure Target Scope:**
1. In **Target ⟶ Site map**, right-click the target domain or IP (e.g. `http://MACHINE_IP/`) and select **`Add to scope`**.
2. Burp will prompt: *"Would you like Burp Proxy to stop sending out-of-scope items to the history or other tools?"* Select **Yes** (or configure it manually under `Target ⟶ Scope settings`).
3. **Limiting the Proxy to Only Intercept In-Scope Items:**
   - Navigate to **Proxy ⟶ Proxy settings ⟶ Intercept Client Requests**.
   - Enable the rule: **`And URL Is in target scope`**.
   - With this rule active, Burp Proxy will only pause requests heading to your target; all other background traffic (such as operating system updates or search engine pings) will pass through transparently without interrupting your workflow.

#### الأسئلة المطروحة (Questions):
1. Add http://MACHINE_IP/ to your scope and change the proxy settings to only intercept traffic to in-scope targets. See the difference between the amount of traffic getting caught by the proxy before and after limiting the scope. (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الالتزام بالقانون وقواعد الاشتباك (Rules of Engagement & Legal OpSec):**
  * فحص أو تخمين مسارات على خوادم خارج النطاق (مثل نطاقات الطرف الثالث لشركات الدفع أو تحليلات جوجل) يعتبر هجوماً غير مصرح به قد يعرض المختبر لمساءلات قانونية.
  * ضبط النطاق بدقة في Burp Suite هو صمام الأمان الذي يحمي المختبر من توجيه أي هجمات آلية (مثل Intruder أو Scanner) لنطاقات غير مصرح بها.
* **تقليل حجم المشروع وحفظ الذاكرة (Memory Optimization):**
  * عند حصر البروكسي على النطاق المصرح به فقط، يتوقف Burp عن تخزين مئات الميجابايت من بيانات الفيديو والملفات الثابتة الخارجية، مما يبقي حجم ملفات المشروع خفيفاً وسريعاً.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة تطبيق عملي على ضبط النطاق داخل Burp Suite.
2. **التلميح الثاني (المستوى العملي):**
   * انقر بالزر الأيمن على عنوان IP الخاص بالهدف في خريطة الموقع واختر "Add to scope".
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * تأكد من تفعيل خيار حصر الاعتراض في إعدادات البروكسي واضغط Submit للمتابعة.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### خطوات حصر النطاق في Burp Suite:
```text
1. في تبويب Target -> Site map:
   Right-click http://MACHINE_IP -> [Add to scope]

2. في تبويب Target -> Scope settings:
   التأكد من ظهور القاعدة:
   Prefix: http://MACHINE_IP/
   Enabled: True

3. في تبويب Proxy -> Proxy settings:
   تحت Intercept Client Requests:
   تحديد الخيار: [✔] And URL Is in target scope

4. في شريط التصفية في HTTP history:
   الضغط على Filter bar وتحديد: [✔] Show only in-scope items
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم إضافة الهدف إلى النطاق وحصر اعتراض البروكسي على النطاق المصرح به فقط.
* تم اعتماد السؤال بنجاح على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
تحديد النطاق (Target Scoping) هو الفارق بين العمل الأمني المنظم والنشاط العشوائي؛ فهو يضمن تركيز كل قدرات التحليل والهجوم على الأهداف المصرح بها حصراً دون إهدار الموارد أو تجاوز حدود التفويض.
