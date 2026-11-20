# ملاحظات إضافية وتعمق تقني: ما بعد فحص المنافذ ومحرك السكربتات (ADDITIONAL-NOTES.md)

---

### 1. المعمارية الداخلية لملف استعلامات الخدمات (`nmap-service-probes`)
يقع ملف `nmap-service-probes` في المسار `/usr/share/nmap/nmap-service-probes` ويحتوي على آلاف الأسطر البرمجية التي تحكم آلية استنطاق الخدمات:
* **بنية التوجيهات الأساسية (Directives):**
  * `Probe <protocol> <probename> <probestring>`: يحدد نوع البروتوكول (TCP أو UDP)، واسم الاستعلام، وحمولة البيانات الخام بصيغة سداسية عشرية.
  * `ports <port_list>`: المنافذ التي يُرسل إليها هذا الاستعلام أولاً (لتسريع الفحص).
  * `match <service> <pattern> <versioninfo>`: التعبير النمطي (RegEx) الذي يُطابق رد الخدمة ويستخرج المتغيرات:
    * `$1`, `$2` تمثل الحقول المستخرجة: Product (`p/../`), Version (`v/../`), Info (`i/../`), Hostname (`h/../`), OS (`o/../`), Device (`d/../`), و CPE (`cpe:/../`).
  * `softmatch <service> <pattern>`: مطابقة أولية سريعة لتضييق نطاق الاستعلامات التالية دون الجزم بالإصدار.
* **كيف يعمل `--version-intensity`؟**
  * كل توجيه `Probe` يرتبط برقم شدة (Rarity) من 1 إلى 9.
  * عند ضبط `--version-light` (الشدة 2)، يتجاهل Nmap أي استعلام تزيد ندرته عن 2، بينما عند `--version-all` (الشدة 9) يتم تنفيذ كافة الاستعلامات تباعاً حتى تتطابق إحدى التعابير النمطية.

---

### 2. التشريح الهندسي لبصمة نظام التشغيل (`nmap-os-db`)
يستخدم Nmap نظاماً علمياً دقيقاً في ملف `/usr/share/nmap/nmap-os-db` يرسل 16 حزمة اختبارية مقسمة إلى مجموعات:
1. **اختبارات التسلسل (SEQ, OPS, WIN, T1):** إرسال 6 حزم TCP SYN إلى منفذ مفتوح بفواصل زمنية 100 مللي ثانية لتحليل:
   * **خوارزمية توليد الأرقام التسلسلية (ISN):** هل هي عشوائية حقيقية (لينكس وويندوز الحديث) أم خطية تصاعدية (أنظمة مدمجة قديمة)؟
   * **خيارات TCP المعتمدة (OPS):** مثل أقصى حجم للقطعة (MSS)، وتوسيع النافذة (Window Scale)، والطوابع الزمنية (Timestamps)، وخاصية SACK.
2. **اختبار التنبيه الصريح بالازدحام (ECN):** إرسال حزمة SYN مع تفعيل رايات CWR و ECE لمراقبة استجابة الخادم.
3. **اختبارات المنافذ المغلقة والمفتوحة (T2 إلى T7):**
   * `T2`: حزمة خالية من الرايات (Null) لمنفذ مفتوح مع خيارات IP.
   * `T3`: حزمة SYN, FIN, URG, PSH لمنفذ مفتوح.
   * `T4`: حزمة ACK لمنفذ مفتوح.
   * `T5`: حزمة SYN لمنفذ مغلق.
   * `T6`: حزمة ACK لمنفذ مغلق.
   * `T7`: حزمة FIN, PSH, URG لمنفذ مغلق.
4. **اختبار حزم UDP المغلقة (U1):** حزمة UDP لمنفذ مغلق لفحص ترويسة خطأ `ICMP Port Unreachable`.
5. **اختبار حزم ICMP (IE):** حزمتا ICMP Echo مع قيم TOS وأكواد مختلفة.
يتم تحويل هذه الردود إلى سطر بصمة مشفر تتم مقارنته مع أكثر من 2,600 بصمة معتمدة.

---

### 3. بنية سكربتات محرك NSE البرمجي (Anatomy of a Lua NSE Script)
يتكون أي سكربت Nmap مكتوب بلغة Lua (امتداد `.nse`) من أربعة أقسام معمارية رئيسية:
```lua
-- 1. رأس السكربت والبيانات الوصفية (Metadata)
description = [[ يفحص وجود ملف robots.txt ويستخرج المسارات المحظورة ]]
author = "Eddie Bell"
license = "Same as Nmap--See https://nmap.org/book/man-legal.html"
categories = {"discovery", "safe"}

-- 2. دالة الشرط والقاعدة (Rule)
-- تحدد متى يشتغل السكربت (prerule, hostrule, portrule, postrule)
portrule = shortport.http

-- 3. دالة التنفيذ والإجراء (Action)
action = function(host, port)
    local path = "/robots.txt"
    local response = http.get(host, port, path)
    if response.status == 200 then
        return parse_robots(response.body)
    end
    return nil
end
```

---

### 4. معالجة مخرجات Nmap برمجياً (Automating XML & Greppable Parsing)

#### استخراج كافة خوادم الويب المفتوحة بلمسة واحدة من ملف `.gnmap`:
```bash
grep "/open/tcp//http/" audit-report.gnmap | awk '{print $2}' > live-http-hosts.txt
```

#### معالجة تقرير XML في بايثون لاستخراج الثغرات والمنافذ:
```python
import xml.etree.ElementTree as ET

tree = ET.parse('audit-report.xml')
root = tree.getroot()

for host in root.findall('host'):
    ip = host.find('address').attrib['addr']
    for port in host.find('ports').findall('port'):
        port_id = port.attrib['portid']
        state = port.find('state').attrib['state']
        service = port.find('service')
        prod = service.attrib.get('product', 'Unknown')
        ver = service.attrib.get('version', '')
        if state == 'open':
            print(f"[+] {ip}:{port_id} -> {prod} {ver}")
```

#### الاستيراد المباشر في قاعدة بيانات Metasploit Framework:
```bash
msfconsole -q
msf6 > db_import audit-report.xml
msf6 > hosts
msf6 > services -p 80,443
```
يحول ملف XML إلى قاعدة بيانات كاملة للأهداف داخل Metasploit جاهزة لإطلاق وحدات الاستغلال (Exploits).
