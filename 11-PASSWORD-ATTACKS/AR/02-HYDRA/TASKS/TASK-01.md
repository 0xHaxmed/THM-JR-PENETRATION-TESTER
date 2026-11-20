# Task 01 · Hydra Introduction (مقدمة: أداة التخمين الشبكي السريع Hydra)

---

## Stage A: Original Task Content & Objective (المحتوى الأصلي والهدف)

### 1. Original English Text
```text
What is Hydra?
Hydra is a brute force online password cracking program, a quick system login password “hacking” tool.

Hydra can run through a list and “brute force” some authentication services. Imagine trying to manually guess someone’s password on a particular service (SSH, Web Application Form, FTP or SNMP) - we can use Hydra to run through a password list and speed this process up for us, determining the correct password.

According to its official repository (https://github.com/vanhauser-thc/thc-hydra), Hydra supports, i.e., has the ability to brute force the following protocols:
“Asterisk, AFP, Cisco AAA, Cisco auth, Cisco enable, CVS, Firebird, FTP, HTTP-FORM-GET, HTTP-FORM-POST, HTTP-GET, HTTP-HEAD, HTTP-POST, HTTP-PROXY, HTTPS-FORM-GET, HTTPS-FORM-POST, HTTPS-GET, HTTPS-HEAD, HTTPS-POST, HTTP-Proxy, ICQ, IMAP, IRC, LDAP, MEMCACHED, MONGODB, MS-SQL, MYSQL, NCP, NNTP, Oracle Listener, Oracle SID, Oracle, PC-Anywhere, PCNFS, POP3, POSTGRES, Radmin, RDP, Rexec, Rlogin, Rsh, RTSP, SAP/R3, SIP, SMB, SMTP, SMTP Enum, SNMP v1+v2+v3, SOCKS5, SSH (v1 and v2), SSHKEY, Subversion, TeamSpeak (TS2), Telnet, VMware-Auth, VNC and XMPP.”

This shows the importance of using a strong password; if your password is common, doesn’t contain special characters and is not above eight characters, it will be prone to be guessed. A one-hundred-million-password list contains common passwords, so when an out-of-the-box application uses an easy password to log in, change it from the default! CCTV cameras and web frameworks often use admin:password as the default login credentials, which is obviously not strong enough.

Question:
- Read the above and have Hydra at the ready. (No answer needed)
```

### 2. Arabic Pedagogical Translation
```text
ما هي أداة هيدرا (Hydra)؟
أداة هيدرا هي برنامج متقدم لشن هجمات التخمين العنيف المباشر عبر الشبكة (Online Brute-Force Password Cracker)، وتُعد إحدى أسرع وأقوى أدوات اختبار اختراق واجهات تسجيل الدخول والأنظمة.

تتيح أداة Hydra تجربة قوائم ضخمة من كلمات المرور تلقائياً ضد مختلف خدمات المصادقة. تخيل أنك تحاول تخمين كلمة مرور مستخدم يدوياً عبر بروتوكول SSH أو استمارة ويب أو FTP أو SNMP؛ سيتطلب ذلك ساعات طويلة، بينما تقوم Hydra بأتمتة هذه العملية بالتوازي وبسرعات هائلة لتحديد كلمة المرور الصحيحة خلال ثوانٍ أو دقائق معدودة.

وفقاً للمستودع الرسمي للأداة (vanhauser-thc/thc-hydra)، تدعم Hydra أكثر من 50 بروتوكول شبكي وخدمة مصادقة، من أبرزها:
- بروتوكولات الإدارة والتحكم عن بعد: SSH, Telnet, RDP, VNC.
- خدمات نقل الملفات والبيانات: FTP, SMB, NFS.
- قواعد البيانات: MySQL, PostgreSQL, MS-SQL, Oracle, MongoDB.
- تطبيقات الويب: HTTP-GET, HTTP-POST, HTTP-FORM-GET, HTTP-FORM-POST.
- خدمات البريد والدلائل: IMAP, POP3, SMTP, LDAP.

توضح هذه القدرات الفائقة الأهمية القصوى لفرض كلمات مرور قوية ومعقدة؛ فإذا كانت كلمة المرور شائعة أو لا تحتوي على رموز خاصة أو يقل طولها عن 12-16 محرفاً، فإنها ستسقط حتماً أمام قوائم التخمين المعجمية (مثل rockyou.txt). كما تبرز خطورة الإبقاء على كلمات المرور الافتراضية مثل admin:password في كاميرات المراقبة وأطر العمل الجاهزة.

السؤال:
- اقرأ النص أعلاه وجهز أداة Hydra للعمل. (لا توجد إجابة مطلوبة - اضغط Complete)
```

### 3. Mission Objectives & Pedagogical Target
- **Target Question:** `Read the above and have Hydra at the ready.`
- **Pedagogical Goal:** Understand the architecture of online network brute-forcing, examine the extensive protocol ecosystem supported by THC-Hydra, and initialize the testing toolkit.

---

## Stage B: Independent Thinking & OpSec Considerations (التفكير المستقل والعملياتي)

### 1. Online Brute-Forcing vs Offline Cracking
$$\text{Password Attacks} = \begin{cases} \text{Online (Hydra, Medusa)} & \text{Limited by network latency, firewalls, and lockouts} \\ \text{Offline (Hashcat, John)} & \text{Limited only by GPU/CPU compute throughput} \end{cases}$$
- In online attacks, each attempt generates a real network socket connection (TCP handshake, application handshake, credentials transmission, response parsing).
- Network rate-limiting, Fail2ban, and firewall connection throttling will drop threads if `-t` is set too high.

---

## Stage C: Guided Hints (تلميحات متدرجة)

### Hint 1: Completing the Task
> هذه المهمة تمثل المدخل النظري لأداة Hydra. اضغط على زر Complete لتسجيل إتمام المهمة الأولى والانتقال للمختبر العملي.

---

## Stage D: Complete Practical Solution & Technical Deep Dive (الحل العملي والعمق التقني)

### Tool Verification & Setup
On Kali Linux or AttackBox, verify Hydra is installed:
```bash
hydra -h | head -n 20
```
Key command-line flags:
- `-l <username>`: Target single username.
- `-L <file>`: Target multiple usernames from a wordlist.
- `-p <password>`: Test a single password.
- `-P <file>`: Test a list of candidate passwords.
- `-t <tasks>`: Number of parallel worker threads (default 16).
- `-v / -V`: Verbose mode displaying attempted combinations.

---

## Stage E: Final Answer & Key Values (الإجابات النهائية والقيم المفتاحية)

- **Question:** `Read the above and have Hydra at the ready.`
- **Answer:**
  ```text
  No answer needed
  ```

---

## Stage F: Practical Verification & Proof of Work (التحقق العملي وإثبات الإنجاز)

Verified on the user's completed account:
- Room: `Hydra` (`hydra`)
- Status: **100% Completed**

---

## Stage G: Key Educational Takeaway (الخلاصة التعليمية المستفادة)

- **Speed must be balanced against detection and denial-of-service.** Aggressive multi-threading (`-t 64`) against production network daemons can easily crash legacy services or trigger immediate IP bans.
