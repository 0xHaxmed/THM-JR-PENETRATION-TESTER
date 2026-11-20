# References & Standards: Protocols and Servers 2
## المراجع الرسمية والمواصفات القياسية: البروتوكولات والخوادم 2

---

## 1. مواصفات الـ RFC الرسمية (IETF Standards)
* **RFC 4253:** [The Secure Shell (SSH) Transport Layer Protocol](https://datatracker.ietf.org/doc/html/rfc4253) — المواصفة المعمارية لبروتوكول SSH والمصادقة وتبادل المفاتيح.
* **RFC 8446:** [The Transport Layer Security (TLS) Protocol Version 1.3](https://datatracker.ietf.org/doc/html/rfc8446) — المعيار الرسمي الأحدث لبروتوكول TLS وتقليل زمن المصافحة وإلغاء الخوارزميات القديمة.
* **RFC 7858:** [Specification for DNS over Transport Layer Security (DoT)](https://datatracker.ietf.org/doc/html/rfc7858) — معيار تشفير استعلامات أسماء النطاقات عبر المنفذ 853 TCP.
* **RFC 8484:** [DNS Queries over HTTPS (DoH)](https://datatracker.ietf.org/doc/html/rfc8484) — معيار تشفير استعلامات الـ DNS عبر منفذ الويب 443.
* **NIST SP 800-63B:** [Digital Identity Guidelines: Authentication and Lifecycle Management](https://pages.nist.gov/800-63-3/sp800-63b.html) — معايير المعهد الوطني للمعايير والتقنية لإدارة كلمات المرور والمصادقة متعددة العوامل.

---

## 2. دليل الأدوات المتقدمة (Tool Syntax Cheat Sheet)

### أداة tcpdump (Packet Sniffing)
```bash
# التقاط حزم منفذ محدد مع عرض البيانات كـ ASCII بنص صريح
sudo tcpdump -i eth0 -A -n port 23

# حفظ الحزم في ملف pcap لتحليله بـ Wireshark
sudo tcpdump -i eth0 -w capture.pcap port 21 or port 110
```

### أداة Bettercap (MITM Framework)
```bash
# تشغيل أداة Bettercap وتفعيل استكشاف الأجهزة وتسميم الـ ARP
sudo bettercap -iface eth0
net.probe on
set arp.spoof.targets 192.168.1.50
arp.spoof on
net.sniff on
```

### أداة THC Hydra (Online Password Attacks)
```bash
# كسر كلمة مرور خدمة IMAP
hydra -l lazie -P /usr/share/wordlists/rockyou.txt 10.10.x.x imap

# كسر كلمة مرور خدمة SSH بـ 4 خيوط متزامنة وتفادي الحظر
hydra -l mark -P /usr/share/wordlists/rockyou.txt -t 4 10.10.x.x ssh

# كسر كلمة مرور FTP مع قائمة مستخدمين وقائمة كلمات مرور
hydra -L users.txt -P passwords.txt 10.10.x.x ftp
```

### أوامر النقل الآمن عبر SSH (SCP)
```bash
# تنزيل ملف من الخادم البعيد إلى المجلد الحالي
scp mark@10.10.x.x:book.txt .

# رفع ملف محلي إلى الخادم البعيد
scp exploit.sh mark@10.10.x.x:/tmp/
```
