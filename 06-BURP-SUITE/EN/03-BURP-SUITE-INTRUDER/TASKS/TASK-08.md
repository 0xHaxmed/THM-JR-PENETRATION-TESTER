# Task 08: Pitchfork (هجوم الشوكة الرنانة: التكرار المتزامن بالتوازي)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **Pitchfork** attack type is specifically engineered for multi-position attacks where items in different payload sets have a direct, one-to-one relationship.

Unlike Sniper and Battering Ram, Pitchfork utilizes **multiple payload sets**—allocating exactly one distinct payload set for each defined position in the request (up to a system maximum of **20 payload sets**).

#### How Pitchfork Operates (Lockstep Iteration):
- Request 1 takes the 1st payload from Set 1 and the 1st payload from Set 2 (and 1st from Set 3, etc.).
- Request 2 takes the 2nd payload from Set 1 and the 2nd payload from Set 2.
- The attack steps through all payload sets simultaneously in parallel (like the tines of a pitchfork moving through hay).
- **List Length Rule:** If your payload sets contain unequal numbers of items (e.g., Set 1 has 10 items, but Set 2 has only 5 items), Intruder will halt the attack as soon as the shortest list is exhausted (sending only 5 requests).

#### Use Cases:
- **Credential Stuffing:** When you possess a dump of known `username:password` or `email:token` pairs.
- **Matched Identifier Testing:** Testing user IDs against their corresponding known API keys.

#### الأسئلة المطروحة (Questions):
1. What is the maximum number of payload sets we can load into Intruder in Pitchfork mode? (ما هو الحد الأقصى لعدد مجموعات الحمولات التي يمكننا تحميلها في أداة Intruder في نمط Pitchfork؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **المعادلة الرياضية والتحليل السلوكي لهجوم Pitchfork:**
  * المعادلة الصارمة لعدد الطلبات في Pitchfork هي:
    $$\text{Total Requests} = \min(\text{len}(S_1), \text{len}(S_2), \dots, \text{len}(S_k))$$
  * الميزة التكتيكية: هجوم خطي شديد الكفاءة ($O(N)$). إذا كان لديك 10,000 حساب مسرب بكلمات مرورهم المقابلة، فإن Pitchfork سينفذ بالضبط 10,000 طلب (زوجاً بزوج)، بينما لو نُفذت نفس القائمتين عبر Cluster Bomb، لتولد $10,000 \times 10,000 = 100,000,000$ طلب، وهو ما يستحيل تنفيذه عملياً!

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * يدعم Burp Intruder عدداً محدداً من مجموعات الحمولات المرقمة في تبويب Payloads.
2. **التلميح الثاني (المستوى العملي):**
   * هذا الرقم هو الحد الأقصى الثابت لمجموعات الحمولات في جميع أوضاع التعدد (Pitchfork و Cluster Bomb).
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الرقم هو عشرون: `20`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### محاكاة تدفق هجوم Pitchfork:
```text
قالب الطلب:
username=§pos1§&password=§pos2§

المجموعة 1 (Usernames):      المجموعة 2 (Passwords):
1. alice                     1. Summer2026!
2. bob                       2. Password123
3. charlie                   3. Welcome1

الطلبات المرسلة بالتوازي (Lockstep Pairs):
Request 1: username=alice   & password=Summer2026!
Request 2: username=bob     & password=Password123
Request 3: username=charlie & password=Welcome1

إجمالي الطلبات = 3 فقط!
```

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `20`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* بالاطلاع على القائمة المنسدلة لـ Payload set في Burp Intruder، نجد أن الأرقام المتاحة للاختيار تبدأ من 1 وتنتهي عند `20`، وتم التحقق من صحة القيمة `20` في منصة TryHackMe بنسبة نجاح 100%.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* هجوم Pitchfork هو السلاح الأمثل لهجمات حشو بيانات الاعتماد (Credential Stuffing)، حيث يختبر الأزواج المرتبطة بتزامن تام ونمو خطي دون إهدار موارد الشبكة في توليد تباديل عشوائية.
