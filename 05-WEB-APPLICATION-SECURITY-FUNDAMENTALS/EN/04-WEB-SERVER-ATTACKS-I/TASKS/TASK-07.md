# Task 07: Common Misconfigurations (أخطاء التهيئة المشتركة وترويسات الأمان الغائبة والفحص بـ Nikto)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
While each server technology has unique architecture and syntax, certain critical security misconfigurations apply universally across all web infrastructure. The most prevalent of these is the omission of standard **HTTP Security Headers**:

1. **`X-Frame-Options`:**
   Tells browsers whether the page is allowed to be rendered within an `<frame>`, `<iframe>`, `<embed>`, or `<object>`. When omitted, attackers can embed the application in an invisible frame on an external malicious website and execute **Clickjacking (UI Redressing)** attacks.
2. **`X-Content-Type-Options: nosniff`:**
   Prevents web browsers from MIME-sniffing a response away from the declared `Content-Type`. Without it, attackers can trick browsers into treating uploaded images or text files as executable JavaScript.
3. **`Content-Security-Policy (CSP)`:**
   Restricts the origins from which scripts, styles, images, and frames can be loaded, mitigating Cross-Site Scripting (XSS) and data injection.
4. **`Referrer-Policy`:**
   Controls how much referrer information (including URLs with sensitive tokens) is transmitted when following links.
5. **`Strict-Transport-Security (HSTS)`:**
   Forces compliant browsers to interact exclusively over encrypted HTTPS connections.

**Automated Misconfiguration Auditing with Nikto:**
Running Nikto against port 80:
```bash
nikto -h http://MACHINE_IP:80 -nointeractive
```
Nikto automatically identifies missing security headers, exposed status endpoints, and directory listings:
- `+ The anti-clickjacking X-Frame-Options header is not present.`
- `+ /files/: Directory indexing found.`

#### الأسئلة المطروحة (Questions):
1. Which security header, when missing, allows a page to be embedded in an iframe on another domain? (Answer Format: Header-Name)
2. What finding text does Nikto report when it detects directory indexing on the /files/ path? (Answer Format: Text finding)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تشريح هجوم Clickjacking عبر غياب `X-Frame-Options`:**
  * إذا لم يرسل الخادم ترويسة `X-Frame-Options: DENY` أو `SAMEORIGIN` (أو ما يعادلها في CSP عبر `frame-ancestors 'self'`)، يستطيع المهاجم استدعاء الموقع داخل إطار `iframe` شفاف فوق زر وهمي في موقع خبيث (مثل "اضغط هنا للفوز بجائزة").
  * عندما ينقر الضحية على الزر الظاهر، تكون نقرته الفعلية قد تمت على زر حساس داخل الموقع المضمن (مثل "تأكيد تحويل مصرفي" أو "حذف الحساب").
* **فهم تقارير Nikto وقواعد بيانات OSVDB:**
  * تستخدم أداة Nikto قاعدة معرفية تشمل آلاف مسارات الخوادم الافتراضية؛ فعندما تجد صفحة تحتوي على جدول أو نصوص فهرسة، تطلق التنبيه القياسي `Directory indexing found`.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * الترويسة المسؤولة عن منع تضمين الصفحة في إطارات (Frames) تبدأ بـ `X-Frame-...`.
2. **التلميح الثاني (المستوى العملي):**
   * عند تشغيل أداة Nikto أو قراءة التقرير في المهمة، ابحث عن السطر الخاص بمسار `/files/` وانقل النص المطابق للوصف.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الإجابة الأولى: `X-Frame-Options`، والإجابة الثانية: `Directory indexing found`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: فحص ترويسات الأمان المفقودة
```bash
# فحص ترويسات الخادم وملاحظة غياب ترويسات الحماية
curl -sI http://MACHINE_IP:80/ | grep -Ei "x-frame|content-security|nosniff|strict-transport"
```
* النتيجة: لا يوجد أي خرج (خلو تام من ترويسات الحماية).
* الترويسة الأساسية لمنع التضمين في إطارات: **`X-Frame-Options`**.

---

#### الخطوة 2: تشغيل فحص Nikto ورصد المخرجات
```bash
# تشغيل أداة Nikto لفحص خادم أباتشي على المنفذ 80
nikto -h http://MACHINE_IP:80 -nointeractive
```
**تقرير فحص Nikto الفعلي:**
```text
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          MACHINE_IP
+ Target Hostname:    MACHINE_IP
+ Target Port:        80
+ Server: Apache/2.4.58 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type.
+ OSVDB-3268: /files/: Directory indexing found.
+ /server-status: Apache Server Status is accessible.
```
* **النص الذي تسجله أداة Nikto عند اكتشاف الفهرسة:** `Directory indexing found`

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Anti-Clickjacking Header):** `X-Frame-Options`
* **إجابة السؤال الثاني (Nikto Directory Indexing Finding):** `Directory indexing found`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تأكيد دور ترويسة `X-Frame-Options` في منع هجمات الاختطاف النقرية.
* تم تشغيل Nikto ومطابقة نص التنبيه بدقة: `Directory indexing found`.
* تم اعتماد الإجابات رسمياً على منصة TryHackMe بنسبة 100%.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
ترويسات الأمان (Security Headers) تمثل خط الدفاع الأول المتاح مجاناً لكل مسؤول خادم؛ فضبط `X-Frame-Options` و `X-Content-Type-Options` و `Content-Security-Policy` يحمي ملايين المستخدمين من هجمات Clickjacking و MIME-Sniffing و XSS دون أي تكلفة معمارية أو استهلاك لموارد الخادم.
