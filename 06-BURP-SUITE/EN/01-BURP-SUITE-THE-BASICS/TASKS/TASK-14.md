# Task 14: Example Attack (تطبيق هجوم عملي: تجاوز التحقق المحلي وحقن سكريبت XSS)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
To demonstrate the practical power of Burp Proxy, this task walks through an attack vector: **Client-Side Validation Bypass** leading to **Reflected Cross-Site Scripting (XSS)**.

**The Target Scenario:**
The target application hosts a support ticket submission form at `http://MACHINE_IP/ticket/`.
- The form accepts a contact email address.
- If you attempt to enter a JavaScript payload directly into the browser's email field (e.g. `<script>alert("Succ3ssful XSS")</script>`), the browser blocks submission locally with an HTML5 / JavaScript regex validation warning: *"Please enter a valid email address"*.
- Because this check runs entirely inside the client-side browser, the payload never even reaches the server!

**The Proxy Bypass Attack Flow:**
1. In Burp Suite, turn **`Intercept is on`** under **Proxy ⟶ Intercept**.
2. In your browser, enter a valid, compliant email address into the form (e.g. `test@example.com`) along with dummy ticket text, and click **Submit**.
3. The browser validates the email locally, finds it compliant, constructs the raw HTTP `POST /ticket/` request, and transmits it.
4. **Burp Proxy intercepts the outgoing request in memory!**
5. In the Proxy Intercept tab, locate the POST body:
   ```http
   email=test%40example.com&ticket_text=Help+please
   ```
6. Replace `test%40example.com` with your raw XSS payload:
   ```html
   <script>alert("Succ3ssful XSS")</script>
   ```
7. Select the payload and press **`Ctrl + U`** to ensure safe URL encoding (producing `%3cscript%3ealert(%22Succ3ssful+XSS%22)%3c%2fscript%3e`).
8. Click **`Forward`**.
9. In your browser, observe the response: the server processes the unvalidated parameter, renders it directly into the HTML response, and triggers a JavaScript alert popup executing **`Succ3ssful XSS`**!

#### الأسئلة المطروحة (Questions):
1. Click me to proceed to the next task. (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **القاعدة الذهبية في أمن تطبيقات الويب (Never Trust Client-Side Validation):**
  * يعد التحقق من المدخلات من جانب العميل (Client-Side Validation عبر HTML5 أو JS) ميزة لتجربة المستخدم (User Experience - UX) لتنبيهه إلى الأخطاء الإملائية الشائعة، لكنه **لا يمثل أي قيمة أمنية على الإطلاق**.
  * يمكن لأي مهاجم تجاوز كافة قيود الحقول (مثل `maxlength`, `required`, `pattern`, `disabled`) بمجرد اعتراض الطلب في Burp وتعديل القيم كما يشاء قبل إرسالها.
  * الحماية الحقيقية يجب أن تتم دائماً على الخادم (Server-Side Validation & Sanitization).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * املأ النموذج ببيانات بريد إلكتروني صحيحة أولاً حتى يقبل المتصفح إرسال الطلب.
2. **التلميح الثاني (المستوى العملي):**
   * تأكد من تفعيل Intercept في Burp قبل ضغط زر Submit في الصفحة.
   * استبدل قيمة معامل البريد بالحمولة واضغط Ctrl+U لترميزها بصيغة URL ثم اضغط Forward.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * السؤال لا يتطلب أي قيمة نصية؛ اضغط على Submit لإنهاء المهمة.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشريح الطلب قبل وبعد التعديل في Burp Proxy:

```http
# الطلب كما أرسله المتصفح بعد تجاوز فحص الـ HTML5:
POST /ticket/ HTTP/1.1
Host: 10.10.x.x
User-Agent: Mozilla/5.0 ...
Content-Type: application/x-www-form-urlencoded
Content-Length: 48

email=test%40example.com&ticket=Technical+Support

# -------------------------------------------------------------
# الطلب بعد تعديله يدوياً داخل Burp Proxy وحقن حمولة XSS:
POST /ticket/ HTTP/1.1
Host: 10.10.x.x
User-Agent: Mozilla/5.0 ...
Content-Type: application/x-www-form-urlencoded
Content-Length: 89

email=%3cscript%3ealert(%22Succ3ssful+XSS%22)%3c%2fscript%3e&ticket=Technical+Support
```

* **النتيجة في المتصفح:** ظهور نافذة منبثقة تفيد بنجاح تشغيل سكريبت XSS المعكوس بنجاح.

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم اعتراض الطلب واستبدال البريد بالحمولة وترميزها بـ `Ctrl + U` ثم تمريرها.
* تم تنفيذ كود الجافا سكريبت وظهور نافذة الـ Alert في المتصفح.
* تم اعتماد السؤال بنجاح على منصة TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
أثبت هذا الهجوم العملي أن المتصفح مجرد بيئة عرض لا يمكن الوثوق بمدخلاتها؛ فباستخدام Burp Suite، يستطيع المختبر تعرية منطق الخادم الحقيقي واختبار مدى متانة آليات التطهير والتعقيم البرمجية في الواجهة الخلفية.
