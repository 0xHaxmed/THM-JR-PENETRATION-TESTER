# المهمة 02: الاستكشاف اليدوي - الملفات القياسية الشائعة (Task 02: Manual Discovery - Common Files)

---

### 1. الوصف العام (Stage A: Original Task Overview)
تستعرض هذه المهمة تقنيات **الاستكشاف اليدوي الأولي للملفات القياسية (Standard Files Enumeration)** التي توجد بحكم الأعراف المعمارية للويب في المسار الجذري لأي موقع:
1. **ملف استبعاد روبوتات الفهرسة (`robots.txt`):**
   * يُستخدم لإبلاغ عناكب محركات البحث (مثل Googlebot و Bingbot) بالمسارات والصفحات التي يُحظر الزحف إليها وفهرستها في نتائج البحث العامة بموجب بروتوكول استبعاد الروبوتات (Robots Exclusion Protocol).
2. **ملف خريطة الموقع الهيكلية (`sitemap.xml`):**
   * ملف XML يقدم لمحركات البحث قائمة شاملة بجميع روابط وصفحات الموقع ومواعيد تعديلها وأولويات فهرستها لتسهيل الزحف إليها.

---

### 2. التفكير التحليلي المستقل (Stage B: Independent Analytical Thinking)
* **المفارقة الأمنية في ملف `robots.txt` (The robots.txt Paradox):**
  * عندما يرغب المطور في منع محرك بحث جوجل من فهرسة لوحة الإدارة السرية (مثل بوابة الموظفين `/staff-portal`)، فإنه يقوم بإضافتها تحت تعليمة `Disallow: /staff-portal`.
  * ولكن، هذا الملف عام ومتاح للقراءة لأي شخص على الإنترنت!
  * نتيجة لذلك، وبدلاً من إخفاء المسار، يتحول ملف `robots.txt` إلى **خريطة كنز مجانية لمختبر الاختراق** تكشف له أهم المسارات السرية والحساسة التي يحاول الموقع إخفاءها عن العلن!
* **التسريب المعلوماتي في ملف `sitemap.xml`:**
  * تقوم العديد من أنظمة إدارة المحتوى (CMS) وأدوات النشر بتوليد ملف `sitemap.xml` آلياً.
  * في كثير من الحالات، يقوم المولد الآلي بإدراج صفحات الاختبار، والمسارات السرية، وبوابات الدخول التجريبية (مثل `/s3cr3t-area`) ضمن ملف الخريطة دون علم المطور، مما يكشفها للمختبر بمجرد قراءة الملف.

---

### 3. التلميحات الموجهة (Stage C: Guided Hints)
* توجه في متصفحك إلى المسار: `http://MACHINE_IP/robots.txt` واقرأ المسارات الموجودة بجانب تعليمة `Disallow:`.
* توجه إلى المسار: `http://MACHINE_IP/sitemap.xml` وافحص وسوم `<loc>` للبحث عن المسارات السرية أو غير المألوفة.

---

### 4. الحل العملي المفصل (Stage D: Complete Practical Solution)

#### الخطوة 1: فحص ملف `robots.txt`
باستخدام المتصفح أو عبر أداة `curl`:
```bash
curl -s http://MACHINE_IP/robots.txt
```
المخرجات:
```text
User-agent: *
Disallow: /staff-portal
```
المسار المحظور على عناكب البحث هو: **`/staff-portal`**.

#### الخطوة 2: فحص ملف `sitemap.xml`
```bash
curl -s http://MACHINE_IP/sitemap.xml
```
المخرجات:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>http://MACHINE_IP/</loc>
  </url>
  <url>
    <loc>http://MACHINE_IP/news</loc>
  </url>
  <url>
    <loc>http://MACHINE_IP/contact</loc>
  </url>
  <url>
    <loc>http://MACHINE_IP/s3cr3t-area</loc>
  </url>
</urlset>
```
المسار السري المكشوف في خريطة الموقع هو: **`/s3cr3t-area`**.

---

### 5. الإجابات النموذجية المعتمدة (Stage E: Final Answers)
1. **السؤال 1:** What is the directory in robots.txt that isn't allowed to be viewed by web crawlers?
   * **الإجابة:** `/staff-portal`
2. **السؤال 2:** What is the path of the secret area found in sitemap.xml?
   * **الإجابة:** `/s3cr3t-area`

---

### 6. التحقق العملي والأدلة (Stage F: Practical Verification & Evidence)
* استخراج المسارين `/staff-portal` و `/s3cr3t-area` مباشرة من الملفات المعملية الحية.
* مطابقة الإجابات بنسبة 100% مع حساب المستخدم على TryHackMe.

---

### 7. المخرجات التعليمية والدروس المستفادة (Stage G: Educational Takeaway)
* لا تستخدم أبداً ملف `robots.txt` لإخفاء الصفحات الحساسة؛ فالطريقة الوحيدة الصحيحة لحماية المسارات السرية هي فرض المصادقة والتحقق من الصلاحيات (Authentication & RBAC) على مستوى الخادم وليس عبر طلب الامتناع الأدبي من الروبوتات.
