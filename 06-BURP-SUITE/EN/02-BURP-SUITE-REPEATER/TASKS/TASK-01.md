# Task 01: Introduction (المقدمة ومنهجية التكرار اليدوي للطلبات)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
In web application penetration testing, intercepting requests in the Proxy is only the initial step. Once an interesting endpoint or input parameter is identified, testing it requires making dozens or hundreds of minor, iterative modifications—tweaking SQL syntax, altering headers, probing for input reflection, or observing subtle response status variations.

Repeating this process by repeatedly filling out forms in a web browser is painfully inefficient. This is where **Burp Suite Repeater** becomes indispensable:
- It allows you to take any captured HTTP request from the Proxy history or Site Map and populate it into an isolated workbench.
- You can manually craft, modify, and re-transmit the request as many times as needed.
- You can immediately observe the server's raw, byte-level response without reloading browser DOM trees or handling client-side JavaScript.

#### الأسئلة المطروحة (Questions):
1. Let's get started! (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **القيمة التكتيكية لوحدة Repeater (Tactical Iteration):**
  * المتصفحات العادية تعالج استجابة الخادم وترسمها في شجرة DOM، مما قد يخفي معلومات تشخيصية حرجة (مثل ترويسات أخطاء مخصصة، أو أكواد حالة 500 مصحوبة بنصوص خطأ، أو تعليقات HTML مقتطعة).
  * في Repeater، يتعامل المختبر مع البروتوكول في صورته الخام المجردة (Raw Protocol Stream)، مما يتيح له رصد التغير في حجم الاستجابة بمقدار بايت واحد، أو قياس زمن الاستجابة بدقة المللي ثانية في هجمات الحقن المعتمدة على الوقت (Time-Based Blind SQLi).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة ترحيبية ومدخل لاستيعاب أهداف معمل المكرر (Repeater).
2. **التلميح الثاني (المستوى العملي):**
   * تأكد من تشغيل الآلة المعملية واستقرار اتصالك بالشبكة.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * اضغط على Submit لاعتماد المهمة مباشرة.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### التحقق من جاهزية الاتصال بالآلة المعملية:
```bash
# فحص الاتصال بالهدف عبر Nmap
nmap -p 80 -sS -n -T4 MACHINE_IP
```

#### دورة حياة التحليل في وحدة Repeater:
```text
[ Browser ] ──▶ [ Burp Proxy (History) ] ──(Ctrl + R)──▶ [ Repeater Workbench ]
                                                                 │
                                                        Edit Raw Request
                                                                 │
                                                      Click Send (Ctrl + Space)
                                                                 │
                                                                 ▼
                                                    [ Immediate Server Response ]
                                                    - Pretty / Raw / Hex / Render
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم بدء تشغيل الآلة والتحقق من عمل الخادم.
* تم اعتماد السؤال بنجاح 100% على منصة TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
المكرر (Repeater) هو المختبر التجريبي الأساسي لمختبر الاختراق؛ فهو يوفر منصة جراحية سريعة لإجراء التجارب المتكررة على مدخلات الويب دون أي معوقات من جانب العميل.
