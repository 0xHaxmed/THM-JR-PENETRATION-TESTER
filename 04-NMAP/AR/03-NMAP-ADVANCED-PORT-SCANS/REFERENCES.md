# المراجع والمصادر الرسمية: فحص المنافذ المتقدم والتخفي (REFERENCES.md)

---

### 📚 1. المراجع الأكاديمية والتوثيق الرسمي (Official Documentation & Books)
1. **Nmap Network Scanning: The Official Nmap Project Guide to Network Discovery and Vulnerability Scanning**
   * المؤلف: Gordon "Fyodor" Lyon
   * الفصول المرجعية:
     * *Chapter 5: Advanced Port Scanning Techniques (TCP Null, FIN, Xmas, Maimon, Window, ACK)* ([https://nmap.org/book/man-port-scanning-techniques.html](https://nmap.org/book/man-port-scanning-techniques.html))
     * *TCP Idle Scan (-sI) In-Depth* ([https://nmap.org/book/idlescan.html](https://nmap.org/book/idlescan.html))
     * *Firewall/IDS Evasion and Spoofing* ([https://nmap.org/book/man-bypass-firewalls-ids.html](https://nmap.org/book/man-bypass-firewalls-ids.html))

---

### 🌐 2. الأوراق البحثية الكلاسيكية والمقالات التأسيسية (Classic Papers & Hacktivism)
1. **Uriel Maimon:** *Phrack Magazine, Issue 49, Article 15 (November 1996)*
   * التوثيق الأصلي لخلل كود أنظمة BSD في التعامل مع حزم FIN/ACK.
2. **Salvatore Sanfilippo (Antirez):** *A new TCP port scanning technique based on predictable IP ID (December 1998)*
   * الورقة التأسيسية لفحص الخمول (Idle Scan) واستغلال عداد IP ID.
   * الرابط: [http://www.insecure.org/nmap/idlescan.html](http://www.insecure.org/nmap/idlescan.html)

---

### 📜 3. المعايير القياسية لبروتوكولات الإنترنت (IETF RFC Standards)
1. **RFC 793:** *Transmission Control Protocol (TCP Specification - Page 65)*
   * قواعد إسقاط الحزم في المنافذ المفتوحة وحزم الـ RST في المنافذ المغلقة.
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc793](https://datatracker.ietf.org/doc/html/rfc793)
2. **RFC 791:** *Internet Protocol (IPv4 Specification - Section 3.2 Fragmentation)*
   * تعريف حقول التجزئة: Identification, Flags (DF, MF), و Fragment Offset.
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc791](https://datatracker.ietf.org/doc/html/rfc791)

---

### ⚡ 4. بطاقة الاختصارات التكتيكية للفحوصات المتقدمة (Quick Cheat Sheet)

```bash
# -------------------------------------------------------------
# 1. الفحوصات غير المعيارية الشاذة (تجاوز حظر حزم SYN)
# -------------------------------------------------------------
sudo nmap -sN <target>              # Null Scan (0 flags)
sudo nmap -sF <target>              # FIN Scan (1 flag: FIN)
sudo nmap -sX <target>              # Xmas Scan (3 flags: FIN, PSH, URG)
sudo nmap -sM <target>              # Maimon Scan (2 flags: FIN, ACK)

# -------------------------------------------------------------
# 2. رسم خريطة الجدران النارية وفحص النافذة والفحص المخصص
# -------------------------------------------------------------
sudo nmap -sA <target>              # ACK Scan (Determine filtered vs unfiltered ports)
sudo nmap -sW <target>              # Window Scan (Inspect RST TCP Window field)
sudo nmap --scanflags RST <target>  # Custom scan setting only RST flag
sudo nmap --scanflags SYNFIN <target> # Custom scan setting SYN and FIN flags

# -------------------------------------------------------------
# 3. التمويه بالعناوين الوهمية وانتحال العناوين
# -------------------------------------------------------------
sudo nmap -e eth0 -Pn -S 10.10.10.11 <target> # IP Address Spoofing
sudo nmap -D 10.10.20.21,10.10.20.28,ME <target> # Decoy Scan with specified IPs
sudo nmap -D RND:5,ME <target>      # Decoy scan with 5 randomly generated IPs

# -------------------------------------------------------------
# 4. تجزئة الحزم وتغيير حجم الترويسة
# -------------------------------------------------------------
sudo nmap -sS -f <target>           # 8-byte fragments
sudo nmap -sS -ff <target>          # 16-byte fragments
sudo nmap -sS --mtu 24 <target>     # Custom MTU fragment size (multiple of 8)

# -------------------------------------------------------------
# 5. فحص الخمول والاستطلاع الأعمى (Idle/Zombie Scan)
# -------------------------------------------------------------
sudo nmap -sI 10.10.5.5 <target>    # Use 10.10.5.5 as zombie host

# -------------------------------------------------------------
# 6. تفسير الأسباب والإسهاب
# -------------------------------------------------------------
sudo nmap -sS -F --reason <target>  # Display packet reason (e.g. syn-ack, reset)
```
