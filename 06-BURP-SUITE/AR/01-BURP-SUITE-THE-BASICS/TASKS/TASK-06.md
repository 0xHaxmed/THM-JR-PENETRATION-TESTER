# Task 06: Navigation (التنقل بين الوحدات واختصارات لوحة المفاتيح)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Navigating Burp Suite efficiently is essential for conducting rapid, fluid penetration tests. Burp's interface is structured into two main navigational rows:

1. **Top-Level Module Tabs:**
   The primary ribbon allows switching between core tools: **Dashboard**, **Target**, **Proxy**, **Intruder**, **Repeater**, **Sequencer**, **Decoder**, **Comparer**, and **Extensions**.
2. **Sub-Tabs:**
   Each primary module contains specialized sub-tabs. For example, selecting **Proxy** displays:
   - `Intercept`: Real-time request pausing and modification.
   - `HTTP history`: Chronological audit log of all HTTP requests/responses passing through the proxy.
   - `WebSockets history`: Log of two-way WebSocket frames.
   - `Proxy settings`: Port bindings, interception filters, and match/replace rules.
3. **Detachable Windows:**
   Tabs can be detached into standalone desktop windows via `Window -> Detach` (ideal for multi-monitor setups where you keep the Proxy history on one screen and the Repeater workbench on another).

**Default Navigation Shortcuts:**
Burp Suite includes global hotkeys for immediate navigation:
- **`Ctrl + Shift + D`** ⟶ Dashboard
- **`Ctrl + Shift + T`** ⟶ Target tab
- **`Ctrl + Shift + P`** ⟶ Proxy tab
- **`Ctrl + Shift + I`** ⟶ Intruder tab
- **`Ctrl + Shift + R`** ⟶ Repeater tab

#### الأسئلة المطروحة (Questions):
1. Which tab Ctrl + Shift + P will switch us to? (Answer Format: Name tab)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **كفاءة وسرعة الاستجابة أثناء الهجوم (Tactical Speed):**
  * أثناء الهجمات الحية واختبار الثغرات الحساسة زمنياً (مثل ثغرات Race Conditions أو انتهاء الجلسات)، الاعتماد على الماوس للتنقل بين التبويبات يبطئ الأداء بشكل كبير.
  * إتقان اختصارات لوحة المفاتيح (`Ctrl + Shift + P` للبروكسي، `Ctrl + R` لإرسال الطلب للـ Repeater، `Ctrl + Space` لإرسال الطلب فوراً من Repeater) يمنح المختبر القدرة على إنجاز هجمات معقدة في ثوانٍ معدودة.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * حرف `P` في الاختصار `Ctrl + Shift + P` يرمز إلى تبويب الوكيل باللغة الإنجليزية متبوعاً بكلمة tab.
2. **التلميح الثاني (المستوى العملي):**
   * راجع جدول الاختصارات في شرح المهمة؛ ستجد أن الاختصار يوجهك إلى `Proxy tab`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الإجابة الصحيحة هي: `Proxy tab`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### جدول اختصارات التنقل الميدانية في Burp Suite:
| اختصار لوحة المفاتيح (Shortcut) | الوجهة التكتيكية في واجهة البرنامج (Destination) |
| :---: | :--- |
| **`Ctrl + Shift + D`** | التبديل إلى لوحة التحكم الرئيسية (Dashboard) |
| **`Ctrl + Shift + T`** | التبديل إلى تبويب الهدف وخريطة الموقع (Target tab) |
| **`Ctrl + Shift + P`** | التبديل إلى تبويب الوكيل واعتراض الحزم (Proxy tab) |
| **`Ctrl + Shift + I`** | التبديل إلى تبويب التخمين الآلي (Intruder tab) |
| **`Ctrl + Shift + R`** | التبديل إلى تبويب المكرر اليدوي (Repeater tab) |
| **`Ctrl + R`** | إرسال الطلب المحدد مباشرة من أي تبويب إلى Repeater |
| **`Ctrl + I`** | إرسال الطلب المحدد مباشرة من أي تبويب إلى Intruder |

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `Proxy tab`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تجربة الاختصار وتأكيد الانتقال الفوري إلى تبويب الوكيل: `Proxy tab`.
* تم اعتماد الإجابة رسمياً على TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
التنقل السلس والتحكم في النوافذ عبر الاختصارات واقتطاع التبويبات (Detached Windows) يوفر بيئة عمل احترافية تمكن المختبر من متابعة سجلات الحركة في شاشة واختبار الحمولات في شاشة أخرى بالتوازي.
