# المراجع والمصادر الرسمية: فحص المنافذ الأساسي (REFERENCES.md)

---

### 📚 1. المراجع الأكاديمية والتوثيق الرسمي (Official Documentation & Books)
1. **Nmap Network Scanning: The Official Nmap Project Guide to Network Discovery and Vulnerability Scanning**
   * المؤلف: Gordon "Fyodor" Lyon
   * الفصول المرجعية:
     * *Chapter 4: The Six Port States* ([https://nmap.org/book/man-port-states.html](https://nmap.org/book/man-port-states.html))
     * *Chapter 5: Port Scanning Techniques* ([https://nmap.org/book/man-port-scanning-techniques.html](https://nmap.org/book/man-port-scanning-techniques.html))
     * *Chapter 6: Port Specification and Scan Order* ([https://nmap.org/book/man-port-specification.html](https://nmap.org/book/man-port-specification.html))
     * *Chapter 7: Timing and Performance* ([https://nmap.org/book/man-performance.html](https://nmap.org/book/man-performance.html))

---

### 🌐 2. المعايير القياسية لبروتوكولات الإنترنت (IETF RFC Standards)
1. **RFC 793:** *Transmission Control Protocol (TCP)*
   * مواصفات ترويسة TCP والرايات التحكمية الست (URG, ACK, PSH, RST, SYN, FIN) وحالات النواة.
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc793](https://datatracker.ietf.org/doc/html/rfc793)
2. **RFC 768:** *User Datagram Protocol (UDP)*
   * مواصفات حزم UDP عديمة الاتصال.
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc768](https://datatracker.ietf.org/doc/html/rfc768)
3. **RFC 1812:** *Requirements for IP Version 4 Routers (Section 4.3.2.8 - ICMP Rate Limiting)*
   * متطلبات أجهزة التوجيه في تقييد معدل إرسال رسائل أخطاء ICMP لتفادي الإنهاك.
   * الرابط: [https://datatracker.ietf.org/doc/html/rfc1812](https://datatracker.ietf.org/doc/html/rfc1812)

---

### ⚡ 3. بطاقة الاختصارات التكتيكية لأوامر فحص المنافذ (Quick Cheat Sheet)

```bash
# -------------------------------------------------------------
# 1. فحوصات TCP الأساسية
# -------------------------------------------------------------
nmap -sT <target>                   # TCP Connect Scan (Unprivileged / Full Handshake)
sudo nmap -sS <target>              # TCP SYN Stealth Scan (Default with root / Half-Open)

# -------------------------------------------------------------
# 2. فحص منافذ بروتوكول UDP
# -------------------------------------------------------------
sudo nmap -sU <target>              # Default top 1000 UDP ports
sudo nmap -sU --top-ports 20 <target> # Fast UDP scan on top 20 ports

# -------------------------------------------------------------
# 3. تحديد نطاقات المنافذ المستهدفة
# -------------------------------------------------------------
nmap -p 22,80,443 <target>          # Specific ports
nmap -p 5000-5500 <target>          # Contiguous range
nmap -p- <target>                   # All 65,535 ports
nmap -F <target>                    # Fast mode (top 100 ports)
nmap -r <target>                    # Scan ports consecutively (non-random)

# -------------------------------------------------------------
# 4. التحكم في قوالب التوقيت والأداء
# -------------------------------------------------------------
sudo nmap -sS -T0 <target>          # Paranoid (5 mins between probes, IDS evasion)
sudo nmap -sS -T4 <target>          # Aggressive (Fast, reliable networks / CTFs)
sudo nmap -sS --min-rate 1000 <target>        # Enforce at least 1,000 packets/sec
sudo nmap -sS --min-parallelism=64 <target>  # Run at least 64 parallel probes
```
