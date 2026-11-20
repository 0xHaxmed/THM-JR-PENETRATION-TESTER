# Task 03: Positions (تحديد مواضع الحقن ورمز المقطع الإجرائي)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **Positions** tab is where you configure which components of the captured HTTP request will be replaced dynamically with payloads during an attack.

When you send a request to Intruder (via `Ctrl + I` or right-clicking in Proxy/Repeater and selecting "Send to Intruder"), Burp Suite automatically attempts to identify common parameter positions (such as URL query strings, body parameters, and cookies). It highlights these positions by enclosing them in a unique delimiter: the section symbol (`§`).

For example:
```http
POST /login HTTP/1.1
Host: target.thm
Content-Length: 33

username=§admin§&password=§password123§
```

The right-hand side of the Positions tab provides four critical action buttons:
1. **Add §:** Encloses the currently selected or highlighted text in section markers, turning it into a payload insertion point. If no text is selected, it simply inserts a single section symbol at the cursor position.
2. **Clear §:** Clears all currently defined payload positions from the request template. This is almost always the first button an experienced tester clicks, as Burp's automatic detection frequently tags irrelevant cookies or session tokens.
3. **Auto §:** Runs Burp's internal heuristics to automatically detect and tag parameters across query strings, POST bodies, and headers.
4. **Refresh:** Refreshes the syntax highlighting and view.

#### الأسئلة المطروحة (Questions):
1. What symbol defines the start and the end of a payload position? (ما هو الرمز الذي يحدد بداية ونهاية موضع الحقن في الطلب؟)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الممارسات الفضلى لإدارة المواضع (Position Discipline):**
  * السلوك التلقائي لبيرب عند استيراد طلب هو وضع علامات `§` حول جميع المتغيرات، بما في ذلك ملفات تعريف الارتباط (`PHPSESSID`, `session`).
  * إذا تركت جلسة المستخدم محاطة برمز `§` دون قصد أثناء هجوم Sniper أو Cluster Bomb، سيقوم Intruder بتغيير الجلسة وتدمير حالة المصادقة (Session Invalidation)، مما يؤدي إلى فشل الهجوم بالكامل!
  * **القاعدة الذهبية:** اضغط دائماً على **Clear §** أولاً، ثم حدد يدوياً فقط المتغير الذي ترغب في استهدافه عبر **Add §**.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * الرمز المستخدم هو رمز المقطع القانوني والفقرات في اللاتينية.
2. **التلميح الثاني (المستوى العملي):**
   * ابحث عن الأزرار الموجودة على يمين نافذة الطلب: Add ..., Clear ...
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الرمز المطلوب هو رمز المقطع: `§`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### مثال عملي على عزل موضع الحقن:
```http
# قبل تنظيف العلامات (Automatic Messy Detection):
GET /search?q=§test§ HTTP/1.1
Host: §target.thm§
Cookie: session=§a1b2c3d4e5f6§

# بعد الضغط على Clear § وتحديد الهدف المقصود فقط عبر Add §:
GET /search?q=§test§ HTTP/1.1
Host: target.thm
Cookie: session=a1b2c3d4e5f6
```

#### خصائص رمز المقطع `§`:
- رمز يونيكود: `U+00A7` (Section Sign).
- يتم استخدامه كفاصل ثنائي (Opening & Closing Tag).
- عند بدء الهجوم، يستبدل محرك Intruder كل ما هو موجود بين علامتي `§...§` بالحمل المحدد في تبويب Payloads. إذا تُرك موضع فارغ `§§`، يتم حشر الحمل مباشرة في ذلك الموقع دون حذف أي محرف أصلي.

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `§`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* بالنظر إلى واجهة Burp Intruder -> Positions، يظهر الرمز `§` على أزرار التحكم `Add §` و `Clear §` ويحيط بجميع الحقول المستهدفة، وهو الرمز المعتمد في منصة TryHackMe بنسبة نجاح 100%.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* الرمز `§` هو المحدد الجراحي لمواقع الحقن في Intruder. ويجب على المختبر المحترف البدء دائماً بمسح كافة العلامات التلقائية لتفادي تدمير الجلسات وحصر الهجوم في المتغير المعني بدقة.
