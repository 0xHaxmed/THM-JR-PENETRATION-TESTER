# Task 09: Connecting through the Proxy (ربط المتصفح بالبروكسي عبر إضافة FoxyProxy)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
To route HTTP traffic from an external browser (such as Mozilla Firefox) into Burp Suite, the browser's network settings must be configured to use `127.0.0.1:8080` as its HTTP/HTTPS proxy. Manually changing proxy settings in Firefox preferences back and forth is tedious. The industry standard solution is the **FoxyProxy** browser extension.

**Configuring FoxyProxy Basic in Firefox:**
1. Install the **FoxyProxy Basic** (or Standard) extension from Firefox Browser Add-ons.
2. Open FoxyProxy Options and click **Add**.
3. Fill in the proxy parameters:
   - **Title:** `Burp` (or any recognizable label)
   - **Proxy Type:** `HTTP`
   - **Proxy IP address or DNS name:** `127.0.0.1`
   - **Port:** `8080`
4. Click **Save**.
5. Click on the FoxyProxy icon in the Firefox toolbar and select the newly created **Burp** profile to activate proxy routing.
6. With `Intercept is on` in Burp Suite, browse to `http://MACHINE_IP/`. The request will pause immediately inside Burp Proxy, waiting for you to inspect and click `Forward`.

#### الأسئلة المطروحة (Questions):
1. Click me to proceed to the next task. (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **فصل بيئات التصفح (Browser Isolation):**
  * يجب دائماً تخصيص ملف تعريف متصفح (Dedicated Browser Profile) أو استخدام متصفح منفصل لأعمال اختبار الاختراق عن المتصفح الشخصي.
  * إذا قمت بتفعيل FoxyProxy على متصفحك اليومي، فإن كلمات مرور بريدك الإلكتروني وحساباتك الشخصية سيتم التقاطها وتخزينها في سجلات Burp Suite، وهو ما يتعارض مع أفضل ممارسات الخصوصية والأمن التشغيلي.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة ترشدك خطوة بخطوة لربط متصفح فايرفوكس بالبروكسي.
2. **التلميح الثاني (المستوى العملي):**
   * تأكد من أن منفذ البروكسي في FoxyProxy يطابق منفذ الاستماع في Burp (`8080`).
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * السؤال لا يتطلب أي قيمة نصية؛ اضغط على Submit للمتابعة.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### خطوات ربط المتصفح وتأكيد التقاط الطلب الأول:
```text
1. في فايرفوكس: ضبط FoxyProxy على 127.0.0.1:8080
2. في Burp Suite: التأكد من تفعيل Proxy -> Intercept -> Intercept is on
3. في المتصفح: فتح http://MACHINE_IP/
4. في Burp Suite: ظهور الطلب:
   GET / HTTP/1.1
   Host: MACHINE_IP
   User-Agent: Mozilla/5.0 ...
5. الضغط على زر Forward لتمرير الطلب ومشاهدة الصفحة في المتصفح.
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم ضبط FoxyProxy واعتراض أول طلب HTTP بنجاح وتمريره.
* تم اعتماد السؤال بنجاح على منصة TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
توفر إضافة FoxyProxy تحكماً مرناً وسريعاً للتبديل بين التصفح المباشر وتوجيه الحركة إلى Burp Suite بنقرة زر واحدة، مما يمنع التداخل غير المقصود أثناء جلسات التقييم الأمني.
