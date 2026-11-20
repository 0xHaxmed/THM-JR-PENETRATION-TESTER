# Additional Technical Notes: Offensive Security Intro
## ملاحظات تقنية ميدانية وتعمق مهني

---

### 1. التشريح التقني لأدوات تعداد المسارات (Directory Brute-Forcing Mechanics)
في هذه الغرفة، استُخدمت أداة `dirb`. في بيئات العمل الحقيقية، تم استبدال `dirb` بأدوات أسرع ومكتوبة بلغات برمجة حديثة تدعم التوازي (Multi-threading):
* **Dirb (C):** أداة تقليدية خفيفة، لكنها بطيئة جداً في الفهارس الضخمة لأنها تفحص طلباً واحداً تلو الآخر في الغالب.
* **Gobuster (Go):** أسرع بعشرات المرات، وتدعم فحص المسارات (`dir`)، وتعداد النطاقات الفرعية (`dns`)، وتعداد Virtual Hosts (`vhost`).
* **ffuf (Go):** أداة التخمين الأكثر مرونة واحترافية في مجتمع اختبار الاختراق الحديث، حيث تتيح وضع كلمة `FUZZ` في أي مكان بالطلب (في الرابط، في الهيدر، أو في جسم طلب الـ POST).

#### مقارنة أكواد الاستجابة لبروتوكول HTTP أثناء التعداد:
* `200 OK`: المورد موجود ومتاح للعامة بدون قيود.
* `301 / 302 Redirect`: المسار موجود ولكنه يعيد التوجيه إلى صفحة تسجيل دخول أو مسار آخر.
* `401 Unauthorized`: المسار موجود لكنه يتطلب بيانات اعتماد (Basic Auth / Bearer Token).
* `403 Forbidden`: المسار موجود ولكن خادم الويب يمنع الوصول إليه بناءً على صلاحيات الـ IP أو قيود الجدار الناري.
* `404 Not Found`: المسار غير موجود إطلاقاً (أو تم حجب وجوده كإجراء دفاعي).

---

### 2. جذر المشكلة البرمجية لثغرة FakeBank (Root Cause Analysis)
* **الثغرة:** **Missing Function Level Access Control (Broken Access Control - OWASP A01:2021).**
* **السبب الجذري في الكود:** 
  قام المطور ببناء صفحة `/bank-deposit` وافترض أن عدم وضع رابط لها في القائمة الرئيسية يكفي لحمايتها!
  ```php
  // كود غير آمن (Vulnerable Implementation)
  if (isset($_POST['account']) && isset($_POST['amount'])) {
      $acc = $_POST['account'];
      $amt = $_POST['amount'];
      update_balance($acc, $amt); // تنفيذ فوري بدون فحص للجلسة!
  }
  ```
* **الحل الآمن في الكود (Secure Implementation):**
  يجب التحقق من أن المستخدم مسجل للدخول أولاً، ويحمل دور "موظف بنك معتمد" (Teller / Admin Role):
  ```php
  // كود آمن (Secure Implementation)
  session_start();
  if (!isset($_SESSION['user_role']) || $_SESSION['user_role'] !== 'BANK_TELLER') {
      http_response_code(403);
      die("Access Denied: Insufficient Privileges.");
  }
  // التحقق من صحة المدخلات والتشفير والمعاملات المالية...
  ```
