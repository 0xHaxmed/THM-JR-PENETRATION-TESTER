# المراجع والمصادر الرسمية: ما بعد فحص المنافذ ومحرك السكربتات (REFERENCES.md)

---

### 📚 1. المراجع الأكاديمية والتوثيق الرسمي (Official Documentation & Books)
1. **Nmap Network Scanning: The Official Nmap Project Guide to Network Discovery and Vulnerability Scanning**
   * المؤلف: Gordon "Fyodor" Lyon
   * الفصول المرجعية:
     * *Chapter 7: Service and Application Version Detection* ([https://nmap.org/book/vscan.html](https://nmap.org/book/vscan.html))
     * *Chapter 8: Remote OS Detection* ([https://nmap.org/book/osdetect.html](https://nmap.org/book/osdetect.html))
     * *Chapter 9: Nmap Scripting Engine (NSE)* ([https://nmap.org/book/nse.html](https://nmap.org/book/nse.html))
     * *Chapter 12: Nmap Output Formats* ([https://nmap.org/book/output.html](https://nmap.org/book/output.html))

---

### 🌐 2. مستودعات ومكتبات محرك البرمجة النصية (NSE Repositories & Libraries)
1. **Nmap NSE Documentation Portal:**
   * الفهرس الشامل لجميع السكربتات المدمجة، مكتبات Lua، والأمثلة العملية.
   * الرابط: [https://nmap.org/nsedoc/](https://nmap.org/nsedoc/)
2. **Nmap Scripting Engine API Reference:**
   * مكتبات الشبكات والمقابس غير المتزامنة (`nbase` و `nsock`).
   * الرابط: [https://nmap.org/nsedoc/lib/](https://nmap.org/nsedoc/lib/)

---

### ⚡ 3. بطاقة الاختصارات التكتيكية لما بعد فحص المنافذ (Quick Cheat Sheet)

```bash
# -------------------------------------------------------------
# 1. كشف الخدمات وتحديد الإصدارات
# -------------------------------------------------------------
nmap -sV <target>                   # Service detection with default intensity (7)
nmap -sV --version-light <target>   # Fast service detection (intensity 2)
nmap -sV --version-all <target>     # Deep service detection (intensity 9)

# -------------------------------------------------------------
# 2. كشف بصمة نظام التشغيل وتتبع المسار
# -------------------------------------------------------------
sudo nmap -O <target>               # Operating System detection
sudo nmap -O --osscan-limit <target> # OS detection only on promising targets
sudo nmap -O --osscan-guess <target> # Aggressive fuzzy OS guessing
sudo nmap --traceroute <target>     # Trace hops to target using high TTL

# -------------------------------------------------------------
# 3. محرك سكربتات Nmap البرمجية (NSE)
# -------------------------------------------------------------
nmap -sC <target>                   # Run default safe scripts (--script=default)
nmap --script "vuln" <target>       # Audit for all known vulnerabilities
nmap --script "http-robots.txt" <target> # Check robots.txt for disallowed paths
nmap --script "ssh2-enum-algos" -p 22 <target> # Enumerate SSH cipher algorithms
nmap --script-help <script-name>    # View documentation of any NSE script

# -------------------------------------------------------------
# 4. الفحص الهجومي الشامل المدمج
# -------------------------------------------------------------
sudo nmap -A <target>               # Equivalent to: -sV -O -sC --traceroute

# -------------------------------------------------------------
# 5. حفظ المخرجات وتوثيق الأدلة
# -------------------------------------------------------------
nmap -sV -oN scan.nmap <target>     # Normal readable text format
nmap -sV -oG scan.gnmap <target>    # Greppable single-line format
nmap -sV -oX scan.xml <target>      # Structured XML format
nmap -sV -sC -O -oA audit <target>  # Save all formats: audit.nmap, audit.gnmap, audit.xml
```
