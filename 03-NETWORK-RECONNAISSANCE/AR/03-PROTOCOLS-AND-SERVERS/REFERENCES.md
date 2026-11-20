# References & Standards: Protocols and Servers
## المراجع الرسمية والمواصفات القياسية: البروتوكولات والخوادم

---

## 1. مواصفات الـ RFC الرسمية (IETF Standards)
* **RFC 854:** [Telnet Protocol Specification](https://datatracker.ietf.org/doc/html/rfc854) — المواصفة القياسية الأصلية لبروتوكول تلنت وإدارة المقابس النصية.
* **RFC 959:** [File Transfer Protocol (FTP)](https://datatracker.ietf.org/doc/html/rfc959) — المعيار الرسمي لبروتوكول نقل الملفات ومعمارية القناتين (Control/Data).
* **RFC 5321:** [Simple Mail Transfer Protocol (SMTP)](https://datatracker.ietf.org/doc/html/rfc5321) — معيار نقل البريد الإلكتروني بين الخوادم وتفاصيل رموز الحالة (220, 250, 354, 550).
* **RFC 1939:** [Post Office Protocol - Version 3 (POP3)](https://datatracker.ietf.org/doc/html/rfc1939) — معيار سحب وتنزيل البريد وحالات التفويض والمعاملات.
* **RFC 3501:** [Internet Message Access Protocol - Version 4rev1 (IMAP4)](https://datatracker.ietf.org/doc/html/rfc3501) — معيار مزامنة صناديق البريد على الخوادم.
* **RFC 9110:** [HTTP Semantics and Protocol Architecture](https://datatracker.ietf.org/doc/html/rfc9110) — البنية الحديثة لأوامر وترويسات بروتوكول HTTP.

---

## 2. جدول المنافذ النصية وبدائلها المشفرة (Port Reference Cheat Sheet)

```text
+----------+---------------+----------------+-----------------+
| Protocol | Cleartext TCP | Secure Variant | Encrypted Port  |
+----------+---------------+----------------+-----------------+
| Telnet   | 23            | SSH            | 22              |
| HTTP     | 80            | HTTPS          | 443             |
| FTP      | 21 (Data: 20) | FTPS / SFTP    | 990 / 22        |
| SMTP     | 25            | SMTPS / TLS    | 465 / 587       |
| POP3     | 110           | POP3S          | 995             |
| IMAP     | 143           | IMAPS          | 993             |
+----------+---------------+----------------+-----------------+
```

---

## 3. أوامر التفاعل السريع عبر سطر الأوامر (Raw CLI Cheat Sheet)

```bash
# 1. الاتصال بـ HTTP وسحب ملف يدوياً:
telnet <TARGET_IP> 80
GET /index.html HTTP/1.1
Host: <TARGET_IP>
[Enter twice]

# 2. المصادقة عبر FTP وتنزيل ملف:
ftp <TARGET_IP>
# Name: frank -> Password: <PASS> -> get file.txt -> quit

# 3. استعراض راية خادم SMTP:
nc -vn <TARGET_IP> 25
HELO client.thm
QUIT

# 4. الاستعلام عن البريد عبر POP3:
telnet <TARGET_IP> 110
USER frank
PASS <PASS>
STAT
LIST
QUIT
```
