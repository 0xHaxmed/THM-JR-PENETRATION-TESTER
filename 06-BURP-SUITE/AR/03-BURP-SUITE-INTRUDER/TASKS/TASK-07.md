# Task 07: Battering Ram (هجوم الكبش: الحقن المتزامن الموحد)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **Battering Ram** attack type operates similarly to Sniper in that it uses only a **single payload set**. However, its injection mechanism is completely different.

Instead of iterating through positions one at a time, Battering Ram places the **exact same payload into every defined position simultaneously** in every request.
- If you have defined three positions (`§pos1§`, `§pos2§`, `§pos3§`) and your wordlist contains 50 words:
  - Request 1 injects Word 1 into `pos1`, `pos2`, and `pos3`.
  - Request 2 injects Word 2 into `pos1`, `pos2`, and `pos3`.
  - The total number of requests sent will be exactly equal to the number of words in the payload set (50 requests).

#### Use Cases:
- Testing for default or lazy credentials where the username is identical to the password (e.g., `admin:admin`, `guest:guest`, `test:test`).
- Testing applications that duplicate a token or tracking value across multiple parameters or headers simultaneously.

#### الأسئلة المطروحة (Questions):
1. As a hypothetical question: You need to perform a Battering ram Intruder attack on the example request above. If you have a wordlist with two words in it (admin and Guest) and the positions in the request template look like this: `username=§pentester§&password=§Expl01ted§`. What would the body parameters of the first request that Burp Suite sends be? (كسؤال افتراضي: تحتاج إلى تنفيذ هجوم Battering ram على الطلب أعلاه. إذا كانت لديك قائمة كلمات تحتوي على كلمتين admin و Guest وكانت المواضع في قالب الطلب username=§pentester§&password=§Expl01ted§، فماذا ستكون بارامترات جسم أول طلب يرسله Burp Suite؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المعادلة الرياضية والتحليل السلوكي لهجوم الكبش:**
  * عدد الطلبات = عدد الكلمات في القائمة فقط:
    $$\text{Total Requests} = \text{len(PayloadSet)}$$
  * الميزة التكتيكية: هجوم فائق السرعة وبأقل بصمة شبكية ممكنة عند فحص كلمات المرور الافتراضية المتطابقة مع أسماء الحسابات.
  * في أول طلب، يتم أخذ الكلمة الأولى من القائمة (`admin`) وحقنها في كلا الموضعين المحددين برمز `§`.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هجوم Battering Ram يضع نفس القيمة في كل الحقول المحددة في نفس اللحظة.
2. **التلميح الثاني (المستوى العملي):**
   * الكلمة الأولى في القائمة هي `admin`. استبدل كلا الموضعين `§pentester§` و `§Expl01ted§` بهذه الكلمة.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الصيغة النهائية المطلوبة هي: `username=admin&password=admin`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### محاكاة تدفق هجوم Battering Ram:
```text
قالب الطلب:
username=§pentester§&password=§Expl01ted§

قائمة الكلمات:
1. admin
2. Guest

الطلب الأول (Request 1):
username=admin&password=admin

الطلب الثاني (Request 2):
username=Guest&password=Guest

إجمالي الطلبات = 2 فقط!
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `username=admin&password=admin`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم إدخال السلسلة النصية `username=admin&password=admin` في منصة TryHackMe وتم قبولها فوراً كإجابة صحيحة ومطابقة لمنطق عمل هجوم Battering Ram.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* هجوم Battering Ram هو أداة متخصصة للحقن المتطابق المتزامن، ويستخدم بفعالية عالية لاكتشاف الحسابات ذات بيانات الاعتماد المتماثلة (`admin:admin`) بأقل عدد ممكن من الطلبات.
