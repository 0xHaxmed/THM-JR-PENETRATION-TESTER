# Task 05: Introduction to Attack Types (المدخل لأنماط الهجوم الأربعة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The true power of Burp Suite Intruder lies in its ability to deploy four distinct **Attack Types**. Choosing the correct attack type dictates how Burp maps your payload sets onto the defined insertion points in the request template:

1. **Sniper:**
   - The default attack type.
   - It utilizes a **single payload set**.
   - It cycles through each defined position sequentially, inserting one payload at a time into one position while keeping all other positions set to their original baseline values.
   - Ideal for fuzzing individual parameters, parameter discovery, and testing for SQLi/XSS across multiple fields independently.
2. **Battering Ram:**
   - Utilizes a **single payload set**.
   - It places the exact same payload into *every* defined position simultaneously on each request.
   - Ideal when an identical value is expected across multiple fields (e.g., verifying if username equals password or updating matching token headers).
3. **Pitchfork:**
   - Utilizes **multiple payload sets** (one distinct set per defined position, up to 20 sets).
   - It steps through all payload sets in lockstep (parallel iteration). Request 1 uses the 1st payload from Set 1, 1st from Set 2; Request 2 uses the 2nd from Set 1, 2nd from Set 2.
   - Ideal for credential stuffing where known username:password pairs are matched one-to-one.
4. **Cluster Bomb:**
   - Utilizes **multiple payload sets** (one distinct set per defined position, up to 20 sets).
   - It iterates through every possible permutation across all payload sets (Cartesian product).
   - Ideal for full dictionary brute-forcing when you have a list of usernames and an independent list of passwords.

#### الأسئلة المطروحة (Questions):
1. What attack type cycles through the payloads inserting one payload at a time into each position defined in the request? (ما هو نوع الهجوم الذي يدور عبر الحمولات عن طريق إدخال حمولة واحدة في كل مرة في كل موضع محدد في الطلب؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المصفوفة التكتيكية لاختيار نوع الهجوم:**
  * **هل تبحث عن ثغرة في بارامتر واحد أو تريد فحص عدة حقول كلٍ على حدة؟** ⟶ **Sniper**.
  * **هل تريد اختبار قيمة موحدة في كل الحقول في نفس اللحظة؟** ⟶ **Battering Ram**.
  * **هل لديك أزواج بيانات مرتبطة مسبقاً (مثل مستخدم وكلمة مروره المسربة)؟** ⟶ **Pitchfork**.
  * **هل تريد تجربة كل الاحتمالات الممكنة بين قائمتين مستقلتين (مثل 100 مستخدم مع 100 كلمة مرور = 10,000 طلب)؟** ⟶ **Cluster Bomb**.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذا النمط هو النمط الافتراضي في Intruder ويشبه القناص الذي يصوب على هدف واحد في كل مرة.
2. **التلميح الثاني (المستوى العملي):**
   * يستخدم مجموعة حمولات واحدة فقط ويختبر المواضع بالتناوب مع الحفاظ على القيم الأصلية لبقية الحقول.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الكلمة المطلوبة تتكون من 6 أحرف: `Sniper`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### المخطط المقارن لأنماط الهجوم الأربعة:
```text
Positions in Template:  user=§pos1§&pass=§pos2§
Payload List A: [admin, root]
Payload List B: [pass1, pass2]

1. SNIPER (1 List):
   Req 1: user=admin & pass=original
   Req 2: user=root  & pass=original
   Req 3: user=original & pass=admin
   Req 4: user=original & pass=root

2. BATTERING RAM (1 List):
   Req 1: user=admin & pass=admin
   Req 2: user=root  & pass=root

3. PITCHFORK (2 Lists in Lockstep):
   Req 1: user=admin & pass=pass1
   Req 2: user=root  & pass=pass2

4. CLUSTER BOMB (Permutations / Cartesian Product):
   Req 1: user=admin & pass=pass1
   Req 2: user=admin & pass=pass2
   Req 3: user=root  & pass=pass1
   Req 4: user=root  & pass=pass2
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `Sniper`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* بالاطلاع على خيارات القائمة المنسدلة في Burp Intruder -> Positions -> Attack type، نجد أن الخيار الأول والافتراضي هو `Sniper` ومطابق لتعريف استهداف موضع واحد في كل مرة.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* هجوم **Sniper** هو حجر الزاوية في فحص المعاملات الفردية، حيث يعزل كل متغير على حدة ويفحصه ضد قائمة الحمولات دون تغيير باقي أجزاء الطلب الأساسية.
