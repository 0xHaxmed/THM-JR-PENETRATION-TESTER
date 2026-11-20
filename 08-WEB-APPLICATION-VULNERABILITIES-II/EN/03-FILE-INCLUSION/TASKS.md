# Matrix of Tasks - File Inclusion (مصفوفة المهام)

| رقم المهمة | عنوان المهمة | عدد الأسئلة | الموضوع الجوهري | حالة الإنجاز | رابط ملف الشرح التفصيلي |
| :---: | :--- | :---: | :--- | :---: | :---: |
| **01** | Introduction | 0 | مدخل ثغرات تضمين الملفات وارتباطها بإطار OWASP | مكتمل ✅ | [TASK-01.md](../../03-FILE-INCLUSION/TASKS/TASK-01.md) |
| **02** | Deploy the VM | 0 | تشغيل الآلة وفحص هيكلية المعامل والصفحة الرئيسية | مكتمل ✅ | [TASK-02.md](../../03-FILE-INCLUSION/TASKS/TASK-02.md) |
| **03** | Path Traversal | 1 | تشريح دالة `file_get_contents` وقراءة ملفات لينكس وويندوز | مكتمل ✅ | [TASK-03.md](../../03-FILE-INCLUSION/TASKS/TASK-03.md) |
| **04** | Local File Inclusion - LFI | 2 | المعملين 1 و 2 وتحديد المسارات المطلقة واستخراج مجلد `includes` | مكتمل ✅ | [TASK-04.md](../../03-FILE-INCLUSION/TASKS/TASK-04.md) |
| **05** | Local File Inclusion - LFI Continued | 4 | تقنيات الالتفاف: البايت الفارغ `%00`، المجلد `THM-profile`، وإصدار النظام `12.04` | مكتمل ✅ | [TASK-05.md](../../03-FILE-INCLUSION/TASKS/TASK-05.md) |
| **06** | Remote File Inclusion - RFI | 0 | شروط RFI في `php.ini` ومسار استضافة الحمولات وتحقيق RCE | مكتمل ✅ | [TASK-06.md](../../03-FILE-INCLUSION/TASKS/TASK-06.md) |
| **07** | Challenge | 4 | تحديات الأعلام الثلاثة + RCE Playground (`lfi-vm-thm-f8c5b1a78692`) | مكتمل ✅ | [TASK-07.md](../../03-FILE-INCLUSION/TASKS/TASK-07.md) |
| **08** | Remediation | 0 | القوائم البيضاء، تحصين `php.ini`، وتنقية المسارات بـ `basename` | مكتمل ✅ | [TASK-08.md](../../03-FILE-INCLUSION/TASKS/TASK-08.md) |

---

## ملخص الإجابات والحلول المعتمدة (Quick Reference Table)

| المهمة | السؤال | الإجابة الدقيقة | طريقة الاستخراج التقنية |
| :--- | :--- | :--- | :--- |
| **Task 1** | Read Introduction | `No answer needed` | قراءة المقدمة |
| **Task 2** | Deploy VM | `No answer needed` | تشغيل الآلة وفحص المنافذ |
| **Task 3** | Function causing path traversal in PHP | `file_get_contents` | تحليل الكود البرمجي لدوال قراءة الملفات |
| **Task 4** | Lab #1 request URI to read /etc/passwd | `/lab1.php?file=/etc/passwd` | استدعاء المسار المطلق مباشرة |
| **Task 4** | Lab #2 directory specified in include | `includes` | استخراج المجلد من رسالة التحذير (PHP Warning) |
| **Task 5** | Lab #3 request look like | `/lab3.php?file=../../../../etc/passwd%00` | حقن البايت الفارغ لإنهاء امتداد `.php` |
| **Task 5** | Function causing traversal in Lab #4 | `file_get_contents` | فحص كود المعمل الرابع |
| **Task 5** | Lab #6 directory required in input | `THM-profile` | استخراج البادئة الإلزامية من شاشة المعمل |
| **Task 5** | Lab #6 /etc/os-release VERSION_ID value | `12.04` | قراءة ملف إصدار التوزيعة (Ubuntu 12.04) |
| **Task 6** | Research RFI RCE | `No answer needed` | دراسة استضافة الحمولات عبر خادم HTTP |
| **Task 7** | Capture Flag1 at /etc/flag1 | `F1x3d-iNpu7-f0rrn` | تعديل طريقة الإرسال إلى POST مع البايت الفارغ |
| **Task 7** | Capture Flag2 at /etc/flag2 | `c00k13_i5_yuMmy1` | التلاعب بترويسة الكوكي `Cookie: THM=...` |
| **Task 7** | Capture Flag3 at /etc/flag3 | `P0st_1s_w0rk1in9` | استغلال مصفوفة `$_REQUEST` بتمرير المعلمة في POST |
| **Task 7** | Playground RFI hostname output | `lfi-vm-thm-f8c5b1a78692` | استدعاء حمولة بايثون وتنفيذ أمر `hostname` |
| **Task 8** | Remediation completed | `No answer needed` | مراجعة معايير التحصين والقوائم البيضاء |
