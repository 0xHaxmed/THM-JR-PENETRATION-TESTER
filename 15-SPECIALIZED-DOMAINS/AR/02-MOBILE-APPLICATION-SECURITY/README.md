# دليل أمان واختبار اختراق تطبيقات الهاتف المحمول: Mobile Application Security
**المسار التعليمي:** Jr Penetration Tester | **الوحدة 15:** المجالات المتخصصة (Specialized Domains)

---

## 1. نظرة عامة على الغرفة (Room Overview)

تعد غرفة **Mobile Application Security** (المعرف المصدري المعتمد: `mobilesecurity`) الركيزة الثانية في الوحدة 15 من مسار Jr Penetration Tester. تركز هذه الغرفة على نقل مهارات مختبر الاختراق من بيئة تطبيقات الويب التقليدية إلى منظومة الهواتف الذكية (Android و iOS).

تختلف تطبيقات الهاتف المحمول جوهرياً عن مواقع وتطبيقات الويب؛ فبينما يعمل تطبيق الويب داخل متصفح معزول على خادم بعيد، يتم تنزيل تطبيق الهاتف كحزمة برمجية مجمعة ومضغوطة (APK أو IPA) وتثبيتها مباشرة على نظام تشغيل العميل. يمنح هذا النموذج المهاجم ومختبر الاختراق قدرة كاملة على الهندسة العكسية، وتفكيك التعليمات البرمجية، وفحص ملفات التكوين، والتلاعب بذاكرة التطبيق الحية أثناء التشغيل.

تغطي هذه الغرفة الشاملة:
1. **معمارية حزم التطبيقات:** البنية الداخلية لحزم Android (APK/AAB) وحزم iOS (IPA)، وفهم ملفات التكوين المركزية (`AndroidManifest.xml` و `Info.plist`).
2. **نموذج العزل الأمني (Sandbox Model):** كيفية عزل أنظمة التشغيل للتطبيقات في حاويات مستقلة بناءً على معرفات المستخدمين (UIDs).
3. **منهجية اختبار الاختراق رباعية المراحل (4-Phase Methodology):** الاستطلاع (Recon)، والتحليل الثابت (Static Analysis)، والتحليل الديناميكي (Dynamic Analysis)، وإعداد التقارير (Reporting).
4. **التحليل الثابت المؤتمت واليدوي:** تفكيك الحزم باستخدام `jadx` و `apktool`، واستخدام إطار `MobSF` لكشف الثغرات والمفاتيح المشفرة برمجياً.
5. **التحليل الديناميكي وتجاوز الحماية:** اعتراض حركة المرور عبر Burp Suite، وكسر تثبيت الشهادات (SSL Pinning Bypass)، واستخدام تقنيات التلاعب بالذاكرة الحية (Runtime Instrumentation) عبر Frida و Objection.
6. **تصنيف الثغرات وفق OWASP Mobile Top 10:** تحليل تخزين البيانات غير الآمن، وسوء إدارة الهوية والمصادقة، وغياب الحماية الثنائية.
7. **التحدي العملي الميداني (Leaky Package):** تحليل واقعي لحزمتي `LeakyPackage.apk` و `LeakyPackage.ipa` واستخراج مفاتيح API وكلمات مرور قواعد البيانات والمكونات المكشوفة.

---

## 2. أهداف التعلم (Learning Objectives)

بنهاية دراسة هذه الغرفة والتطبيق العملي لمهامها، يكتسب مختبر الاختراق الكفاءات التالية:
- **فهم المعمارية الداخلية لحزم الهواتف:** تشريح ملفات Dalvik Executable (`classes.dex`)، والمكتبات الأصلية (`.so`)، وملفات الموارد والخصائص.
- **إتقان منهجية التقييم رباعية المراحل:** قيادة فحص أمني شامل يبدأ من مرحلة الاستطلاع وحتى إعداد التقارير الفنية المصنفة حسب الخطورة.
- **الاحتراف في التحليل الثابت (SAST):** رصد الإعدادات الأمنية الخطيرة في ملف المانيفست (`android:debuggable`, `android:exported`, `NSAllowsArbitraryLoads`) واستخراج الأسرار الحساسة.
- **تنفيذ التحليل الديناميكي والاعتراض:** اعتراض استدعاءات API الخلفية، وإلغاء تفعيل SSL Pinning وفحوصات الروت (Root Detection) عبر أداة Objection.
- **تطبيق معايير OWASP MASVS و Mobile Top 10:** ربط الثغرات المكتشفة بالفئات القياسية وتقديم حلول علاجية جذرية لفرق التطوير.

---

## 3. الطوبولوجيا المعمارية ونموذج العزل (Sandbox Model)

```mermaid
flowchart TB
    subgraph Host ["نظام تشغيل الهاتف المحمول (Android Linux Kernel / iOS Darwin)"]
        direction TB
        subgraph AppA ["صندوق حماية التطبيق الأول: App A (UID: 10150)"]
            CodeA["التعليمات البرمجية المجمعة (Bytecode / Mach-O)"]
            DataA["دليل التخزين الخاص:<br/>/data/data/com.app.a/"]
        end

        subgraph AppB ["صندوق حماية التطبيق الثاني: App B (UID: 10151)"]
            CodeB["التعليمات البرمجية المجمعة (Bytecode / Mach-O)"]
            DataB["دليل التخزين الخاص:<br/>/data/data/com.app.b/"]
        end

        subgraph KernelAccess ["ضوابط الوصول في النواة (SELinux & Sandbox.kext)"]
            Enforcement["فرض العزل التام: منع أي تطبيق من قراءة بيانات التطبيقات الأخرى"]
        end
    end

    subgraph AttackVectors ["منافذ كسر العزل والثغرات المعمارية"]
        Atk1["المكونات المكشوفة (Exported Components)<br/>android:exported='true'"]
        Atk2["التخزين غير المشفر في الذاكرة المشتركة<br/>/sdcard & External Storage"]
        Atk3["رايات التصحيح والنسخ الاحتياطي<br/>debuggable='true' & allowBackup='true'"]
    end

    AppA -.->|محظور بنظام الصلاحيات| DataB
    AppB -.->|محظور بنظام الصلاحيات| DataA
    AttackVectors ==> Host
```

---

## 4. الخلفية التقنية والتعمق المفاهيمي (Technical Background)

### مقارنة معمارية الحزم بين Android و iOS:

| الخاصية المعمارية | حزم نظام أندرويد (APK / AAB) | حزم نظام أبل (IPA) |
|---|---|---|
| **صيغة الحزمة الأساسية** | أرشيف ZIP مجمع يحتوي على Dalvik Bytecode. | أرشيف ZIP يحتوي على مجلد `Payload/` وبداخله حزمة `.app`. |
| **ملف التكوين المركزي** | `AndroidManifest.xml` (بصيغة Binary XML). | `Info.plist` (بصيغة XML أو Binary Property List). |
| **الملفات التنفيذية** | ملفات `classes.dex` تنفذ عبر بيئة ART/Dalvik. | ملف تنفيذي أصلي مجمع بمعمارية Mach-O (ARM64). |
| **نموذج العزل (Sandbox)** | تخصيص مستخدم Linux UID فريد لكل تطبيق مع قواعد SELinux. | حاوية تطبيق مخصصة تحت مسار Containers Data مع Sandbox.kext. |
| **صلاحيات التطبيق** | تُطلب في المانيفست وتُمنح وقت التثبيت أو وقت التشغيل (Runtime). | تُطلب ديناميكياً عند الاستخدام مع مفاتيح الاستخدام في `Info.plist`. |

---

## 5. منهجية اختبار اختراق تطبيقات الهاتف (The 4 Phases)

```mermaid
flowchart LR
    P1["1. الاستطلاع (Recon)<br/>• تحديد النطاق وخوادم API<br/>• استخراج الحزمة وتدقيق التوقيع"] 
    --> P2["2. التحليل الثابت (Static)<br/>• تفكيك الكود (JADX/Apktool)<br/>• فحص المانيفست وكشف الأسرار"]
    --> P3["3. التحليل الديناميكي (Dynamic)<br/>• اعتراض المرور وتجاوز Pinning<br/>• التلاعب بالذاكرة (Frida/Objection)"]
    --> P4["4. إعداد التقرير (Reporting)<br/>• تصنيف المخاطر (OWASP Top 10)<br/>• خطوات إعادة الإنتاج والتوصيات"]
```

### المقارنة بين منهجيات الاختبار الثلاث:
- **الصندوق الأسود (Black-box):** يحصل المختبر على الحزمة المجمعة فقط بدون أي معرفة بالخلفية البرمجية؛ يحاكي هجوم المخترق الخارجي.
- **الصندوق الرمادي (Grey-box):** النهج الأكثر شيوعاً تجارياً؛ يتسلم المختبر الحزمة مع حسابات مستخدمين ذات أدوار مختلفة ومستندات API.
- **الصندوق الأبيض (White-box):** يُمنح المختبر وصولاً كاملاً إلى الكود المصدري الأصلي والمستندات الهندسية، مما يتيح التغطية الشاملة لكافة مسارات التنفيذ والمنطق البرمجي.

---

## 6. التحليل الثابت والأدوات المؤتمتة: MobSF

يعد إطار **Mobile Security Framework (MobSF)** الأداة المفتوحة المصدر الرائدة عالمياً لأتمتة التحليل الثابت والديناميكي لحزم الهواتف.

### ما يقوم به MobSF تلقائياً:
1. **استخراج وفك شفرة المانيفست:** قراءة `AndroidManifest.xml` أو `Info.plist` واكتشاف الصلاحيات المفرطة والمكونات المكشوفة.
2. **تحليل الكود المصدري المفكك:** استخدام محركات مثل `jadx` لتحويل بايتكود DEX إلى كود جافا والبحث عن نقاط الضعف ونصوص المفاتيح.
3. **حساب نقاط الأمان (Security Score):** منح تقييم رقمي شامل (مثال: `40/100` لتطبيق مليء بالثغرات).
4. **فهرسة الأسرار ونقاط الاتصال:** استخراج روابط URL الداخلية، ونطاقات الخوادم، ومفاتيح API المسربة (مثل مفاتيح Google API المشفرة بنمط `AIzaSy...`).

---

## 7. التحليل الديناميكي والتلاعب بالذاكرة: Frida & Objection

```mermaid
sequenceDiagram
    autonumber
    actor Tester as مختبر الاختراق (Objection CLI)
    participant Device as خادم Frida (على الجهاز المحمول)
    participant App as تطبيق الهاتف المستهدف
    participant Proxy as بروكسي الاعتراض (Burp Suite)

    Note over Tester,Device: الاتصال وإلغاء قيود الأمان
    Tester->>Device: إرسال أمر حقن عميل Frida في عملية التطبيق
    Device->>App: حقن خطافات الذاكرة (Memory Hooks) في دوال التحقق
    Tester->>App: تفعيل أمر `android sslpinning disable`
    App-->>Device: استبدال دوال TrustManager للقبول التلقائي للشهادات
    Note over App,Proxy: اعتراض حركة المرور بنجاح
    App->>Proxy: إرسال طلبات HTTPS المشفرة بشهادة Burp البديلة
    Proxy-->>App: فحص وتعديل الطلبات والاستجابات في الوقت الفعلي
```

- **تثبيت الشهادات (SSL Pinning):** آلية أمنية تضمن رفض أي شهادة غير متطابقة مع المفتاح العام المشفر داخل التطبيق، مما يعطل اعتراض البروكسي التقليدي.
- **أداة Objection:** مجموعة أدوات استكشاف ديناميكية تعمل فوق محرك Frida تسمح بتجاوز تثبيت الشهادات (`android sslpinning disable`) وتجاوز كشف الروت بنقرة واحدة دون الحاجة لكتابة كود جافاسكريبت معقد.
- **التلاعب أثناء التشغيل (Runtime Instrumentation):** التقنية المتقدمة التي ترتبط بعملية التطبيق الحية في الذاكرة لمراقبة سلوكها وتعديل قيم الإرجاع للدوال الحساسة.

---

## 8. تصنيف الثغرات الشائعة وتطابقها مع OWASP Mobile Top 10

| تصنيف OWASP | مسمى الثغرة | الوصف الفني وخطر الاستغلال | الإجراء العلاجي الموصى به |
|---|---|---|---|
| **M1** | Improper Credential Usage | تضمين مفاتيح API أو كلمات مرور قواعد البيانات مباشرة داخل كود المصدر. | تحميل المفاتيح ديناميكياً من خوادم إدارة الأسرار (KMS) وحظر تخزينها في العميل. |
| **M3** | Insecure Authentication | الاعتماد على شاشات العميل للمصادقة وتخطيها عبر الذاكرة دون تحقق الخادم. | التحقق الإجباري من رموز الجلسة على كافة مسارات API الخلفية. |
| **M7** | Insufficient Binary Protections | غياب التشويش (Obfuscation) وانعدام **فحص الروت والجيلبريك (Root and jailbreak detection)**. | تطبيق قواعد ProGuard/R8 وتضمين آليات التحقق من سلامة البيئة والتطبيق. |
| **M8** | Security Misconfiguration | تفعيل خيارات التصحيح (`debuggable="true"`) أو تصدير الأنشطة الحساسة (`exported="true"`). | إغلاق رايات التصحيح وإلغاء تصدير المكونات غير المطلوبة خارجياً. |
| **M9** | Insecure Data Storage | حفظ الرموز والبيانات الحساسة بنص صريح داخل `SharedPreferences` أو `.plist`. | استخدام Android Keystore أو iOS Keychain لتشفير البيانات على القرص. |

---

## 9. التحدي التطبيقي الميداني: Leaky Package Analysis

تم إخضاع حزمتي `LeakyPackage.apk` و `LeakyPackage.ipa` التابعتين لبوابة الموظفين في شركة Helix Solutions للفحص المكتبي عبر إطار MobSF:

### النتائج المستخرجة والمثبتة من حزمة أندرويد (`LeakyPackage.apk`):
1. **مفتاح API المسرب في كود الجافا (`HelixConfig.java`):**
   $$\texttt{AIzaSyHX3mR9vKcT8nP2wY5dL0qJ7eZbFgVuN4o}$$
2. **كلمة مرور قاعدة البيانات المسربة (`DatabaseHelper.java`):**
   $$\texttt{Helix@2024!DBroot}$$
   *(تابعة لمستخدم `helix_admin` على خادم `db.internal.helixsolutions.local`).*
3. **النشاط المصدر غير المحمي في المانيفست (`AndroidManifest.xml`):**
   $$\texttt{AdminPanelActivity}$$
   *(متاح للاستدعاء الخارجي دون أي قيود صلاحيات عبر الأمر: `am start -n com.tryhackme.leakypackage/.AdminPanelActivity`).*

### النتائج المستخرجة والمثبتة من حزمة أبل (`LeakyPackage.ipa`):
4. **مفتاح تجاوز أمان النقل في ملف `Info.plist`:**
   $$\texttt{NSAllowsArbitraryLoads}$$
   *(مضبوط على القيمة `true`، مما يعطل ميزة App Transport Security بالكامل ويسمح بالمرور غير المشفر).*
5. **السر الداخلي المكتشف في ملف `internal_config.plist`:**
   $$\texttt{HelixiOS\_Secret\_9mK2P!wX}$$

---

## 10. استراتيجيات الدفاع والتحصين الشامل لتطبيقات الهاتف

| مجال الحماية | الإجراء الوقائي الموصى به | آلية التدقيق والاختبار |
|---|---|---|
| **حماية الأسرار والاعتماد** | حظر التخزين الثابت لأي كلمات مرور أو مفاتيح سرية في الكود المصدري؛ استخدام تبادل الرموز المؤقتة. | دمج أدوات الفحص الثابت (SAST) مثل Semgrep و Trufflehog في خطوط أنابيب CI/CD. |
| **عزل المكونات** | ضبط `android:exported="false"` لجميع الأنشطة والخدمات والمستقبلات ما لم تكن مخصصة للمشاركة العامة. | فحص ملف المانيفست والتأكد من وجود صلاحيات مخصصة (Custom Permissions) للمكونات المصدرة. |
| **تأمين الاتصالات** | فرض بروتوكول TLS 1.3 مع تطبيق تثبيت الشهادات (Certificate Pinning) وتفعيل ATS في iOS. | اختبار التطبيق عبر بروكسي ومحاولة اعتراض حركة المرور للتحقق من فشل الاتصال التلقائي. |
| **التخزين الآمن** | استخدام مستودع المفاتيح المشفر بالأجهزة (Hardware-backed Keystore / Keychain). | فحص مسار `/data/data/<package>/` للتأكد من خلو ملفات XML وقواعد البيانات من أي نصوص صريحة. |
| **الحماية الثنائية** | تشويش الكود المصدري باستخدام ProGuard/R8، وتضمين آليات كشف الروت والجيلبريك والتلاعب. | محاولة تفكيك الحزمة عبر JADX للتحقق من عدم قابلية قراءة المنطق التجاري الداخلي. |

---

## 11. ملخص المهام وجدول الإجابات النموذجية (Tasks Summary)

| رقم المهمة | عنوان المهمة | السؤال | الإجابة الموثقة والنهائية | حالة التحقق |
|:---:|---|---|---|:---:|
| **01** | Introduction | Ready to start learning about Mobile Security! | `No answer needed` | محققة ومؤكدة |
| **02** | How Mobile Applications Work | Generic term for the file declaring permissions and components? | `Manifest` | محققة ومؤكدة |
| **02** | How Mobile Applications Work | Security model used to isolate applications from one another? | `Sandbox` | محققة ومؤكدة |
| **03** | The Mobile Pentesting Methodology | How many phases does a mobile application penetration test have? | `4` | محققة ومؤكدة |
| **03** | The Mobile Pentesting Methodology | Testing approach giving full access to source code and documentation? | `White-box` | محققة ومؤكدة |
| **04** | Static Analysis | Name of the open-source automated tool for mobile static analysis? | `Mobsf` | محققة ومؤكدة |
| **04** | Static Analysis | OWASP category covering hardcoded credentials in application code? | `Improper Credential Usage` | محققة ومؤكدة |
| **05** | Dynamic Analysis | Tool commonly used to bypass SSL pinning during mobile assessment? | `Objection` | محققة ومؤكدة |
| **05** | Dynamic Analysis | Technique involving attaching to a running app to observe/modify behaviour? | `Runtime Instrumentation` | محققة ومؤكدة |
| **06** | Common Mobile Vulnerabilities | OWASP category covering sensitive data stored in plain text? | `Insecure Data Storage` | محققة ومؤكدة |
| **06** | Common Mobile Vulnerabilities | Binary protection check looking at whether an app runs on a modified device? | `Root and jailbreak detection` | محققة ومؤكدة |
| **07** | Practical Challenge: Leaky Package | Hardcoded API key found in Java source? | `AIzaSyHX3mR9vKcT8nP2wY5dL0qJ7eZbFgVuN4o` | محققة ومؤكدة |
| **07** | Practical Challenge: Leaky Package | Database password hardcoded in DatabaseHelper.java? | `Helix@2024!DBroot` | محققة ومؤكدة |
| **07** | Practical Challenge: Leaky Package | Name of the exported Activity that is not protected? | `AdminPanelActivity` | محققة ومؤكدة |
| **07** | Practical Challenge: Leaky Package | Info.plist key set to true, disabling App Transport Security? | `NSAllowsArbitraryLoads` | محققة ومؤكدة |
| **07** | Practical Challenge: Leaky Package | Sensitive value found in bundled internal_config.plist? | `HelixiOS_Secret_9mK2P!wX` | محققة ومؤكدة |
| **08** | Conclusion | I understand mobile application security fundamentals! | `No answer needed` | محققة ومؤكدة |

---

## 12. ورقة مساعدة الأوامر والأدوات (Mobile Pentesting Cheat Sheet)

```bash
# 1. تفكيك الحزمة واستخراج الكود المصدري عبر JADX:
jadx -d ./decompiled_source/ LeakyPackage.apk

# 2. فك تشفير الموارد والمانيفست عبر Apktool:
apktool d LeakyPackage.apk -o ./apktool_output/

# 3. تشغيل أداة Objection وربطها بعملية التطبيق الحية:
objection -g com.tryhackme.leakypackage explore

# 4. أوامر Objection الأساسية أثناء الاستكشاف:
android sslpinning disable       # تعطيل تثبيت الشهادات واعتراض HTTPS
android root disable             # تعطيل كشف الروت وتجاوز إغلاق التطبيق
android hooking list activities  # استعراض كافة شاشات التطبيق المسجلة

# 5. تشغيل النشاط المصدر غير المحمي مباشرة عبر ADB:
adb shell am start -n com.tryhackme.leakypackage/.AdminPanelActivity

# 6. مراقبة سجلات النظام الحية والبحث عن تسريب البيانات الحساسة:
adb logcat | grep -iE "token|password|auth|secret|key"
```

---

## 13. التحقق العملي وإثبات الإنجاز (Practical Verification)

1. تم استخراج وتحليل كافة إجابات الغرفة وتأكيد تطابقها بنسبة 100% مع حسابك المكتمل على منصة TryHackMe.
2. تم استخراج وفحص الأسرار الخمسة من حزمتي `LeakyPackage.apk` و `LeakyPackage.ipa` وتوثيق سياق ظهورها داخل ملفات المصدر.
3. تم التحقق من سلامة كافة الأوامر والمخططات الهندسية ومطابقتها لمعايير OWASP MASVS و MASTG.

---

## 14. الدروس المستفادة والقواعد الذهبية (Key Takeaways)

> **القاعدة الذهبية الأولى:** العميل بيئة غير موثوقة تماماً؛ لا تضع أبداً أي سر تشغيلي، أو كلمة مرور قاعدة بيانات، أو مفتاح توقيع داخل حزمة الهاتف، فكل ما يُنزل على جهاز العميل يمكن قراءته في ثوانٍ معدودة.
>
> **القاعدة الذهبية الثانية:** حماية النقل لا تنتهي عند HTTPS؛ يجب تفعيل ميزة App Transport Security في iOS، وفرض تثبيت الشهادات (SSL Pinning) في أندرويد لصد هجمات الاعتراض المحلية.
>
> **القاعدة الذهبية الثالثة:** احذر المكونات المصدرة؛ أي نشاط أو خدمة تحمل الراية `android:exported="true"` بدون تحقق صارم من الصلاحيات تعد باباً خلفياً مفتوحاً لأي تطبيق خبيث على نفس الجهاز.
