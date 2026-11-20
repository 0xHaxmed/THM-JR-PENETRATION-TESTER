# Additional Technical Notes: Search Skills
## ملاحظات تقنية ميدانية وتعمق هندسي في أدوات البحث والاستخبار

---

### 1. جدول التحول الكامل لحزمة الشبكات في لينكس (iproute2 Migration Matrix)
تخلت كافة توزيعات لينكس الحديثة (Debian, Ubuntu, RHEL, Arch) عن حزمة `net-tools` القديمة لبطئها واعتمادها على قراءة ملفات `/proc/net/` النصية، واستبدلتها بحزمة `iproute2` التي تتواصل مع النواة مباشرة عبر مقابس Netlink.

| الأداة القديمة المتقاعدة (Deprecated) | الأداة الحديثة القياسية (Modern Standard) | مثال عملي في اختبار الاختراق | الغرض والوظيفة |
|---|---|---|---|
| `netstat -antp` | `ss -tulpn` | `ss -tulpn \| grep LISTEN` | عرض كافة المنافذ المفتوحة وأرقام العمليات المسؤولة عنها |
| `ifconfig -a` | `ip a` (أو `ip address`) | `ip a show eth0` | استعراض عناوين IP وأقنعة الشبكة والماك أدرس |
| `route -n` | `ip r` (أو `ip route`) | `ip route show` | استعراض جدول التوجيه وبوابة الخروج الافتراضية (Default Gateway) |
| `arp -a` | `ip neigh` | `ip neigh show` | فحص جدول بروتوكول ARP للأجهزة المجاورة على نفس الشبكة |

---

### 2. معجم مشغلات Shodan المتقدمة لمختبر الاختراق (Shodan Dorks Cheat Sheet)
يسمح محرك Shodan بتحديد الأجهزة والخوادم بدقة جراحية عبر الفلاتر التالية:

* `product:"Apache httpd"` ⟶ البحث عن خوادم أباتشي حصراً.
* `port:3389 country:"EG"` ⟶ البحث عن أجهزة تفتح بروتوكول RDP في مصر.
* `os:"Windows Server 2008 R2"` ⟶ البحث عن أنظمة تشغيل متقادمة ومعرضة لثغرات حرجة (مثل BlueKeep).
* `org:"Microsoft Corporation"` ⟶ استخراج عناوين IP المسجلة لشركة معينة.
* `has_ssl:true "default password"` ⟶ البحث عن أجهزة تدعم SSL وتحتوي ترويستها على إعدادات افتراضية.
* `"authentication disabled"` ⟶ كشف خوادم كاميرات مراقبة أو قواعد بيانات لا تطلب كلمة مرور.

---

### 3. تشريح درجات تقييم الثغرات (CVSS v3.1 Scoring Breakdown)
في Task 05 تم التعرف على ثغرة `CVE-2024-3094` بدرجة خطورة قصوى `10.0`. يعتمد هذا التقييم على معيار **CVSS v3.1** الذي يقسم مقاييس الثغرة إلى:

$$\text{CVSS Base Score} = f(\text{Exploitability Metrics}, \text{Impact Metrics})$$

1. **مقاييس قابلية الاستغلال (Exploitability Metrics):**
   * **Attack Vector (AV:N):** الاستغلال عن بُعد عبر الشبكة بدون وصول فيزيائي.
   * **Attack Complexity (AC:L):** لا توجد شروط معقدة لتنفيذ الهجوم.
   * **Privileges Required (PR:N):** لا يتطلب الهجوم أي حساب أو صلاحيات مسبقة.
   * **User Interaction (UI:N):** لا يتطلب أي تفاعل أو ضغط من المستخدم الضحية.
2. **مقاييس الأثر (Impact Metrics):**
   * **Confidentiality Impact (C:H):** فقدان كامل وسري للبيانات (High).
   * **Integrity Impact (I:H):** قدرة كاملة على تعديل وتخريب الملفات (High).
   * **Availability Impact (A:H):** قدرة كاملة على إسقاط وإيقاف الخدمة (High).

اجتماع كل هذه العوامل هو ما جعل ثغرة `xz` تحصل على درجة `10.0 Critical`.
