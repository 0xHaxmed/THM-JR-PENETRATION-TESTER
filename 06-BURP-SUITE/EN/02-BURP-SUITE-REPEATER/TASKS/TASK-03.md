# Task 03: Basic Usage (الاستخدام الأساسي وتمرير الطلبات من الوكيل)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The standard workflow for utilising Repeater begins in the **Proxy** module:
1. Navigate through the target web application using the configured browser (or Burp's embedded browser) while Burp Proxy is active.
2. In **Proxy -> HTTP history**, locate an interesting HTTP interaction (e.g., an authentication attempt, a search query, an API call, or an administrative endpoint).
3. Send the request to Repeater using one of three methods:
   - Press the standard keyboard shortcut: `Ctrl + R` (or `Cmd + R` on macOS).
   - Right-click anywhere on the request and choose **Send to Repeater**.
   - Select the request from the top action menu and click **Send to Repeater**.
4. Switch to the **Repeater** tab. You will find a new numbered tab containing the captured HTTP interaction.
5. Notice that sending a request to Repeater populates the **Request** pane immediately with the full captured request data. The Response pane remains completely empty until you explicitly press the **Send** button.

Once you press **Send**:
- Burp establishes a direct TCP socket connection to the target web server specified in the `Target` field.
- It transmits the exact bytes in the Request view over the wire.
- It receives the response bytes and populates the **Response** view.

#### الأسئلة المطروحة (Questions):
1. Which view will be populated when we send a request from the Proxy to Repeater? (أي لوحة أو واجهة عرض يتم ملؤها بالبيانات تلقائياً عندما نرسل طلباً من الوكيل Proxy إلى Repeater؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **فصل بيئة التحليل عن مسار حركة البيانات (Isolation of State):**
  * عند إرسال طلب من Proxy History إلى Repeater، يتم أخذ "لقطة ثابتة" (Snapshot) مستقلة عن الطلب الأصلي. أي تعديل تجريه داخل Repeater لن يغير السجل التاريخي للوكيل ولا يؤثر على جلسة المتصفح المفتوحة حتى ترسل الطلب الجديد وتستقبل الرد.
  * هذا الفصل يسمح بإجراء اختبارات هجومية متكررة (Fuzzing / Tampering) بأمان دون قطع اتصال المتصفح الحقيقي أو تلويث الـ History بطلبات تالفة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * تتكون واجهة Repeater من شقين رئيسيين: لوحة للطلب ولوحة للاستجابة.
2. **التلميح الثاني (المستوى العملي):**
   * عند إرسال حركة مرور ملتقطة من الوكيل، فإنها تحتوي على الطلب الصادر من العميل قبل أن يرد الخادم عليه في بيئة الاختبار.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الكلمة المطلوبة تمثل لوحة الطلب بالإنجليزية: `Request`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### خطوات نقل الطلب وتفعيله عملياً:
```bash
# 1. اختصار لوحة المفاتيح الأسرع:
[ In Proxy -> HTTP History ] ──▶ اضغط Ctrl + R

# 2. الانتقال إلى Repeater:
[ In Burp Suite ] ──▶ اضغط Ctrl + Shift + R (أو انقر فوق تبويب Repeater)

# 3. إرسال الطلب إلى السيرفر:
[ In Repeater ] ──▶ اضغط Ctrl + Space (اختصار زر Send السريع)
```

#### تسلسل ملء البيانات في اللوحات:
```text
┌───────────────────────┐         Ctrl + R          ┌───────────────────────────┐
│     Burp Proxy        │ ────────────────────────▶ │       Burp Repeater       │
│  (HTTP History Item)  │                           │                           │
└───────────────────────┘                           │ 1. [Request View]: ملء    │
                                                    │    بالبيانات تلقائياً     │
                                                    │ 2. [Response View]: فارغ  │
                                                    │    (حتى الضغط على Send)   │
                                                    └───────────────────────────┘
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال:**
  `Request`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
1. افتح متصفح Burp وتصفح أي صفحة ويب (مثل صفحة تسجيل الدخول للمعمل).
2. في **Proxy -> HTTP history**، اختر الطلب واضغط `Ctrl + R`.
3. انتقل إلى **Repeater**، ستلاحظ أن لوحة **Request** تحتوي على الطلب بالكامل، بينما لوحة **Response** فارغة تماماً، مما يثبت صحة الإجابة.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* إرسال الطلب إلى Repeater ينشئ علامة تبويب جديدة يملأ فيها حقل **Request** فوراً، مما يمنح المختبر السيطرة التامة لتعديل الترويسات والبارامترات قبل إطلاق الطلب إلى الخادم ومراقبة الرد في حقل Response.
