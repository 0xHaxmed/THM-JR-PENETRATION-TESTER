# دليل مهام غرفة دخيل بيرب سويت (Burp Suite: Intruder Tasks Guide)

يقدم هذا الملف فهرساً شاملاً ومفصلاً لجميع المهام الثلاث عشرة الخاصة بغرفة **Burp Suite: Intruder**، مع توثيق الأسئلة، الإجابات المعتمدة، وروابط الوصول المباشر للتحليل التقني المتعمق (بمنهجية المراحل السبع من أ إلى ز).

---

## جدول المهام والحلول الموثقة (Tasks Summary Table)

| رقم المهمة | عنوان المهمة (Task Title) | السؤال المطروح (Question) | نوع الإجابة / القيمة (Answer) | ملف التحليل التفصيلي |
| :---: | :--- | :--- | :--- | :---: |
| **01** | Introduction | Let's get started! | `No answer needed` | [TASK-01.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-01.md) |
| **02** | What is Intruder | In which Intruder tab can we define the "Attack type" for our planned attack? | `Positions` | [TASK-02.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-02.md) |
| **03** | Positions | What symbol defines the start and the end of a payload position? | `§` | [TASK-03.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-03.md) |
| **04** | Payloads | Which Payload processing rule could we use to add characters at the end of each payload in the set? | `Add suffix` | [TASK-04.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-04.md) |
| **05** | Introduction to Attack Types | What attack type cycles through the payloads inserting one payload at a time into each position defined in the request? | `Sniper` | [TASK-05.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-05.md) |
| **06** | Sniper | If you were using Sniper to fuzz three parameters with 100 words, how many requests?<br>How many sets of payloads will Sniper accept? | Q1: `300`<br>Q2: `1` | [TASK-06.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-06.md) |
| **07** | Battering Ram | What would the body parameters of the first request that Burp Suite sends be? | `username=admin&password=admin` | [TASK-07.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-07.md) |
| **08** | Pitchfork | What is the maximum number of payload sets we can load into Intruder in Pitchfork mode? | `20` | [TASK-08.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-08.md) |
| **09** | Cluster Bomb | How many requests will Intruder make with sets containing 100, 2, and 30 lines? | `6000` | [TASK-09.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-09.md) |
| **10** | Practical Example | What username and password combination indicates a successful login attempt? | `m.rivera:letmein1` | [TASK-10.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-10.md) |
| **11** | Practical Challenge | 1. Which attack type is best suited?<br>2. Configure position and payload...<br>3. What is the flag? | Q1: `Sniper`<br>Q2: `No answer needed`<br>Q3: `THM{MTMxNTg5NTUzMWM0OWRlYzUzMDVjMzJl}` | [TASK-11.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-11.md) |
| **12** | Extra Mile Challenge | What username and password combination indicates a successful login attempt? | `o.bennett:bella1` | [TASK-12.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-12.md) |
| **13** | Conclusion | I can use Intruder! | `No answer needed` | [TASK-13.md](../../03-BURP-SUITE-INTRUDER/TASKS/TASK-13.md) |

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
