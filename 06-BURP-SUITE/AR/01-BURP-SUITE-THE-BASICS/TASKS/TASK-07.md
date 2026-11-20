# Task 07: Options (خيارات وتهيئة الإعدادات العامة وإعدادات المشروع)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Burp Suite separates its configuration into two distinct scopes:
1. **User Settings (Global):**
   Settings that persist globally across all sessions and application restarts (e.g. UI theme, custom hotkey bindings, update behavior, upstream proxy configurations).
2. **Project Settings:**
   Settings that apply exclusively to the current active project session (e.g. project-specific target scope, active session handling rules, custom client-side TLS certificates).

**Navigating the Unified Settings Dialog:**
Modern versions of Burp Suite provide a centralized `Settings` modal dialog accessible via the top-right toolbar. The dialog includes a live search bar and structured categories:
- **Sessions:** Contains rules for handling cookies, automatic session renewal, macros, and the **Cookie jar** (which passively monitors and updates cookies observed in in-scope responses).
- **Suite:** Controls core application behaviors, including automatic software **Updates**, logging, and embedded browser flags.
- **Hotkeys:** Allows custom modification of keybindings across the entire platform.
- **Network / TLS:** Manages server TLS verification, client-side PKCS#12 certificates, and per-project certificate overrides.

#### الأسئلة المطروحة (Questions):
1. In which category can you find a reference to a "Cookie jar"? (Answer Format: CategoryName)
2. In which base category can you find the "Updates" sub-category, which controls the Burp Suite update behaviour? (Answer Format: CategoryName)
3. What is the name of the sub-category which allows you to change the keybindings for shortcuts in Burp Suite? (Answer Format: SubCategoryName)
4. If we have uploaded Client-Side TLS certificates, can we override these on a per-project basis (yea/nay)? (Answer Format: yea/nay)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **الدور الأمني لقواعد إدارة الجلسات (Session Handling Rules & Cookie Jar):**
  * في كثير من التطبيقات، تنتهي صلاحية جلسة المستخدم بعد بضع دقائق من الخمول، أو تتطلب بعض النماذج إرسال رمز CSRF ديناميكي مع كل طلب.
  * إذا أطلقت هجوماً عبر Intruder دون تحديث هذه الكعكات، ستفشل 99% من الطلبات.
  * يحل **Cookie jar** وقواعد الجلسات في تبويب `Sessions` هذه المشكلة عبر التقاط أحدث كعكة وتحديث الطلبات الصادرة آلياً قبل إرسالها للخادم.
* **تجاوز شهادات العميل TLS (Client-Side TLS Certificates):**
  * بعض الأنظمة البنكية والمصرفية تتطلب شهادة عميل (mTLS) مثبتة على حاسوب المستخدم. إمكانية تجاوز هذه الشهادات وتخصيصها لكل مشروع على حدة (`Project Settings Override`) تتيح للمختبر العمل على عدة عملاء في نفس الوقت دون تداخل.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * ابحث في نافذة Settings عن كلمة "Cookie jar"؛ ستجد أنها تقع تحت فئة إدارة الجلسات: `Sessions`.
2. **التلميح الثاني (المستوى العملي):**
   * إعدادات تحديثات البرنامج تقع ضمن الفئة الأساسية للمنصة: `Suite`.
   * إعدادات تعديل اختصارات المفاتيح تسمى في اللغة الإنجليزية: `Hotkeys`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * إمكانية تخصيص شهادات TLS لكل مشروع مدعومة بالكامل، والإجابة المطلوبة هي: `yea`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### التحقق من فئات الإعدادات في واجهة Burp Suite:
```text
Settings Dialog Hierarchy:
├── Sessions
│   ├── Session handling rules
│   └── Cookie jar (Tracks session cookies for automated tools)  <-- Q1: Sessions
├── Suite
│   ├── Embedded browser
│   └── Updates (Controls check for updates behavior)            <-- Q2: Suite
├── User interface
│   └── Hotkeys (Customize shortcut bindings)                   <-- Q3: Hotkeys
└── Network
    └── TLS
        └── Client TLS certificates (Override per project)       <-- Q4: yea
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Cookie Jar Category):** `Sessions`
* **إجابة السؤال الثاني (Updates Category):** `Suite`
* **إجابة السؤال الثالث (Shortcuts Sub-category):** `Hotkeys`
* **إجابة السؤال الرابع (TLS Certificate Override):** `yea`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم مطابقة جميع الفئات والأقسام في نافذة الإعدادات واعتمادها.
* تم اعتماد الإجابات رسمياً على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
الفصل بين إعدادات المستخدم العامة (User Settings) وإعدادات المشروع الخاصة (Project Settings) يسمح للمختبر بتخصيص بيئة عمل فريدة لكل هدف تقييم (مثل شهادات mTLS وقواعد الجلسات) دون التأثير على إعدادات المنصة الأساسية.
