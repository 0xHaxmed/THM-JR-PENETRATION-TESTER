# دليل مهام غرفة مكرر بيرب سويت (Burp Suite: Repeater Tasks Guide)

يقدم هذا الملف فهرساً شاملاً ومفصلاً لجميع المهام التسع الخاصة بغرفة **Burp Suite: Repeater**، مع توثيق الأسئلة، الإجابات المعتمدة، وروابط الوصول المباشر للتحليل التقني المتعمق (بمنهجية المراحل السبع من أ إلى ز).

---

## جدول المهام والحلول الموثقة (Tasks Summary Table)

| رقم المهمة | عنوان المهمة (Task Title) | السؤال المطروح (Question) | نوع الإجابة / القيمة (Answer) | ملف التحليل التفصيلي |
| :---: | :--- | :--- | :--- | :---: |
| **01** | Introduction | Let's get started! | `No answer needed` | [TASK-01.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-01.md) |
| **02** | What is Repeater? | Which section provides an intuitive interface for editing requests? | `Inspector` | [TASK-02.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-02.md) |
| **03** | Basic Usage | Which view will be populated when we send a request from the Proxy to Repeater? | `Request` | [TASK-03.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-03.md) |
| **04** | Message Analysis Toolbar | Which view option displays the response as it would appear in a browser? | `Render` | [TASK-04.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-04.md) |
| **05** | Inspector | Which section in the Inspector is specific to POST requests? | `Body Parameters` | [TASK-05.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-05.md) |
| **06** | Practical Example | Capture the request to one of the flags on the site. Send it to Repeater and add the FlagAuthorised header as described. What is the flag you receive? | `THM{Yzg2MWI2ZDhlYzdlNGFiZTUzZTIzMzVi}` | [TASK-06.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-06.md) |
| **07** | Challenge | See if you can get the server to return an error 500. What is the flag? | `THM{N2MzMzFhMTA1MmZiYjA2YWQ4M2ZmMzhl}` | [TASK-07.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-07.md) |
| **08** | Extra-mile Challenge | Exploit the union-based SQL injection vulnerability on the /about/ID endpoint to find the flag in the database. | `THM{ZGE3OTUyZGMyMzkwNjJmZjg3Mzk1NjJh}` | [TASK-08.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-08.md) |
| **09** | Conclusion | I can now use Repeater to manually edit and resend HTTP requests! | `No answer needed` | [TASK-09.md](../../02-BURP-SUITE-REPEATER/TASKS/TASK-09.md) |

---

## المنهجية المتبعة في إعداد المهام (7-Stage Methodology)
تمت كتابة وتوثيق كل ملف من الملفات المذكورة أعلاه وفق منهجية دقيقة وشاملة تتألف من المراحل التالية:
1. **المرحلة أ:** نص المهمة الأصلي والأسئلة (باللغة الإنجليزية والترجمة العربية).
2. **المرحلة ب:** التفكير التحليلي المستقل والحس الأمني (OpSec وتحليل السلوك).
3. **المرحلة ج:** التلميحات والإرشادات المتدرجة (ثلاثة مستويات توجيهية).
4. **المرحلة د:** الحل العملي الكامل والعمق التقني (مع الشروحات، الأوامر، والرسومات التوضيحية).
5. **المرحلة هـ:** الإجابة النهائية والقيم المستخرجة (وفق حساب المنصة المعتمد).
6. **المرحلة و:** التحقق العملي وإثبات الأثر (برهان الكفاءة وإعادة الإنتاج).
7. **المرحلة ز:** الخاتمة والدرس المستفاد (الخلاصة التكتيكية).
