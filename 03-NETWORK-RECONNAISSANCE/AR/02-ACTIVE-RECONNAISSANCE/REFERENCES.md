# References & Tool Documentation: Active Reconnaissance
## المراجع الرسمية ودليل الأدوات: الاستطلاع النشط

---

## 1. المعايير والمواصفات القياسية (RFC Standards)
* **RFC 792:** [Internet Control Message Protocol (ICMP)](https://datatracker.ietf.org/doc/html/rfc792) — المواصفة القياسية الأصلية لبروتوكول رسائل التحكم ورسائل Echo و Time Exceeded.
* **RFC 854:** [Telnet Protocol Specification](https://datatracker.ietf.org/doc/html/rfc854) — البنية المعيارية لبروتوكول تلنت والتفاوض الثنائي على المقابس.
* **RFC 959:** [File Transfer Protocol (FTP)](https://datatracker.ietf.org/doc/html/rfc959) — بنية أوامر وترويسات خوادم بروتوكول نقل الملفات على المنفذ 21.
* **RFC 2616 / RFC 9110:** [HTTP Semantics and Headers](https://datatracker.ietf.org/doc/html/rfc9110) — توثيق استجابات الويب وترويسات الخوادم (`Server:` header).

---

## 2. أدلة الأدوات الرسمية (Official Manuals & Cheat Sheets)

### أداة Ping
```bash
# إرسال 5 حزم بفاصل زمني 0.5 ثانية وحجم حمولة 128 بايت
ping -c 5 -i 0.5 -s 128 <TARGET_IP>

# إرسال حزمة دون انتظار الرد لقياس استقرار الشبكة (Flood ping - يتطلب root)
sudo ping -f -c 100 <TARGET_IP>
```

### أداة Traceroute
```bash
# التتبع المعياري عبر حزم UDP (الافتراضي في لينكس)
traceroute <TARGET_IP>

# التتبع عبر حزم ICMP Echo لتجاوز موجهات تسقط UDP
traceroute -I <TARGET_IP>

# التتبع عبر حزم TCP SYN على المنفذ 80 لتجاوز جدران الحماية الخارجية
traceroute -T -p 80 <TARGET_IP>

# منع التحليل العكسي لأسماء النطاقات لتسريع عملية التتبع
traceroute -n <TARGET_IP>
```

### أداة Netcat
```bash
# الاتصال بمقبس TCP محدد واستخراج الترويسة
nc -vn <TARGET_IP> <PORT>

# فحص نطاق منافذ في وضع القراءة الصامتة دون إرسال بيانات (Zero-I/O Scan)
nc -zvn <TARGET_IP> 20-25

# الاتصال عبر بروتوكول UDP بدلاً من TCP
nc -u -vn <TARGET_IP> 53
```

---

## 3. روابط المصادر الأكاديمية والمهنية
* [Nmap Network Scanning (Gordon Lyon)](https://nmap.org/book/): المرجع الشامل لهندسة فحص الشبكات وبناء حزم الـ ICMP/TCP.
* [Wireshark Sample Captures (ICMP & Traceroute)](https://wiki.wireshark.org/SampleCaptures): لقطات حزم شبكية توضح آلية إنقاص الـ TTL ورسائل Time Exceeded.
* [TryHackMe Active Reconnaissance Room](https://tryhackme.com/room/activerecon): الغرفة التعليمية المعتمدة في مسار Jr Penetration Tester.
