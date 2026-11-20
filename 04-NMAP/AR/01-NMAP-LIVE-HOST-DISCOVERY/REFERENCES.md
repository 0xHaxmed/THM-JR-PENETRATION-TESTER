# المراجع والمصادر الرسمية: اكتشاف الأجهزة النشطة (REFERENCES.md)

---

### 📚 1. المراجع الأكاديمية والتوثيق الرسمي (Official Documentation & Books)
1. **Nmap Network Scanning: The Official Nmap Project Guide to Network Discovery and Vulnerability Scanning**
   * المؤلف: Gordon "Fyodor" Lyon
   * الفصول المرجعية: *Chapter 8: Host Discovery (Ping Scanning)*
   * الرابط: [https://nmap.org/book/man-host-discovery.html](https://nmap.org/book/man-host-discovery.html)
2. **Nmap Reference Guide (Man Pages):**
   * التوثيق الشامل لخيارات الاستكشاف، بروتوكولات Ping، واستعلامات الـ DNS.
   * الرابط: [https://nmap.org/book/man.html](https://nmap.org/book/man.html)

---

### 🌐 2. المعايير القياسية لبروتوكولات الإنترنت (IETF RFC Standards)
1. **RFC 826:** *An Ethernet Address Resolution Protocol (ARP)*
   * المعيار الرسمي لآلية استبانة العناوين في طبقة ربط البيانات.
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc826](https://datatracker.ietf.org/doc/html/rfc826)
2. **RFC 792:** *Internet Control Message Protocol (ICMP)*
   * تعريف رسائل التحكم: Echo (Type 8/0), Timestamp (Type 13/14), Address Mask (Type 17/18), Destination Unreachable (Type 3).
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc792](https://datatracker.ietf.org/doc/html/rfc792)
3. **RFC 793:** *Transmission Control Protocol (TCP)*
   * مواصفات آلة الحالة لمكدس TCP وقواعد الاستجابة بحزم RST عند الاتصالات غير المعترف بها.
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc793](https://datatracker.ietf.org/doc/html/rfc793)
4. **RFC 768:** *User Datagram Protocol (UDP)*
   * بروتوكول البيانات عديم الاتصال وآليات توليد أخطاء Port Unreachable.
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc768](https://datatracker.ietf.org/doc/html/rfc768)

---

### ⚡ 3. بطاقة الاختصارات التكتيكية لأوامر Nmap (Quick Cheat Sheet)

```bash
# -------------------------------------------------------------
# 1. الاستكشاف السريع دون فحص منافذ (No Port Scan)
# -------------------------------------------------------------
nmap -sn <target>

# -------------------------------------------------------------
# 2. الفحص في الشبكة المحلية عبر ARP (يتطلب صلاحيات root)
# -------------------------------------------------------------
sudo nmap -PR -sn 192.168.1.0/24

# -------------------------------------------------------------
# 3. فحوصات بروتوكول ICMP المتعددة لتجاوز الجدران النارية
# -------------------------------------------------------------
sudo nmap -PE -sn 10.10.10.0/24        # ICMP Echo Request
sudo nmap -PP -sn 10.10.10.0/24        # ICMP Timestamp Request
sudo nmap -PM -sn 10.10.10.0/24        # ICMP Address Mask Request

# -------------------------------------------------------------
# 4. فحوصات بروتوكول TCP/UDP على مستوى الطبقة الرابعة
# -------------------------------------------------------------
sudo nmap -PS22,80,443 -sn 10.10.10.0/24  # TCP SYN Ping (Default Port 80)
sudo nmap -PA80,443 -sn 10.10.10.0/24     # TCP ACK Ping (Requires Root)
sudo nmap -PU53,161,40125 -sn 10.10.10.0/24 # UDP Ping

# -------------------------------------------------------------
# 5. التحكم في استعلامات أسماء النطاقات (DNS Resolution)
# -------------------------------------------------------------
nmap -n -sn 10.10.10.0/24              # Never do DNS resolution (Fast & Stealthy)
nmap -R -sn 10.10.10.0/24              # Always do DNS resolution (Resolve offline hosts)
nmap -R -sn --dns-servers 8.8.8.8 <target> # Custom DNS server query

# -------------------------------------------------------------
# 6. استعراض قائمة الأهداف وحسابات العناوين دون إرسال حزم
# -------------------------------------------------------------
nmap -sL -n 10.10.12.13/29             # List targets and calculate range
```
