# itMCQ
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>اختبار الشبكات وتقنيات الويب الشامل (63 سؤالاً)</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Cairo', sans-serif; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen py-8">
    <div class="max-w-4xl mx-auto px-4" x-data="quizApp()">
        <!-- Header & Controls -->
        <header class="bg-white rounded-2xl shadow-sm p-6 mb-6 border border-slate-100">
            <div class="flex flex-col md:flex-row justify-between items-center gap-4 border-b border-slate-100 pb-4 mb-4">
                <div>
                    <h1 class="text-2xl font-bold text-slate-900">اختبار الشبكات وتقنيات الويب الشامل</h1>
                    <p class="text-slate-500 text-sm mt-1">يحتوي على جميع الأسئلة الـ 63 مع الترجمة العربية والتوضيح.</p>
                </div>
                <div class="flex items-center gap-2">
                    <span class="bg-indigo-50 text-indigo-700 font-bold px-4 py-2 rounded-xl text-sm">
                        الدرجة: <span x-text="score"></span> / <span x-text="questions.length"></span>
                    </span>
                </div>
            </div>

            <!-- Batch Filter / Pagination Controls -->
            <div class="flex flex-wrap items-center justify-between gap-3 text-sm">
                <div class="flex items-center gap-2">
                    <label class="text-slate-600 font-medium">الانتقال إلى المجموعة:</label>
                    <select @change="jumpToBatch($event.target.value)" class="bg-slate-100 border border-slate-200 text-slate-800 rounded-lg px-3 py-1.5 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                        <template x-for="(batch, idx) in batches" :key="idx">
                            <option :value="batch.start" x-text="`الأسئلة ${batch.start + 1} - ${batch.end}`" :selected="currentIndex >= batch.start && currentIndex < batch.end"></option>
                        </template>
                    </select>
                </div>
                
                <div class="text-slate-500 font-mono">
                    السؤال <strong x-text="currentIndex + 1"></strong> من <strong x-text="questions.length"></strong>
                </div>
            </div>
        </header>

        <!-- Quiz Card -->
        <template x-if="!quizFinished">
            <div class="bg-white rounded-2xl shadow-sm border border-slate-100 p-6 md:p-8">
                <!-- Progress Bar -->
                <div class="w-full bg-slate-100 rounded-full h-2 mb-6">
                    <div class="bg-indigo-600 h-2 rounded-full transition-all duration-300" :style="`width: ${((currentIndex + 1) / questions.length) * 100}%`"></div>
                </div>

                <!-- Question Header -->
                <div class="mb-6 pb-6 border-b border-slate-100">
                    <span class="text-xs font-bold text-indigo-600 uppercase tracking-wider block mb-1">
                        Question <span x-text="currentIndex + 1"></span> of <span x-text="questions.length"></span>
                    </span>
                    <h2 class="text-lg md:text-xl font-bold text-slate-900 mb-2" x-text="questions[currentIndex].questionEn"></h2>
                    <p class="text-slate-600 text-sm md:text-base" x-text="questions[currentIndex].questionAr"></p>
                </div>

                <!-- Options -->
                <div class="space-y-3">
                    <template x-for="(option, optIndex) in questions[currentIndex].options" :key="optIndex">
                        <button 
                            @click="selectOption(optIndex)"
                            :disabled="answered"
                            :class="{
                                'bg-indigo-50 border-indigo-500 text-indigo-900': selectedOption === optIndex && !answered,
                                'bg-emerald-50 border-emerald-500 text-emerald-900 font-semibold': answered && option.isCorrect,
                                'bg-rose-50 border-rose-500 text-rose-900': answered && selectedOption === optIndex && !option.isCorrect,
                                'bg-white border-slate-200 hover:bg-slate-50 text-slate-700': !answered && selectedOption !== optIndex
                            }"
                            class="w-full text-right p-4 rounded-xl border-2 transition-all flex flex-col gap-1 text-sm md:text-base">
                            <div class="flex justify-between items-center w-full">
                                <span class="font-bold text-indigo-600" x-text="option.label"></span>
                                <span class="text-xs text-emerald-700 bg-emerald-100 px-2 py-0.5 rounded font-bold" x-show="answered && option.isCorrect">الإجابة الصحيحة</span>
                                <span class="text-xs text-rose-700 bg-rose-100 px-2 py-0.5 rounded font-bold" x-show="answered && selectedOption === optIndex && !option.isCorrect">إجابتك</span>
                            </div>
                            <span class="font-semibold text-slate-900" x-text="option.textEn"></span>
                            <span class="text-xs md:text-sm text-slate-500" x-text="option.textAr"></span>
                        </button>
                    </template>
                </div>

                <!-- Next / Previous Controls -->
                <div class="mt-8 flex justify-between items-center pt-4 border-t border-slate-100">
                    <button 
                        @click="prevQuestion()"
                        :disabled="currentIndex === 0"
                        class="bg-slate-100 hover:bg-slate-200 disabled:opacity-40 text-slate-700 font-semibold px-5 py-2.5 rounded-xl transition-all text-sm">
                        السؤال السابق
                    </button>

                    <button 
                        x-show="answered"
                        @click="nextQuestion()"
                        class="bg-indigo-600 hover:bg-indigo-700 text-white font-semibold px-6 py-2.5 rounded-xl transition-all text-sm shadow-sm">
                        <span x-text="currentIndex < questions.length - 1 ? 'السؤال التالي ➔' : 'إنهاء الاختبار وفحص النتيجة 🏁'"></span>
                    </button>
                </div>
            </div>
        </template>

        <!-- Final Results Screen -->
        <template x-if="quizFinished">
            <div class="bg-white rounded-2xl shadow-sm border border-slate-100 p-8 text-center animate-fadeIn">
                <div class="w-24 h-24 bg-indigo-50 text-indigo-600 rounded-full flex items-center justify-center mx-auto mb-4 text-4xl font-bold">
                    <span x-text="Math.round((score / questions.length) * 100) + '%'"></span>
                </div>
                <h2 class="text-2xl font-bold text-slate-900 mb-2">أحسنت! لقد أكملت الاختبار الكامل</h2>
                <p class="text-slate-600 mb-6">درجتك النهائية هي <span class="font-bold text-indigo-600" x-text="score"></span> من أصل <span x-text="questions.length"></span> سؤالاً.</p>
                
                <button 
                    @click="restartQuiz()"
                    class="bg-indigo-600 hover:bg-indigo-700 text-white font-semibold px-8 py-3 rounded-xl transition-all shadow-sm">
                    إعادة الاختبار من البداية 🔄
                </button>
            </div>
        </template>
    </div>

    <script>
        function quizApp() {
            return {
                currentIndex: 0,
                selectedOption: null,
                answered: false,
                score: 0,
                quizFinished: false,
                userAnswers: {},
                batches: [
                    { start: 0, end: 10 },
                    { start: 10, end: 20 },
                    { start: 20, end: 30 },
                    { start: 30, end: 40 },
                    { start: 40, end: 50 },
                    { start: 50, end: 60 },
                    { start: 60, end: 63 }
                ],
                questions: [
                    {
                        questionEn: "1. Which statement correctly describes FTP directory browsing (Lecture 4, slide 36)?",
                        questionAr: "1. أي عبارة تصف بشكل صحيح استعراض الدليل في بروتوكول FTP (المحاضرة 4، الشريحة 36)؟",
                        options: [
                            { label: "A", textEn: "Ability of a client to request a folder listing on an FTP server.", textAr: "قدرة العميل على طلب قائمة بالمجلدات على خادم FTP.", isCorrect: true },
                            { label: "B", textEn: "A resource address that begins with ftp://.", textAr: "عنوان مورد يبدأ بـ ftp://.", isCorrect: false },
                            { label: "C", textEn: "Support for controlling access to shared files and directories.", textAr: "دعم التحكم في الوصول إلى الملفات والمجلدات المشتركة.", isCorrect: false },
                            { label: "D", textEn: "Text-based commands exchanged between FTP clients and servers.", textAr: "أوامر نصية يتم تبادلها بين عملاء وخوادم FTP.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "2. Why might a last-updated date be useful to readers?",
                        questionAr: "2. لماذا قد يكون تاريخ آخر تحديث مفيداً للقراء؟",
                        options: [
                            { label: "A", textEn: "It makes tables unnecessary.", textAr: "يجعل الجداول غير ضرورية.", isCorrect: false },
                            { label: "B", textEn: "It helps them assess how current the content is.", textAr: "يساعدهم على تقييم مدى حداثة المحتوى.", isCorrect: true },
                            { label: "C", textEn: "It replaces evidence for every claim.", textAr: "يحل محل الأدلة لكل ادعاء.", isCorrect: false },
                            { label: "D", textEn: "It automatically improves network security.", textAr: "يحسن أمان الشبكة تلقائياً.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "3. Which statement correctly describes Resource sharing (Lecture 1, slide 12)?",
                        questionAr: "3. أي عبارة تصف بشكل صحيح مشاركة الموارد (المحاضرة 1، الشريحة 12)؟",
                        options: [
                            { label: "A", textEn: "Interconnected computers and hardware that share resources and information through communication channels.", textAr: "أجهزة حاسوب وأجهزة مترابطة تشارك الموارد والمعلومات عبر قنوات الاتصال.", isCorrect: false },
                            { label: "B", textEn: "The process of planning and creating a website.", textAr: "عملية تخطيط وإنشاء موقع ويب.", isCorrect: false },
                            { label: "C", textEn: "A global network of interconnected networks using standardized protocols.", textAr: "شبكة عالمية من الشبكات المترابطة باستخدام بروتوكولات قياسية.", isCorrect: false },
                            { label: "D", textEn: "The original motivation for connecting expensive centralized computers and peripherals.", textAr: "الدافع الأصلي لتوصيل الحواسيب المركزية باهظة الثمن والملحقات.", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "4. Which statement correctly describes Static HTML page (Lecture 4, slide 13)?",
                        questionAr: "4. أي عبارة تصف بشكل صحيح صفحة HTML الثابتة (المحاضرة 4، الشريحة 13)؟",
                        options: [
                            { label: "A", textEn: "Web resource delivered as an existing HTML file rather than generated in response to user input.", textAr: "مورد ويب يتم تسليمه كملف HTML موجود بدلاً من إنشائه استجابة لإدخال المستخدم.", isCorrect: true },
                            { label: "B", textEn: "Page whose content is generated by a server program using inputs or processing.", textAr: "صفحة يتم إنشاء محتواها بواسطة برنامج خادم باستخدام المدخلات أو المعالجة.", isCorrect: false },
                            { label: "C", textEn: "User-side computer/application that provides an interface and initiates service requests.", textAr: "حاسوب/تطبيق من جانب المستخدم يوفر واجهة ويبدأ طلبات الخدمة.", isCorrect: false },
                            { label: "D", textEn: "Server-side Common Gateway Interface program enabling generated dynamic web content.", textAr: "برنامج بوابة مشتركة من جانب الخادم يتيح محتوى ويب ديناميكي تم إنشاؤه.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "5. Which statement correctly describes WordPress (Lecture 2, slide 6)?",
                        questionAr: "5. أي عبارة تصف بشكل صحيح نظام ووردبريس (المحاضرة 2، الشريحة 6)؟",
                        options: [
                            { label: "A", textEn: "A server arrangement in which multiple smaller websites share one host.", textAr: "ترتيب خادم تشترك فيه عدة مواقع ويب أصغر في مضيف واحد.", isCorrect: false },
                            { label: "B", textEn: "Example of a web-based interface for publishing and managing website content.", textAr: "مثال على واجهة تعتمد على الويب لنشر وإدارة محتوى موقع الويب.", isCorrect: true },
                            { label: "C", textEn: "Example of a service for hosting and publishing blogs.", textAr: "مثال على خدمة لاستضافة ونشر المدونات.", isCorrect: false },
                            { label: "D", textEn: "Making website content and layout publicly available through a hosting server.", textAr: "جعل محتوى موقع الويب وتخطيطه متاحين للجمهور من خلال خادم استضافة.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "6. A user submits a form and server-side code generates the resulting page. Which mechanism is illustrated?",
                        questionAr: "6. يقوم المستخدم بإرسال نموذج ويقوم الكود الموجود على الخادم بتوليد الصفحة الناتجة. ما هي الآلية الموضحة؟",
                        options: [
                            { label: "A", textEn: "Direct static file delivery without server processing.", textAr: "تسليم ملف ثابت مباشرة بدون معالجة الخادم.", isCorrect: false },
                            { label: "B", textEn: "Dynamic page generation using a CGI program.", textAr: "توليد صفحة ديناميكية باستخدام برنامج CGI.", isCorrect: true },
                            { label: "C", textEn: "Browser tab creation.", textAr: "إنشاء علامة تبويب للمتصفح.", isCorrect: false },
                            { label: "D", textEn: "Classful prefix assignment.", textAr: "تعيين بادئة فئوية.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "7. Which example is specifically listed as social media in the lecture?",
                        questionAr: "7. ما هو المثال المحدد المدرج كوسائط اجتماعية في المحاضرة؟",
                        options: [
                            { label: "A", textEn: "A router gateway table.", textAr: "جدول بوابة الموجه (Router).", isCorrect: false },
                            { label: "B", textEn: "An HTML DOCTYPE declaration.", textAr: "إعلان DOCTYPE في HTML.", isCorrect: false },
                            { label: "C", textEn: "YouTube video sharing.", textAr: "مشاركة فيديو يوتيوب.", isCorrect: true },
                            { label: "D", textEn: "An IP binary prefix.", textAr: "بادئة ثنائية لعنوان IP.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "8. Which statement correctly describes File server (Lecture 1, slide 19)?",
                        questionAr: "8. أي عبارة تصف بشكل صحيح خادم الملفات (المحاضرة 1، الشريحة 19)؟",
                        options: [
                            { label: "A", textEn: "Protecting data in transit by obscuring its readable contents.", textAr: "حماية البيانات أثناء النقل عن طريق إخفاء محتوياتها المقروءة.", isCorrect: false },
                            { label: "B", textEn: "Checking a user identity before permitting access to a file.", textAr: "التحقق من هوية المستخدم قبل السماح بالوصول إلى ملف.", isCorrect: false },
                            { label: "C", textEn: "A location that allows colleagues to access shared files.", textAr: "موقع يتيح للزملاء الوصول إلى الملفات المشتركة.", isCorrect: true },
                            { label: "D", textEn: "A computer file sent together with an electronic mail message.", textAr: "ملف حاسوبي يتم إرساله مع رسالة بريد إلكتروني.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "9. In Lecture 4, which term or concept best matches this description: Web resource delivered as an existing HTML file rather than generated in response to user input?",
                        questionAr: "9. في المحاضرة 4، ما المصطلح الذي يطابق: مورد ويب يتم تسليمه كملف HTML موجود بدلاً من توليده استجابة لمدخلات المستخدم؟",
                        options: [
                            { label: "A", textEn: "Client.", textAr: "العميل.", isCorrect: false },
                            { label: "B", textEn: "Static HTML page.", textAr: "صفحة HTML الثابتة.", isCorrect: true },
                            { label: "C", textEn: "CGI.", textAr: "واجهة البوابة المشتركة (CGI).", isCorrect: false },
                            { label: "D", textEn: "Dynamic web page.", textAr: "صفحة ويب ديناميكية.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "10. Which pair commonly produces comparable italic appearance in the lecture?",
                        questionAr: "10. أي زوج ينتج عادةً مظهرًا مائلاً (Italic) متماثلاً في المحاضرة؟",
                        options: [
                            { label: "A", textEn: "<table> and <caption>.", textAr: "<table> و <caption>.", isCorrect: false },
                            { label: "B", textEn: "<li> and <ul>.", textAr: "<li> و <ul>.", isCorrect: false },
                            { label: "C", textEn: "<em> and <i>.", textAr: "<em> و <i>.", isCorrect: true },
                            { label: "D", textEn: "<sub> and <sup>.", textAr: "<sub> و <sup>.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "11. A client provides the presentation and much of the application logic while a server stores the database. Which design is this?",
                        questionAr: "11. يوفر العميل العرض ومعظم منطق التطبيق بينما يخزن الخادم قاعدة البيانات. ما هو هذا التصميم؟",
                        options: [
                            { label: "A", textEn: "Two-tier architecture.", textAr: "هندسة الطبقتين (Two-tier architecture).", isCorrect: true },
                            { label: "B", textEn: "One-tier architecture with all software on one device.", textAr: "هندسة الطبقة الواحدة.", isCorrect: false },
                            { label: "C", textEn: "A browser without a client/server model.", textAr: "متصفح بدون نموذج عميل/خادم.", isCorrect: false },
                            { label: "D", textEn: "Three-tier with an independent business server.", textAr: "هندسة الثلاث طبقات.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "12. Which statement correctly describes <sub> (Lecture 3, slide 28)?",
                        questionAr: "12. أي عبارة تصف بشكل صحيح <sub> (المحاضرة 3، الشريحة 28)؟",
                        options: [
                            { label: "A", textEn: "Formatting element for bigger text.", textAr: "عنصر تنسيق للنص الأكبر.", isCorrect: false },
                            { label: "B", textEn: "Formatting element that places text in subscript.", textAr: "عنصر تنسيق يضع النص في وضع منخفض (Subscript).", isCorrect: true },
                            { label: "C", textEn: "Formatting element conventionally rendering text in bold.", textAr: "عنصر تنسيق يعرض النص بخط عريض.", isCorrect: false },
                            { label: "D", textEn: "Formatting element that places text in superscript.", textAr: "عنصر تنسيق يضع النص في وضع مرتفع (Superscript).", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "13. In Lecture 4, which term or concept best matches this description: Support for controlling access to shared files and directories?",
                        questionAr: "13. في المحاضرة 4، ما المصطلح الذي يطابق: دعم التحكم في الوصول إلى الملفات والمجلدات المشتركة؟",
                        options: [
                            { label: "A", textEn: "FTP ASCII control messages.", textAr: "رسائل التحكم ASCII في FTP.", isCorrect: false },
                            { label: "B", textEn: "FTP ownership and authentication.", textAr: "الملكية والمصادقة في FTP.", isCorrect: true },
                            { label: "C", textEn: "FTP heterogeneity support.", textAr: "دعم التباين في FTP.", isCorrect: false },
                            { label: "D", textEn: "FTP directory browsing.", textAr: "استعراض الدليل في FTP.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "14. In Lecture 1, which term or concept best matches this description: The initial page presented by a website?",
                        questionAr: "14. في المحاضرة 1، ما المصطلح الذي يطابق: الصفحة الأولى التي يعرضها موقع الويب؟",
                        options: [
                            { label: "A", textEn: "Web browser.", textAr: "متصفح الويب.", isCorrect: false },
                            { label: "B", textEn: "Web surfing.", textAr: "تصفح الويب.", isCorrect: false },
                            { label: "C", textEn: "Downloading.", textAr: "التنزيل.", isCorrect: false },
                            { label: "D", textEn: "Home page.", textAr: "الصفحة الرئيسية.", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "15. In Lecture 3, which term or concept best matches this description: Element that contains the visible contents of the web page?",
                        questionAr: "15. في المحاضرة 3، ما المصطلح الذي يطابق: العنصر الذي يحتوي على المحتويات المرئية لصفحة الويب؟",
                        options: [
                            { label: "A", textEn: "<P>.", textAr: "<P>.", isCorrect: false },
                            { label: "B", textEn: "<html>.", textAr: "<html>.", isCorrect: false },
                            { label: "C", textEn: "<h1>.", textAr: "<h1>.", isCorrect: false },
                            { label: "D", textEn: "<body>.", textAr: "<body>.", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "16. Which statement correctly describes Two-tier architecture (Lecture 4, slide 50)?",
                        questionAr: "16. أي عبارة تصف بشكل صحيح هندسة الطبقتين (المحاضرة 4، الشريحة 50)؟",
                        options: [
                            { label: "A", textEn: "Deployment separating presentation, application logic, and database access into three tiers.", textAr: "نشر يفصل العرض، والمنطق، وقاعدة البيانات إلى ثلاث طبقات.", isCorrect: false },
                            { label: "B", textEn: "Monolithic deployment with presentation, application logic and data management together.", textAr: "نشر أحادي يجمع العرض والمنطق وإدارة البيانات معاً.", isCorrect: false },
                            { label: "C", textEn: "Deployment where software runs across a client and a server, with tightly coupled presentation and logic.", textAr: "نشر يعمل فيه البرنامج عبر عميل وخادم مع ربط وثيق بين العرض والمنطق.", isCorrect: true },
                            { label: "D", textEn: "Deployment dividing application functionality into multiple separate server-side tiers.", textAr: "نشر يقسم الوظائف إلى طبقات متعددة من جانب الخادم.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "17. In Lecture 4, which term or concept best matches this description: Deployment separating presentation, application/business logic and database access into three tiers?",
                        questionAr: "17. في المحاضرة 4، ما المصطلح الذي يطابق: نشر يفصل العرض ومنطق الأعمال والوصول لقاعدة البيانات إلى ثلاث طبقات؟",
                        options: [
                            { label: "A", textEn: "Presentation logic.", textAr: "منطق العرض.", isCorrect: false },
                            { label: "B", textEn: "Two-tier architecture.", textAr: "هندسة الطبقتين.", isCorrect: false },
                            { label: "C", textEn: "Three-tier architecture.", textAr: "هندسة الثلاث طبقات.", isCorrect: true },
                            { label: "D", textEn: "Multi-tier architecture.", textAr: "الهندسة متعددة الطبقات.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "18. A network administrator allocates addresses within a given network. Which IP part can be assigned locally without global coordination?",
                        questionAr: "18. يقوم مسؤول الشبكة بتخصيص عناوين داخل شبكة معينة. ما هو الجزء من عنوان IP الذي يمكن تعيينه محلياً دون تنسيق عالمي؟",
                        options: [
                            { label: "A", textEn: "The host suffix.", textAr: "لاحقة المضيف (Host suffix).", isCorrect: true },
                            { label: "B", textEn: "The Internet protocol version.", textAr: "إصدار بروتوكول الإنترنت.", isCorrect: false },
                            { label: "C", textEn: "The globally coordinated network prefix.", textAr: "بادئة الشبكة المنسقة عالمياً.", isCorrect: false },
                            { label: "D", textEn: "The overall IPv4 address length.", textAr: "الطول الإجمالي لعنوان IPv4.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "19. Which statement correctly describes MVC architectural pattern (Lecture 4, slide 72)?",
                        questionAr: "19. أي عبارة تصف بشكل صحيح نمط MVC المعماري (المحاضرة 4، الشريحة 72)؟",
                        options: [
                            { label: "A", textEn: "More detailed way of organizing component responsibilities in implementation.", textAr: "طريقة أكثر تفصيلاً لتنظيم مسؤوليات المكونات في التنفيذ.", isCorrect: false },
                            { label: "B", textEn: "Higher-level strategy organizing large-scale application components.", textAr: "استراتيجية رفيعة المستوى لتنظيم مكونات التطبيق واسعة النطاق.", isCorrect: true },
                            { label: "C", textEn: "Benefit from isolating responsibilities so individual pieces can be tested more easily.", textAr: "فائدة من عزل المسؤوليات لاختبار الأجزاء بسهولة.", isCorrect: false },
                            { label: "D", textEn: "Design pattern that the lecture compares semantically to MVC.", textAr: "نمط تصميم تقارنه المحاضرة دلالياً بـ MVC.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "20. A site uses dozens of conflicting bright colors on every line. What principle does this violate?",
                        questionAr: "20. يستخدم موقع عشرات الألوان الزاهية المتضاربة في كل سطر. ما المبدأ الذي يخالفه ذلك؟",
                        options: [
                            { label: "A", textEn: "Always use only warm colors.", textAr: "استخدم دائماً الألوان الدافئة فقط.", isCorrect: false },
                            { label: "B", textEn: "Use a dedicated web host for color.", textAr: "استخدم مضيف ويب مخصص للألوان.", isCorrect: false },
                            { label: "C", textEn: "Every hyperlink needs an FTP URL.", textAr: "كل رابط تشعبي يحتاج عنوان FTP.", isCorrect: false },
                            { label: "D", textEn: "Avoid excessive or inappropriate color use.", textAr: "تجنب الاستخدام المفرط أو غير المناسب للألوان.", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "21. Which statement correctly describes <dt> (Lecture 3, slide 41)?",
                        questionAr: "21. أي عبارة تصف بشكل صحيح <dt> (المحاضرة 3، الشريحة 41)؟",
                        options: [
                            { label: "A", textEn: "Element enclosing a list of terms with descriptions.", textAr: "عنصر يتضمن قائمة بالنقاط مع أوصافها.", isCorrect: false },
                            { label: "B", textEn: "Element defining the description of a definition-list term.", textAr: "عنصر يحدد وصف المصطلح في قائمة التعاريف.", isCorrect: false },
                            { label: "C", textEn: "Element defining a term or item in a definition list.", textAr: "عنصر يحدد مصطلحاً أو عنصراً في قائمة التعاريف.", isCorrect: true },
                            { label: "D", textEn: "Element starting a bulleted unordered list.", textAr: "عنصر يبدأ قائمة غير مرتبة منقطة.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "22. Which disadvantage of two-tier architecture is emphasized in the lecture?",
                        questionAr: "22. ما السلبية التي تم التأكيد عليها لهندسة الطبقتين في المحاضرة؟",
                        options: [
                            { label: "A", textEn: "An inability to run a database on the server.", textAr: "عدم القدرة على تشغيل قاعدة بيانات على الخادم.", isCorrect: false },
                            { label: "B", textEn: "Limited scalability.", textAr: "محدودية القابلية للتوسع (Limited scalability).", isCorrect: true },
                            { label: "C", textEn: "Mandatory six-tier deployment.", textAr: "إلزامية النشر على ست طبقات.", isCorrect: false },
                            { label: "D", textEn: "No possible portability advantage.", textAr: "عدم وجود أي ميزة للمحمولية.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "23. In Lecture 1, which term or concept best matches: Sending digital data in small addressed bundles over shared communication paths?",
                        questionAr: "23. في المحاضرة 1، ما المصطلح الذي يطابق: إرسال البيانات الرقمية في حزم صغيرة معنونة عبر مسارات اتصال مشتركة؟",
                        options: [
                            { label: "A", textEn: "Packet addressing information.", textAr: "معلومات عنوان الحزمة.", isCorrect: false },
                            { label: "B", textEn: "ARPA.", textAr: "وكالة مشاريع البحوث المتقدمة ARPA.", isCorrect: false },
                            { label: "C", textEn: "Packet sequencing information.", textAr: "معلومات تسلسل الحزمة.", isCorrect: false },
                            { label: "D", textEn: "Packet switching.", textAr: "تبديل الحزم (Packet switching).", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "24. In Lecture 2, which term or concept best matches: Using visual differences such as color, size or text style to attract attention?",
                        questionAr: "24. في المحاضرة 2، ما المصطلح الذي يطابق: استخدام الاختلافات البصرية مثل اللون أو الحجم لجذب الانتباه؟",
                        options: [
                            { label: "A", textEn: "Unity.", textAr: "الوحدة (Unity).", isCorrect: false },
                            { label: "B", textEn: "Focus.", textAr: "التركيز (Focus).", isCorrect: false },
                            { label: "C", textEn: "Contrast.", textAr: "التباين (Contrast).", isCorrect: true },
                            { label: "D", textEn: "White space.", textAr: "المساحة البيضاء (White space).", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "25. What does rowspan=\"2\" on a table header specify?",
                        questionAr: "25. ماذا يحدد الخيار rowspan=\"2\" في رأس الجدول؟",
                        options: [
                            { label: "A", textEn: "That the header is the second column.", textAr: "أن الرأس هو العمود الثاني.", isCorrect: false },
                            { label: "B", textEn: "That two separate tables are created.", textAr: "أنه يتم إنشاء جدولين منفصلين.", isCorrect: false },
                            { label: "C", textEn: "That it spans two columns.", textAr: "أنه يمتد على عمودين.", isCorrect: false },
                            { label: "D", textEn: "That the header occupies the height of two rows.", textAr: "أن الرأس يشغل ارتفاع صفين.", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "26. In Lecture 2, which term or concept best matches: Publishing advantage of making it easy to interact with a worldwide audience?",
                        questionAr: "26. في المحاضرة 2، ما المصطلح الذي يطابق: ميزة النشر في تسهيل التفاعل مع جمهور عالمي؟",
                        options: [
                            { label: "A", textEn: "Connectivity.", textAr: "الترابط/الاتصالية (Connectivity).", isCorrect: true },
                            { label: "B", textEn: "Timely content.", textAr: "المحتوى في الوقت المناسب.", isCorrect: false },
                            { label: "C", textEn: "Rapid and economical delivery.", textAr: "التسليم السريع والاقتصادي.", isCorrect: false },
                            { label: "D", textEn: "Reduced production costs.", textAr: "تقليل تكاليف الإنتاج.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "27. Why should a website regularly refresh relevant material?",
                        questionAr: "27. لماذا يجب على موقع الويب تحديث المواد ذات الصلة بانتظام؟",
                        options: [
                            { label: "A", textEn: "To retain visitor interest with timely information.", textAr: "للحفاظ على اهتمام الزوار بالمعلومات الحديثة.", isCorrect: true },
                            { label: "B", textEn: "Because web files cannot be stored.", textAr: "لأن ملفات الويب لا يمكن تخزينها.", isCorrect: false },
                            { label: "C", textEn: "To prevent viewers from using a browser.", textAr: "للمنع من استخدام المتصفح.", isCorrect: false },
                            { label: "D", textEn: "Because hyperlinks need changing every second.", textAr: "لأن الروابط تحتاج للتغيير كل ثانية.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "28. Which statement correctly describes Two-tier scalability limitation (Lecture 4, slide 51)?",
                        questionAr: "28. أي عبارة تصف بشكل صحيح حد القابلية للتوسع في هندسة الطبقتين (المحاضرة 4، الشريحة 51)؟",
                        options: [
                            { label: "A", textEn: "A drawback of two-tier applications when the number of clients grows.", textAr: "سلبيّة للتطبيقات ذات الطبقتين عندما يزداد عدد العملاء.", isCorrect: true },
                            { label: "B", textEn: "Client/server risk caused by excessive concurrent client requests.", textAr: "خطر العميل/الخادم الناتج عن الطلبات المتزامنة.", isCorrect: false },
                            { label: "C", textEn: "Client/server benefit making shared information easier to administer.", textAr: "ميزة تسهل إدارة المعلومات المشتركة.", isCorrect: false },
                            { label: "D", textEn: "System keeping all code and processing on a single machine.", textAr: "نظام يحتفظ بكل الكود والمعالجة على جهاز واحد.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "29. In Lecture 4, which term or concept best matches: Design pattern that the lecture compares semantically to MVC?",
                        questionAr: "29. في المحاضرة 4، ما نمط التصميم الذي تقارنه المحاضرة دلالياً بنمط MVC؟",
                        options: [
                            { label: "A", textEn: "Observer pattern.", textAr: "نمط المراقب (Observer pattern).", isCorrect: true },
                            { label: "B", textEn: "MVC business layer.", textAr: "طبقة الأعمال في MVC.", isCorrect: false },
                            { label: "C", textEn: "MVC design pattern.", textAr: "نمط تصميم MVC.", isCorrect: false },
                            { label: "D", textEn: "MVC display layer.", textAr: "طبقة العرض في MVC.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "30. Which term names the programming routines that expose TCP/IP functionality to applications?",
                        questionAr: "30. ما المصطلح الذي يطلق على الإجراءات البرمجية التي تتيح وظائف TCP/IP للتطبيقات؟",
                        options: [
                            { label: "A", textEn: "Hyperlinks.", textAr: "الروابط التشعبية.", isCorrect: false },
                            { label: "B", textEn: "CSS styles.", textAr: "أنماط CSS.", isCorrect: false },
                            { label: "C", textEn: "Sockets.", textAr: "المقابض (Sockets).", isCorrect: true },
                            { label: "D", textEn: "Table headers.", textAr: "رؤوس الجداول.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "31. In Lecture 1, which term or concept best matches: A technical specification issued by the W3C, rather than a software product?",
                        questionAr: "31. في المحاضرة 1، ما المصطلح الذي يطابق: مواصفة فنية صادرة عن W3C وليست منتجاً برمجياً؟",
                        options: [
                            { label: "A", textEn: "World Wide Web Consortium (W3C).", textAr: "رابطة الشبكة العالمية W3C.", isCorrect: false },
                            { label: "B", textEn: "CSS.", textAr: "أنماط CSS.", isCorrect: false },
                            { label: "C", textEn: "W3C Recommendation.", textAr: "توصية منظمة W3C.", isCorrect: true },
                            { label: "D", textEn: "XHTML.", textAr: "لغة XHTML.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "32. In Lecture 2, which term or concept best matches: Quality of writing supported by headings, lists and short clearly paragraphed passages?",
                        questionAr: "32. في المحاضرة 2، ما المصطلح الذي يطابق: جودة الكتابة المدعومة بالرؤوس والقوائم والفقرات القصيرة الواضحة؟",
                        options: [
                            { label: "A", textEn: "Inverted pyramid writing.", textAr: "كتابة الهرم المقلوب.", isCorrect: false },
                            { label: "B", textEn: "Accuracy.", textAr: "الدقة.", isCorrect: false },
                            { label: "C", textEn: "Scannability.", textAr: "قابلية المسح السريع (Scannability).", isCorrect: true },
                            { label: "D", textEn: "Conciseness.", textAr: "الإيجاز.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "33. A router receives a packet and must decide which network to send it to next. Which protocol performs addressing-based forwarding?",
                        questionAr: "33. يستلم الموجه حزمة ويجب أن يقرر الشبكة التالية لإرسالها إليها. ما البروتوكول الذي يقوم بالتوجيه بناءً على العناوين؟",
                        options: [
                            { label: "A", textEn: "XHTML.", textAr: "XHTML.", isCorrect: false },
                            { label: "B", textEn: "CSS.", textAr: "CSS.", isCorrect: false },
                            { label: "C", textEn: "HTML.", textAr: "HTML.", isCorrect: false },
                            { label: "D", textEn: "IP.", textAr: "بروتوكول الإنترنت (IP).", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "34. Which statement correctly describes Web page (Lecture 1, slide 26)?",
                        questionAr: "34. أي عبارة تصف بشكل صحيح صفحة الويب (المحاضرة 1، الشريحة 26)؟",
                        options: [
                            { label: "A", textEn: "An electronic document that is part of the World Wide Web.", textAr: "مستند إلكتروني يشكل جزءاً من الشبكة العالمية.", isCorrect: true },
                            { label: "B", textEn: "Software that reads web pages and lets users follow hyperlinks.", textAr: "برنامج يقرأ صفحات الويب ويسمح بالانتقال بين الروابط.", isCorrect: false },
                            { label: "C", textEn: "A computer that delivers requested web pages to browsers.", textAr: "حاسوب يقدم صفحات الويب المطلوبة للمتصفحات.", isCorrect: false },
                            { label: "D", textEn: "A collection of related web pages and associated items.", textAr: "مجموعة من صفحات الويب المرتبطة.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "35. In Lecture 4, which term or concept best matches: Higher-level strategy organizing large-scale application components?",
                        questionAr: "35. في المحاضرة 4، ما المصطلح الذي يطابق: استراتيجية رفيعة المستوى لتنظيم مكونات التطبيقات واسعة النطاق؟",
                        options: [
                            { label: "A", textEn: "Observer pattern.", textAr: "نمط المراقب.", isCorrect: false },
                            { label: "B", textEn: "MVC architectural pattern.", textAr: "النمط المعماري MVC.", isCorrect: true },
                            { label: "C", textEn: "MVC testability.", textAr: "قابلية اختبار MVC.", isCorrect: false },
                            { label: "D", textEn: "MVC design pattern.", textAr: "نمط تصميم MVC.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "36. In Lecture 3, which term or concept best matches: Element enclosing a list of terms with descriptions?",
                        questionAr: "36. في المحاضرة 3، ما المصطلح الذي يطابق: العنصر الذي يحيط بقائمة من المصطلحات وأوصافها؟",
                        options: [
                            { label: "A", textEn: "<li>.", textAr: "<li>.", isCorrect: false },
                            { label: "B", textEn: "<dd>.", textAr: "<dd>.", isCorrect: false },
                            { label: "C", textEn: "<dt>.", textAr: "<dt>.", isCorrect: false },
                            { label: "D", textEn: "<dl>.", textAr: "<dl> (Definition List).", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "37. What distinguishes a closing tag from its matching opening tag in common HTML elements?",
                        questionAr: "37. ما الذي يميز وسم الإغلاق عن وسم الفتح المقابل له في عناصر HTML؟",
                        options: [
                            { label: "A", textEn: "A forward slash before the tag name.", textAr: "شرطة مائلة للامام (/) قبل اسم الوسم.", isCorrect: true },
                            { label: "B", textEn: "An extra hash mark at the start.", textAr: "علامة هاش إضافية في البداية.", isCorrect: false },
                            { label: "C", textEn: "A backslash after the closing angle bracket.", textAr: "شرطة مائلة للخلف بعد القوس.", isCorrect: false },
                            { label: "D", textEn: "A required numeric port.", textAr: "منفذ رقمي مطلوب.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "38. Which statement correctly describes Document name (Lecture 4, slide 7)?",
                        questionAr: "38. أي عبارة تصف بشكل صحيح اسم المستند (المحاضرة 4، الشريحة 7)؟",
                        options: [
                            { label: "A", textEn: "Port that the lecture assumes for an HTTP address.", textAr: "المنفذ الافتراضي لعنوان HTTP.", isCorrect: false },
                            { label: "B", textEn: "Optional URL part identifying the network service port.", textAr: "جزء اختياري من URL يحدد منفذ الخدمة.", isCorrect: false },
                            { label: "C", textEn: "Optional URL component naming the requested document on a computer.", textAr: "مكون اختياري في URL يحدد اسم المستند المطلوب على الحاسوب.", isCorrect: true },
                            { label: "D", textEn: "Optional URL component conveying extra values.", textAr: "مكون اختياري ينقل قيم إضافية.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "39. A university links several computers to the same network printer. Which early networking goal does this illustrate?",
                        questionAr: "39. تربط جامعة عدة أجهزة بحاسوب بطابعة شبكية واحدة. ما هدف الشبكات المبكر الذي يوضحه هذا؟",
                        options: [
                            { label: "A", textEn: "Using packet sequencing.", textAr: "استخدام تسلسل الحزم.", isCorrect: false },
                            { label: "B", textEn: "Sharing peripheral devices.", textAr: "مشاركة الأجهزة الملحقة (Peripheral devices).", isCorrect: true },
                            { label: "C", textEn: "Converting domain names to IP addresses.", textAr: "تحويل أسماء النطاقات إلى عناوين IP.", isCorrect: false },
                            { label: "D", textEn: "Publishing multimedia on the Web.", textAr: "نشر الوسائط المتعددة على الويب.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "40. In Lecture 1, which term or concept best matches: Information inside a packet used to route it to its destination?",
                        questionAr: "40. في المحاضرة 1، ما المصطلح الذي يطابق: المعلومات داخل الحزمة المستخدمة لتوجيهها إلى وجهتها؟",
                        options: [
                            { label: "A", textEn: "Packet addressing information.", textAr: "معلومات عنوان الحزمة (Packet addressing).", isCorrect: true },
                            { label: "B", textEn: "Packet switching.", textAr: "تبديل الحزم.", isCorrect: false },
                            { label: "C", textEn: "Packet sequencing information.", textAr: "معلومات تسلسل الحزم.", isCorrect: false },
                            { label: "D", textEn: "Packet error-control information.", textAr: "معلومات التحكم بالخطأ.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "41. Which statement correctly describes Computer network (Lecture 1, slide 8)?",
                        questionAr: "41. أي عبارة تصف بشكل صحيح شبكة الحاسوب (المحاضرة 1، الشريحة 8)؟",
                        options: [
                            { label: "A", textEn: "The original motivation for connecting expensive computers.", textAr: "الدافع الأصلي لتوصيل أجهزة الحاسوب باهظة الثمن.", isCorrect: false },
                            { label: "B", textEn: "A global network of interconnected networks.", textAr: "شبكة عالمية من الشبكات المترابطة.", isCorrect: false },
                            { label: "C", textEn: "Interconnected computers and hardware that share resources and information through communication channels.", textAr: "أجهزة حاسوب وأعتدة مترابطة تشارك الموارد والمعلومات عبر قنوات الاتصال.", isCorrect: true },
                            { label: "D", textEn: "Common Internet protocols enabling communication.", textAr: "بروتوكولات إنترنت مشتركة تتيح الاتصال.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "42. Which benefit of client/server architecture follows from keeping shared data in one place?",
                        questionAr: "42. ما فائدة معماريّة العميل/الخادم الناتجة عن الاحتفاظ بالبيانات المشتركة في مكان واحد؟",
                        options: [
                            { label: "A", textEn: "A guarantee that the server never fails.", textAr: "ضمان عدم فشل الخادم أبداً.", isCorrect: false },
                            { label: "B", textEn: "Elimination of every server dependency.", textAr: "إزالة كل الاعتماد على الخادم.", isCorrect: false },
                            { label: "C", textEn: "No possible network congestion.", textAr: "عدم حدوث أي ازدحام في الشبكة.", isCorrect: false },
                            { label: "D", textEn: "Easier administration and consistent access controls.", textAr: "إدارة أسهل وعناصر تحكم وصول متسقة.", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "43. In Lecture 4, which term or concept best matches: User-side computer/application that provides an interface and initiates service requests?",
                        questionAr: "43. في المحاضرة 4، ما المصطلح الذي يطابق: حاسوب/تطبيق من جانب المستخدم يوفر الواجهة ويبدأ طلبات الخدمة؟",
                        options: [
                            { label: "A", textEn: "Client.", textAr: "العميل (Client).", isCorrect: true },
                            { label: "B", textEn: "Server.", textAr: "الخادم.", isCorrect: false },
                            { label: "C", textEn: "Dynamic web page.", textAr: "صفحة ويب ديناميكية.", isCorrect: false },
                            { label: "D", textEn: "Connection-oriented communication.", textAr: "اتصال موجه بالاتصال.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "44. In Lecture 1, which term or concept best matches: The URL component identifying the server or host to contact?",
                        questionAr: "44. في المحاضرة 1، ما المصطلح الذي يطابق: مكون URL الذي يحدد الخادم أو المضيف المراد الاتصال به؟",
                        options: [
                            { label: "A", textEn: "URL domain name.", textAr: "اسم نطاق الـ URL.", isCorrect: true },
                            { label: "B", textEn: "URL directory path.", textAr: "مسار دليل الـ URL.", isCorrect: false },
                            { label: "C", textEn: "URL scheme/protocol.", textAr: "بروتوكول الـ URL.", isCorrect: false },
                            { label: "D", textEn: "URL document filename.", textAr: "اسم ملف المستند في الـ URL.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "45. Which statement correctly describes colspan (Lecture 3, slide 35)?",
                        questionAr: "45. أي عبارة تصف بشكل صحيح colspan (المحاضرة 3، الشريحة 35)؟",
                        options: [
                            { label: "A", textEn: "Element defining a table caption.", textAr: "عنصر يحدد عنوان الجدول.", isCorrect: false },
                            { label: "B", textEn: "Table-cell attribute specifying how many rows a cell spans.", textAr: "خاصية تتحدد عدد الصفوف التي تمتد عليها الخلية.", isCorrect: false },
                            { label: "C", textEn: "Table attribute used to show a visible border.", textAr: "خاصية لإظهار حدود الجدول.", isCorrect: false },
                            { label: "D", textEn: "Table-cell attribute specifying how many columns a cell spans.", textAr: "خاصية لخلية الجدول تحدد عدد الأعمدة التي تمتد عليها الخلية.", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "46. In Lecture 4, which term or concept best matches: Server executing programs, transactions and business-logic processing?",
                        questionAr: "46. في المحاضرة 4، ما المصطلح الذي يطابق: خادم ينفذ البرامج والمعاملات ومعالجة منطق الأعمال؟",
                        options: [
                            { label: "A", textEn: "Web server.", textAr: "خادم الويب.", isCorrect: false },
                            { label: "B", textEn: "Application server.", textAr: "خادم التطبيقات (Application server).", isCorrect: true },
                            { label: "C", textEn: "Mail server.", textAr: "خادم البريد الإلكتروني.", isCorrect: false },
                            { label: "D", textEn: "Database server.", textAr: "خادم قواعد البيانات.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "47. In Lecture 1, which term or concept best matches: Information included in packets to support error checking?",
                        questionAr: "47. في المحاضرة 1، ما المصطلح الذي يطابق: المعلومات المضمنة في الحزم لدعم فحص الأخطاء؟",
                        options: [
                            { label: "A", textEn: "ARPANET.", textAr: "شبكة ARPANET.", isCorrect: false },
                            { label: "B", textEn: "ARPA.", textAr: "وكالة ARPA.", isCorrect: false },
                            { label: "C", textEn: "Packet error-control information.", textAr: "معلومات التحكم بالخطأ في الحزم.", isCorrect: true },
                            { label: "D", textEn: "Packet sequencing information.", textAr: "معلومات تسلسل الحزم.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "48. In Lecture 4, which term or concept best matches: Protocol for transferring files of arbitrary content between computers?",
                        questionAr: "48. في المحاضرة 4، ما المصطلح الذي يطابق: بروتوكول لنقل الملفات ذات المحتوى الاختياري بين الحواسيب؟",
                        options: [
                            { label: "A", textEn: "FTP URL.", textAr: "عنوان FTP URL.", isCorrect: false },
                            { label: "B", textEn: "FTP.", textAr: "بروتوكول نقل الملفات (FTP).", isCorrect: true },
                            { label: "C", textEn: "FTP directory browsing.", textAr: "استعراض دليل FTP.", isCorrect: false },
                            { label: "D", textEn: "FTP data connection.", textAr: "اتصال بيانات FTP.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "49. A label \"Telephone\" must extend vertically beside two separate number rows. Which attribute is appropriate?",
                        questionAr: "49. تسمية \"الهاتف\" يجب أن تمتد رأسياً بجانب صفين رقميين منفصلين. ما الخاصية المناسبة؟",
                        options: [
                            { label: "A", textEn: "colspan=\"2\".", textAr: "colspan=\"2\".", isCorrect: false },
                            { label: "B", textEn: "src=\"2\".", textAr: "src=\"2\".", isCorrect: false },
                            { label: "C", textEn: "border=\"2\".", textAr: "border=\"2\".", isCorrect: false },
                            { label: "D", textEn: "rowspan=\"2\".", textAr: "rowspan=\"2\".", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "50. Under classful examples, to which class does 00000001 00001011 00001011 11101111 belong?",
                        questionAr: "50. تحت أمثلة الفئات التقليدية (Classful)، إلى أي فئة ينتمي العنوان 00000001 00001011 00001011 11101111؟",
                        options: [
                            { label: "A", textEn: "Class B.", textAr: "الفئة B.", isCorrect: false },
                            { label: "B", textEn: "Class A.", textAr: "الفئة Class A (تبدأ بالبت 0).", isCorrect: true },
                            { label: "C", textEn: "Class C.", textAr: "الفئة C.", isCorrect: false },
                            { label: "D", textEn: "Class D.", textAr: "الفئة D.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "51. Which statement correctly describes title (Lecture 3, slide 23)?",
                        questionAr: "51. أي عبارة تصف بشكل صحيح خاصية title (المحاضرة 3، الشريحة 23)؟",
                        options: [
                            { label: "A", textEn: "Element that creates the browser window title only.", textAr: "عنصر ينشئ عنوان نافذة المتصفح فقط.", isCorrect: false },
                            { label: "B", textEn: "Attribute specifying an image file path.", textAr: "خاصية تحدد مسار ملف الصورة.", isCorrect: false },
                            { label: "C", textEn: "Attribute used to merge table columns.", textAr: "خاصية تستخدم لدمج أعمدة الجدول.", isCorrect: false },
                            { label: "D", textEn: "Attribute holding extra information commonly displayed as a tooltip.", textAr: "خاصية تحوي معلومات إضافية تظهر عادة كتلميح (Tooltip).", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "52. Which statement correctly describes Model data storage (Lecture 4, slide 80)?",
                        questionAr: "52. أي عبارة تصف بشكل صحيح تخزين بيانات النموذج (Model data storage) (المحاضرة 4، الشريحة 80)؟",
                        options: [
                            { label: "A", textEn: "View responsibility for drawing interface controls.", textAr: "مسؤولية العرض (View) لرسم عناصر الواجهة.", isCorrect: false },
                            { label: "B", textEn: "MVC model responsibility for storing application values and state.", textAr: "مسؤولية نموذج MVC لتخزين قيم وحالة التطبيق.", isCorrect: true },
                            { label: "C", textEn: "Controller responsibility for opening browser tabs.", textAr: "مسؤولية المتحكم (Controller) لفتح التبويبات.", isCorrect: false },
                            { label: "D", textEn: "Web server responsibility for storing HTML only.", textAr: "مسؤولية خادم الويب لتخزين HTML فقط.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "53. Which statement correctly describes Class A IPv4 address (Lecture 4, slide 17)?",
                        questionAr: "53. أي عبارة تصف بشكل صحيح عنوان IPv4 من الفئة Class A (المحاضرة 4، الشريحة 17)؟",
                        options: [
                            { label: "A", textEn: "Classful address with leading bits 10 and a 16-bit network field.", textAr: "عنوان يبدأ بالبتات 10 وحقل شبكة 16 بت.", isCorrect: false },
                            { label: "B", textEn: "Classful address with leading bits 110 and a 24-bit network field.", textAr: "عنوان يبدأ بالبتات 110 وحقل شبكة 24 بت.", isCorrect: false },
                            { label: "C", textEn: "Address reserved only for multicast traffic.", textAr: "عنوان مخصص للبث المتعدد فقط.", isCorrect: false },
                            { label: "D", textEn: "Classful address with leading bit 0 and an 8-bit network field.", textAr: "عنوان فئوي يبدأ بالبت 0 مع حقل شبكة بحجم 8 بتات.", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "54. In Lecture 3, which term or concept best matches: Program that interprets HTML tags to display the document as a webpage?",
                        questionAr: "54. في المحاضرة 3، ما المصطلح الذي يطابق: برنامج يفسر وسم HTML ليعرض المستند كصفحة ويب؟",
                        options: [
                            { label: "A", textEn: "Web browser.", textAr: "متصفح الويب (Web browser).", isCorrect: true },
                            { label: "B", textEn: "Web server.", textAr: "خادم الويب.", isCorrect: false },
                            { label: "C", textEn: "FTP client.", textAr: "عميل FTP.", isCorrect: false },
                            { label: "D", textEn: "CSS stylesheet.", textAr: "ورقة أنماط CSS.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "55. Which snippet embeds an image named logo.png?",
                        questionAr: "55. أي مقتطع كود يقوم بتضمين صورة باسم logo.png؟",
                        options: [
                            { label: "A", textEn: "<img src=\"logo.png\" />", textAr: "<img src=\"logo.png\" />", isCorrect: true },
                            { label: "B", textEn: "<a href=\"logo.png\">", textAr: "<a href=\"logo.png\">", isCorrect: false },
                            { label: "C", textEn: "<image href=\"logo.png\">", textAr: "<image href=\"logo.png\">", isCorrect: false },
                            { label: "D", textEn: "<src img=\"logo.png\">", textAr: "<src img=\"logo.png\">", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "56. Someone wants three webpages visible as selectable tabs in one browser window. Which feature helps?",
                        questionAr: "56. يرغب شخص في عرض ثلاث صفحات ويب كعلامات تبويب قابلة للاختيار في نافذة واحدة. ما الميزة التي تساعده؟",
                        options: [
                            { label: "A", textEn: "FTP directory listing.", textAr: "قائمة دليل FTP.", isCorrect: false },
                            { label: "B", textEn: "Domain ownership.", textAr: "ملكية النطاق.", isCorrect: false },
                            { label: "C", textEn: "Tabbed browsing.", textAr: "التصفح المبوب (Tabbed browsing).", isCorrect: true },
                            { label: "D", textEn: "Classful IP addressing.", textAr: "عنونة IP الفئوية.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "57. Which example best reflects domain logic in an MVC model?",
                        questionAr: "57. أي مثال يعكس بشكل أفضل منطق المجال (Domain logic) في نموذج MVC؟",
                        options: [
                            { label: "A", textEn: "Opening a new browser tab.", textAr: "فتح علامة تبويب جديدة.", isCorrect: false },
                            { label: "B", textEn: "Selecting a blue page background only.", textAr: "اختيار خلفية زرقاء للصفحة.", isCorrect: false },
                            { label: "C", textEn: "Calculating cart totals, taxes and shipping.", textAr: "حساب إجماليات سلة التسوق والضرائب والشحن.", isCorrect: true },
                            { label: "D", textEn: "Drawing the Save button location.", textAr: "رسم موقع زر الحفظ.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "58. The lecture timeline notes that in 1986, NSF connected NSFnet to which network?",
                        questionAr: "58. يذكر المخطط الزمني للمحاضرة أنه في عام 1986، قامت NSF بربط شبكة NSFnet بأي شبكة؟",
                        options: [
                            { label: "A", textEn: "W3C.", textAr: "منظمة W3C.", isCorrect: false },
                            { label: "B", textEn: "ARPANET.", textAr: "شبكة ARPANET.", isCorrect: true },
                            { label: "C", textEn: "A single FTP client.", textAr: "عميل FTP واحد.", isCorrect: false },
                            { label: "D", textEn: "YouTube.", textAr: "يوتيوب.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "59. In Lecture 4, which term or concept best matches: Page whose content is generated by a server program using inputs or processing?",
                        questionAr: "59. في المحاضرة 4، ما المصطلح الذي يطابق: صفحة يتم توليد محتواها بواسطة برنامج خادم باستخدام المدخلات؟",
                        options: [
                            { label: "A", textEn: "Client.", textAr: "العميل.", isCorrect: false },
                            { label: "B", textEn: "Static HTML page.", textAr: "صفحة HTML الثابتة.", isCorrect: false },
                            { label: "C", textEn: "CGI.", textAr: "واجهة CGI.", isCorrect: false },
                            { label: "D", textEn: "Dynamic web page.", textAr: "صفحة ويب ديناميكية (Dynamic web page).", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "60. Which statement correctly describes Headings and subheadings (Lecture 2, slide 23)?",
                        questionAr: "60. أي عبارة تصف بشكل صحيح العناوين والعناوين الفرعية (المحاضرة 2، الشريحة 23)؟",
                        options: [
                            { label: "A", textEn: "A background treatment that supports readability.", textAr: "علاج الخلفية لدعم القراءة.", isCorrect: false },
                            { label: "B", textEn: "Font category recommended by the lecture.", textAr: "فئة خط موصى بها في المحاضرة.", isCorrect: false },
                            { label: "C", textEn: "Text elements that help visitors quickly scan website content.", textAr: "عناصر نصية تساعد الزوار على مسح محتوى الموقع بسرعة.", isCorrect: true },
                            { label: "D", textEn: "An opening sentence that states the subject.", textAr: "جملة افتتاحية توضح الموضوع.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "61. Which statement correctly describes Server overload (Lecture 4, slide 59)?",
                        questionAr: "61. أي عبارة تصف بشكل صحيح الحمل الزائد على الخادم (Server overload) (المحاضرة 4، الشريحة 59)؟",
                        options: [
                            { label: "A", textEn: "Client/server benefit making shared information easier to administer.", textAr: "فائدة للعميل/الخادم تجعل إدارة المعلومات أسهل.", isCorrect: false },
                            { label: "B", textEn: "Client/server failure mode that prevents dependent clients from receiving service.", textAr: "نموذج فشل يمنع العملاء من تلقي الخدمة.", isCorrect: false },
                            { label: "C", textEn: "Client/server risk caused by excessive concurrent client requests.", textAr: "خطر في نظام العميل/الخادم ناتج عن طلبات متزامنة مفرطة.", isCorrect: true },
                            { label: "D", textEn: "Intermediate application layer between client and database.", textAr: "طبقة تطبيق وسيطة بين العميل وقاعدة البيانات.", isCorrect: false }
                        ]
                    },
                    {
                        questionEn: "62. A browser requests an HTML page from a machine that sends pages in response. What server type is that?",
                        questionAr: "62. يطلب متصفح صفحة HTML من جهاز يرسل الصفحات كاستجابة. ما نوع هذا الخادم؟",
                        options: [
                            { label: "A", textEn: "A definition-list processor.", textAr: "معالج قائمة التعاريف.", isCorrect: false },
                            { label: "B", textEn: "A W3C Recommendation.", textAr: "توصية W3C.", isCorrect: false },
                            { label: "C", textEn: "A packet sequencing number.", textAr: "رقم تسلسل الحزمة.", isCorrect: false },
                            { label: "D", textEn: "Web server.", textAr: "خادم الويب (Web server).", isCorrect: true }
                        ]
                    },
                    {
                        questionEn: "63. What does ftp:// at the beginning of an address indicate?",
                        questionAr: "63. ماذا يعبر بداية العنوان بـ ftp:// ؟",
                        options: [
                            { label: "A", textEn: "The content is a browser tab.", textAr: "أن المحتوى هو علامة تبويب للمتصفح.", isCorrect: false },
                            { label: "B", textEn: "The address is a CSS rule.", textAr: "أن العنوان هو قاعدة CSS.", isCorrect: false },
                            { label: "C", textEn: "The FTP access protocol.", textAr: "بروتوكول الوصول FTP.", isCorrect: true },
                            { label: "D", textEn: "The document starts with a table.", textAr: "أن المستند يبدأ بجدول.", isCorrect: false }
                        ]
                    }
                ],
                selectOption(index) {
                    this.selectedOption = index;
                    this.answered = true;
                    if (this.questions[this.currentIndex].options[index].isCorrect) {
                        if (!this.userAnswers[this.currentIndex]) {
                            this.score++;
                        }
                    }
                    this.userAnswers[this.currentIndex] = index;
                },
                nextQuestion() {
                    if (this.currentIndex < this.questions.length - 1) {
                        this.currentIndex++;
                        this.loadSavedState();
                    } else {
                        this.quizFinished = true;
                    }
                },
                prevQuestion() {
                    if (this.currentIndex > 0) {
                        this.currentIndex--;
                        this.loadSavedState();
                    }
                },
                jumpToBatch(startIndex) {
                    this.currentIndex = parseInt(startIndex);
                    this.loadSavedState();
                },
                loadSavedState() {
                    if (this.userAnswers[this.currentIndex] !== undefined) {
                        this.selectedOption = this.userAnswers[this.currentIndex];
                        this.answered = true;
                    } else {
                        this.selectedOption = null;
                        this.answered = false;
                    }
                },
                restartQuiz() {
                    this.currentIndex = 0;
                    this.selectedOption = null;
                    this.answered = false;
                    this.score = 0;
                    this.quizFinished = false;
                    this.userAnswers = {};
                }
            }
        }
    </script>
</body>
</html>
```eof

I have generated the interactive file containing all **63 questions**, complete with English text and Arabic translations.
