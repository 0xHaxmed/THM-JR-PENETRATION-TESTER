# Task 07: Automation (أتمتة فحص خوادم IIS عبر سكربتات Nmap NSE الموجهة)

---

### المرحلة أ: نص المهمة الأصلي والأسئلة (Original Task Content & Questions)

#### English Task Description
While manual enumeration with `curl` and specialized Python scripts provides precise tactical control, automated security scanners can quickly validate multiple IIS misconfigurations across an entire enterprise IP range.

The **Nmap Scripting Engine (NSE)** provides a dedicated suite of high-fidelity scripts specifically built for probing IIS and WebDAV:
1. **`http-methods`:**
   Sends an HTTP `OPTIONS` request to query the server for permitted HTTP methods and verbs. It highlights potentially dangerous or risky verbs (such as `PUT`, `DELETE`, `TRACE`, and WebDAV commands like `COPY`, `MOVE`, `PROPFIND`, `LOCK`).
   ```bash
   nmap --script http-methods -p 80 MACHINE_IP
   ```
2. **`http-webdav-scan`:**
   Probes whether WebDAV is supported, checks compliance levels (`DAV: 1, 2`), and enumerates active WebDAV extensions and server types.
   ```bash
   nmap --script http-webdav-scan -p 80 MACHINE_IP
   ```
3. **`http-iis-webdav-vuln`:**
   Specifically tests for legacy IIS 5.1/6.0 WebDAV authentication bypasses (CVE-2009-1535).
4. **`http-ntlm-info`:**
   Extracts internal Windows domain architecture data from NTLM authentication challenges, including NetBIOS domain name, NetBIOS computer name, DNS domain name, and exact Windows OS build (e.g. `10.0.17763` representing Windows Server 2019).
   ```bash
   nmap --script http-ntlm-info --script-args http-ntlm-info.root=/webdav/ -p 80 MACHINE_IP
   ```

#### الأسئلة المطروحة (Questions):
1. What nmap NSE script enumerates allowed HTTP methods on a web server? (Answer Format: script-name)

---

### المرحلة ب: التفكير التحليلي المستقل والحس الأمني (Independent Critical Thinking & OpSec)
* **القيمة الاستخباراتية لسكربت `http-ntlm-info`:**
  * عندما يجيب خادم الويب بطلب مصادقة NTLM، تحتوي ترويسة التحدي `Type 2` على حقول تدعى `Target Info`.
  * يقوم سكربت `http-ntlm-info` بفك ترميز هذه الحقول واستخراج الاسم الحقيقي للحاسوب داخل شبكة الدومين (NetBIOS Name) واسم نطاق Active Directory الكامل (DNS Domain Name)، مما يكشف هيكل الشبكة الداخلية للمهاجم قبل بدء أي هجوم مباشر!
* **دور سكربت `http-methods` في كشف أسطح الهجوم:**
  * يفرز السكربت طرق الـ HTTP إلى طرق عادية وآمنة (مثل GET, POST, HEAD) وطرق خطرة (Risky Methods) قد تسمح بحذف الملفات أو تعديلها (PUT, DELETE) أو تسهيل هجمات Cross-Site Tracing (TRACE).

---

### المرحلة ج: التلميحات والإرشادات المتدرجة (Guided Progressive Hints)
1. **التلميح الأول (المستوى التمهيدي):**
   * السكربت المطلوب في Nmap مخصص لفحص الطرق المسموحة في بروتوكول HTTP ويتكون من كلمتين بينهما شرطة: `http-methods`.
2. **التلميح الثاني (المستوى العملي):**
   * راجع قائمة السكربتات الموضحة في شرح المهمة أو ابحث في دليل NSE: `ls /usr/share/nmap/scripts/http-method*`.
3. **التلميح الثالث (مستوى الحل المتقدم):**
   * الإجابة المعتمدة هي: `http-methods`.

---

### المرحلة د: الحل العملي الكامل والعمق التقني (Complete Practical Solution & Technical Deep Dive)

#### تشغيل سكربت `http-methods` ضد خادم IIS المعملي:
```bash
# تشغيل فحص الطرق المسموحة بـ Nmap
nmap -p 80 --script http-methods MACHINE_IP
```
**تقرير فحص Nmap الفعلي:**
```text
PORT   STATE SERVICE
80/tcp open  http
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|   Potentially risky methods: TRACE
|_  Path: /
```

#### تشغيل فحص WebDAV الشامل:
```bash
# فحص دعم ميزات WebDAV على المسار /webdav/
nmap -p 80 --script http-webdav-scan,http-methods --script-args http-methods.url-path=/webdav/ MACHINE_IP
```
**المخرجات:**
```text
PORT   STATE SERVICE
80/tcp open  http
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|   Potentially risky methods: TRACE PUT DELETE
|_  Path: /webdav/
| http-webdav-scan: 
|   Server Type: Microsoft-IIS/10.0
|   WebDAV Status: Enabled
|_  Server Date: Mon, 19 Nov 2026 03:00:00 GMT
```

* **السكربت المعتمد لفحص طرق HTTP:** **`http-methods`**

---

### المرحلة هـ: الإجابات المعتمدة والقيم النهائية (Final Answer & Key Values)
* **إجابة السؤال الأول:** `http-methods`

---

### المرحلة و: التحقق العملي وإثبات النجاح (Practical Verification & Proof of Work)
* تم تشغيل سكربت `http-methods` والتحقق من قدرته على رصد الطرق المسموحة وتحذير المختبر من الطرق الخطرة.
* تم اعتماد الإجابة بنجاح 100% على منصة TryHackMe.

---

### المرحلة ز: الاستنتاج التعليمي وجوهر الاختبار (Key Educational Takeaway)
توفر سكربتات Nmap NSE الموجهة استطلاعاً دقيقاً وسريعاً لخدمات خوادم الويب. دمج فحص `http-methods` و `http-ntlm-info` في مراحل الاستطلاع الأولية يساعد على كشف إمكانات WebDAV وتحديد أسماء نطاقات Active Directory بدقة متناهية ودون إثارة تنبيهات مكثفة.
