# المراجع والمصادر التوثيقية: إضافات بيرب سويت (Burp Suite: Extensions References)

---

## 1. التوثيق الرسمي لمنظومة الإضافات (Official PortSwigger Documentation)
* [PortSwigger Burp Suite Extensions User Guide](https://portswigger.net/burp/documentation/desktop/extensions):
  الدليل الإرشادي الرسمي لإدارة الإضافات، تثبيت الحزم من متجر BApp، وضبط بيئات جافا وبايثون وروبي.
* [PortSwigger Montoya API Reference](https://portswigger.net/burp/documentation/desktop/extensions/montoya-api):
  التوثيق البرمجي الكامل والحديث لواجهة Montoya API لكتابة إضافات جافا المتقدمة.
* [PortSwigger BApp Store Catalog](https://portswigger.net/bappstore):
  المستودع الرسمي المعتمد لجميع إضافات مجتمع أمن المعلومات مع التقييمات وشفرات المصدر.
* [PortSwigger Writing Your First Extension](https://portswigger.net/burp/documentation/desktop/extensions/creating):
  دليل المطورين للمبتدئين لإنشاء أول إضافة تفاعلية للتعامل مع طلبات HTTP.

---

## 2. بيئات ومفسرات اللغات المساندة (Runtime Interpreters)
* [The Jython Project (Python on the JVM)](https://www.jython.org/):
  الموقع الرسمي لمفسر جيثون، تنزيل النسخة المستقلة Standalone JAR، وتوثيق دعم لغة بايثون داخل بيئة جافا.
* [The JRuby Project (Ruby on the JVM)](https://www.jruby.org/):
  الموقع الرسمي لمشروع JRuby لدعم إضافات لغة روبي داخل منصة Burp Suite.
* [Maven Central Repository - Jython Standalone](https://repo1.maven.org/maven2/org/python/jython-standalone/):
  المستودع المباشر لتنزيل حزم Jython Standalone JAR المتوافقة مع أداة Burp Suite.

---

## 3. أشهر مستودعات الإضافات المجتمعية مفتوحة المصدر (Community Tools)
* [Autorize by Barak Tawily](https://github.com/Quitten/Autorize):
  المستودع المصدري الرسمي لإضافة فحص صلاحيات الوصول وتدقيق ثغرات IDOR التلقائي.
* [Turbo Intruder by James Kettle (albinowax)](https://github.com/PortSwigger/turbo-intruder):
  إضافة التخمين الفائق المعتمدة على مكتبة Spike لإجراء هجمات السباق الزمني وتخطي الحظر.
* [Logger++ by Corey Ballou](https://github.com/nccgroup/LoggerPlusPlus):
  إضافة متقدمة لتسجيل حركة المرور وفحص الحزم بدقة عبر تعابير Regex.

---

## 4. معايير أمن سلسلة التوريد (Supply Chain Security Standards)
* [OWASP Top 10: A06:2021 - Vulnerable and Outdated Components](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/):
  مخاطر البرمجيات والمكتبات الخارجية غير الموثوقة وأهمية مراجعة كود الإضافات قبل تثبيتها في بيئات الإنتاج الحساسة.
