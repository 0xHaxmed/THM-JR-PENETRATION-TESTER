# Task 05: Comparer: Overview (نظرة عامة على أداة المقارنة البصرية)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The **Comparer** module is Burp Suite's integrated visual difference engine (diff tool). While external utilities like `diff`, `colordiff`, or Meld exist in Linux, Comparer enables seamless, side-by-side comparison of HTTP requests and responses directly within Burp without leaving your testing context.

#### The Comparer Interface Layout:
1. **Items Repository (Left Pane):**
   - Displays numbered items sent to Comparer from other modules (Proxy, Repeater, Intruder) or loaded manually.
   - Shows details including timestamp, originating module, length in bytes, and preview snippet.
2. **Management Buttons (Top-Right):**
   - `Paste`: Inserts text currently in your clipboard as a new item.
   - `Load`: Loads a raw file from disk.
   - `Remove`: Deletes selected items.
   - `Clear`: Flushes the entire repository.
3. **Comparison Execution Buttons (Bottom-Right):**
   - **Words:** Executes a tokenized textual comparison highlighting added, deleted, or modified words.
   - **Bytes:** Executes a strict byte-level hexadecimal diff, essential for binary files, compressed streams, or subtle header whitespace variations.

#### The Comparison Window:
When you select two items and execute a comparison, a pop-up window opens displaying:
- A side-by-side view of both inputs.
- Color-coded highlighting:
  - Orange/Peach: Modified text.
  - Blue: Deleted text.
  - Yellow: Added text.
- A **Sync views** checkbox that locks the scrolling of both panes together.
- The window title bar indicates the exact count of differences identified.

#### الأسئلة المطروحة (Questions):
1. Click me to proceed to the next task. (Answer Format: No answer needed)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **القيمة التكتيكية للمقارنة النصية في اختبار الويب (Diffing Tactic):**
  * في كثير من الثغرات المتقدمة مثل **Blind SQL Injection** أو **Race Conditions** أو **IDOR**، يكون الفارق بين الاستجابة الناجحة والاستجابة الفاشلة مجرد بايتين أو كلمة واحدة مخفية في صفحة HTML تتألف من 2,000 سطر!
  * محاولة اكتشاف هذا الفارق بالعين المجردة أمر شبه مستحيل ومضيعة للوقت. تقوم أداة Comparer بمسح الاستجابتين وعزل الفروق الدقيقة وتلوينها في أجزاء من الثانية.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * هذه المهمة تقدم شرحاً مفاهيمياً لواجهة أداة المقارنة.
2. **التلميح الثاني (المستوى العملي):**
   * اضغط على Submit مباشرة للانتقال إلى التمرين العملي التالي.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشريح تدفق المقارنة في Burp Suite:
```text
[ Response A (Failed Login) ]  ────(Send to Comparer)────┐
                                                         ▼
                                                  [ Comparer Table ]
                                                  [ Item 1 ] [ Item 2 ]
                                                         ▲
[ Response B (Success Login) ] ────(Send to Comparer)────┘
                                                         │
                                        ┌────────────────┴────────────────┐
                                        ▼                                 ▼
                                  [Words Diff]                      [Bytes Diff]
                               (Textual Changes)                 (Hex Byte Changes)
```

#### المفتاح اللوني للفروق في Comparer:
- **Modified (البرتقالي/الوردي):** محتوى تم استبداله بمحتوى آخر بين الاستجابتين.
- **Deleted (الأزرق):** أسطر أو كلمات كانت موجودة في العنصر الأول وحُذفت في الثاني.
- **Added (الأصفر):** أسطر أو ترويسات جديدة أضيفت في العنصر الثاني ولم تكن موجودة في الأول.

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `No answer needed`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد فهم واجهة أداة Comparer وإتمام المهمة بنجاح 100% في منصة TryHackMe.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* أداة Comparer تمنح المختبر قوة ميكروسكوبية لرصد أدق الفروق بين ردود الخادم، مما يحول دون إغفال التلميحات الأمنية الحساسة المغمورة في كتل البيانات الضخمة.
