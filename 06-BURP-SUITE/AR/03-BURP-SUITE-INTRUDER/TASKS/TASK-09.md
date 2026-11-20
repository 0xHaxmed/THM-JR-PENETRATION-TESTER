# Task 09: Cluster Bomb (هجوم القنبلة العنقودية: التباديل الشاملة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **Cluster Bomb** attack type is the ultimate multi-variable brute-forcing engine in Burp Intruder. Like Pitchfork, it uses multiple payload sets (one distinct set per defined position, up to a maximum of 20 sets).

However, unlike Pitchfork's lockstep approach, Cluster Bomb iterates through **every single permutation and combination across all defined sets** (the mathematical Cartesian Product).

#### How Cluster Bomb Operates (Cartesian Permutations):
- It tests every payload in Set 2 against the 1st payload in Set 1.
- It then increments Set 1 to its 2nd payload and tests every payload in Set 2 again.
- This continues through all sets, ensuring that no potential combination is left untested.

#### The Mathematical Formula:
$$\text{Total Requests} = \text{len}(\text{Set}_1) \times \text{len}(\text{Set}_2) \times \dots \times \text{len}(\text{Set}_n)$$

#### Use Cases:
- Comprehensive dictionary brute-forcing against authentication forms where both usernames and passwords are unknown.
- Testing combinations of API version numbers, resource names, and action verbs.

#### الأسئلة المطروحة (Questions):
1. We have three payload sets. The first set contains 100 lines, the second contains 2 lines, and the third contains 30 lines. How many requests will Intruder make using these payload sets in a Cluster bomb attack? (لدينا ثلاث مجموعات حمولات. المجموعة الأولى تحتوي على 100 سطر، والثانية على سطرين، والثالثة على 30 سطراً. كم عدد الطلبات التي سيقوم Intruder بإجرائها باستخدام هذه المجموعات في هجوم Cluster bomb؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الانفجار التبادلي والحسابات الرياضية المسبقة (Combinatorial Explosion):**
  * حساب الحجم:
    $$\text{عدد الطلبات} = 100 \times 2 \times 30 = 6000 \text{ طلب}$$
  * **تحذير أمني خطير:** هجوم Cluster Bomb ينمو نمواً ضربياً متسارعاً. إذا زادت قائمة المستخدمين إلى 1,000 وقائمة كلمات المرور إلى 10,000، فإن الناتج سيكون $10,000,000$ طلب!
  * في النسخة المجانية (Community Edition) مع فرض خنق السرعة (Throttling)، قد يستغرق تنفيذ 6,000 طلب عدة ساعات، بينما في النسخة المدفوعة (Pro) يمكن إنجازها في دقائق عبر حوض موارد متعدد الخيوط (Multi-threaded Resource Pool).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * اضرب أعداد الأسطر في المجموعات الثلاث ببعضها.
2. **التلميح الثاني (المستوى العملي):**
   * $100 \times 2 = 200$. ثم $200 \times 30 = 6000$.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الناتج الرياضي الإجمالي هو `6000`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### محاكاة جدول التباديل (Cartesian Product Matrix):
بفرض مجموعتين: المستخدمين `[admin, user]` وكلمات المرور `[123, 456]`:
```text
قالب الطلب: user=§pos1§&pass=§pos2§

الطلبات المتولدة:
1. user=admin & pass=123
2. user=admin & pass=456
3. user=user  & pass=123
4. user=user  & pass=456

إجمالي الطلبات = 2 * 2 = 4 طلبات
```

#### حساب هجوم المهمة:
$$\text{Requests} = 100 \times 2 \times 30 = 6000$$

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `6000`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد الحساب الرياضي `6000` في منصة TryHackMe بنسبة نجاح 100% وظهور إشارة القبول الخضراء.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* هجوم Cluster Bomb هو السلاح الأكثر شمولاً لاختبار كافة التباديل والاحتمالات، ولكنه يتطلب حسابات دقيقة لحجم القواميس قبل الإطلاق تجنباً لإغراق الخادم أو التعثر في قيود خنق السرعة.
