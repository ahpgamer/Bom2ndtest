# Bom2ndtest
<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cyphers Exam Portal | HSC Business Management</title>
    <link href="https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.07);
            --accent: #00d2ff;
            --success: #00e676;
            --error: #ff5252;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Hind Siliguri', sans-serif;
            background: #0f172a;
            color: #f8fafc;
            min-height: 100vh;
            display: flex; justify-content: center; padding: 20px;
        }

        .container {
            width: 100%; max-width: 800px;
            background: var(--glass);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255,255,255,0.1);
            border-radius: 25px; padding: 30px;
            box-shadow: 0 25px 50px rgba(0,0,0,0.5);
        }

        /* Pages */
        .page { display: none; }
        .active { display: block; animation: fadeIn 0.5s ease; }

        /* Timer UI */
        #timer-box {
            position: fixed; top: 20px; right: 20px;
            background: rgba(0,0,0,0.8); border: 2px solid var(--accent);
            padding: 10px 20px; border-radius: 50px;
            color: var(--accent); font-size: 1.5rem; font-weight: bold;
            display: none; z-index: 1000;
        }

        /* Inputs & Buttons */
        .input-group { margin-bottom: 20px; }
        input[type="text"] {
            width: 100%; padding: 15px; border-radius: 12px;
            background: rgba(255,255,255,0.05); border: 1px solid rgba(255,255,255,0.2);
            color: white; font-size: 1rem; outline: none;
        }
        .btn {
            width: 100%; padding: 15px; border: none; border-radius: 12px;
            background: linear-gradient(135deg, #00d2ff, #3a7bd5);
            color: white; font-weight: bold; cursor: pointer; transition: 0.3s;
        }
        .btn:hover { transform: translateY(-3px); box-shadow: 0 10px 20px rgba(0,210,255,0.3); }

        /* Question Cards */
        .q-card {
            background: rgba(255,255,255,0.03); padding: 20px;
            border-radius: 15px; margin-bottom: 20px; border-left: 5px solid var(--accent);
        }
        .board-tag { color: var(--accent); font-size: 0.85rem; font-weight: 600; margin-bottom: 10px; display: block; }
        .option {
            display: block; background: rgba(255,255,255,0.05);
            padding: 12px; margin: 8px 0; border-radius: 10px; cursor: pointer;
        }

        /* Result Dashboard */
        .result-box { text-align: center; }
        .score-num { font-size: 4rem; font-weight: 800; color: var(--accent); }
        .review-item { text-align: left; padding: 15px; margin-top: 15px; border-radius: 10px; }
        .correct { border: 1px solid var(--success); background: rgba(0,230,118,0.1); }
        .wrong { border: 1px solid var(--error); background: rgba(255,82,82,0.1); }
        .exp { font-size: 0.9rem; margin-top: 10px; color: #cbd5e1; font-style: italic; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

    <div id="timer-box">15:00</div>

    <div class="container">
        <div id="page1" class="page active">
            <h1 style="text-align: center; margin-bottom: 10px; color: var(--accent);">Cyphers Coaching</h1>
            <p style="text-align: center; margin-bottom: 30px; opacity: 0.7;">ব্যবসায় সংগঠন ও ব্যবস্থাপনা ২য় পত্র পরীক্ষা</p>
            <div class="input-group">
                <input type="text" id="name" placeholder="আপনার নাম">
            </div>
            <div class="input-group">
                <input type="text" id="college" placeholder="আপনার কলেজের নাম">
            </div>
            <button class="btn" onclick="startExam()">পরীক্ষা শুরু করুন</button>
        </div>

        <div id="page2" class="page">
            <div id="questions-area"></div>
            <button class="btn" onclick="submitExam()">জমা দিন</button>
        </div>

        <div id="page3" class="page result-box">
            <h2 style="color: var(--accent);">আপনার ফলাফল</h2>
            <div class="score-num" id="final-score">0</div>
            <p id="msg"></p>
            <hr style="margin: 30px 0; opacity: 0.2;">
            <div id="review-area"></div>
        </div>
    </div>

    <form id="fs-form" action="https://formspree.io/f/mkoogvkv" method="POST" style="display:none;">
        <input name="Name" id="fs-name">
        <input name="College" id="fs-college">
        <input name="Score" id="fs-score">
    </form>

    <script>
        const questions = [
            { q: "১. ব্যবস্থাপনার প্রথম কাজ কোনটি?", options: ["নিয়ন্ত্রণ", "পরিকল্পনা", "সংগঠন", "নেতৃত্ব"], correct: 1, ref: "ঢাকা বোর্ড ২০১৯", exp: "পরিকল্পনা ব্যবস্থাপনার প্রথম ও মৌলিক কাজ।" },
            { q: "২. হেনরি ফেয়ল প্রদত্ত ব্যবস্থাপনার নীতি কয়টি?", options: ["১০টি", "১৪টি", "১৫টি", "১২টি"], correct: 1, ref: "কুমিল্লা বোর্ড ২০১৭", exp: "হেনরি ফেয়ল ১৪টি মূলনীতি প্রদান করেছেন।" },
            { q: "৩. উচ্চস্তরের ব্যবস্থাপনার প্রধান কাজ কোনটি?", options: ["তদারকি", "নীতি নির্ধারণ", "পণ্য বিক্রয়", "শ্রমিক নিয়োগ"], correct: 1, ref: "রাজশাহী বোর্ড ২০১৯", exp: "নীতি ও লক্ষ্য নির্ধারণ করা উচ্চস্তরের কাজ।" },
            { q: "৪. প্রেষণা দান প্রক্রিয়াটি কার সাথে জড়িত?", options: ["মেশিন", "পণ্য", "মানুষ", "কাঁচামাল"], correct: 2, ref: "সকল বোর্ড ২০১৮", exp: "প্রেষণা মানুষের মনের ওপর কাজ করে তাকে উৎসাহিত করে।" },
            { q: "৫. আদেশের ঐক্য নীতি কী নিশ্চিত করে?", options: ["শৃঙ্খল", "একই ব্যক্তির আদেশ", "বেতন বৃদ্ধি", "কাজের মান"], correct: 1, ref: "যশোর বোর্ড ২০১৯", exp: "একজনের আদেশ একজনই দেবেন—এটিই আদেশের ঐক্য।" },
            { q: "৬. 'দ্বি-উপাদান তত্ত্ব' এর উদ্ভাবক কে?", options: ["হার্জবার্গ", "মাসলো", "টেলর", "ফেয়ল"], correct: 0, ref: "চট্টগ্রাম বোর্ড ২০১৭", exp: "ফ্রেডরিক হার্জবার্গ এই তত্ত্বটি দিয়েছেন।" },
            { q: "৭. বাজেট কী?", options: ["পরিকল্পনার সংখ্যাতাত্ত্বিক প্রকাশ", "একটি দায়", "ব্যয়ের হিসাব", "আয় কমানো"], correct: 0, ref: "দিনাজপুর বোর্ড ২০১৭", exp: "পরিকল্পনাকে সংখ্যায় প্রকাশ করাকে বাজেট বলে।" },
            { q: "৮. নিয়ন্ত্রণের সর্বশেষ ধাপ কোনটি?", options: ["মান নির্ধারণ", "বিচ্যুতি নির্ণয়", "সংশোধনমূলক ব্যবস্থা", "ফলাফল তুলনা"], correct: 2, ref: "সিলেট বোর্ড ২০১৯", exp: "ভুল সংশোধনের ব্যবস্থা নেওয়াই নিয়ন্ত্রণের শেষ ধাপ।" },
            { q: "৯. সুপারভিশন কোন স্তরের ব্যবস্থাপনা?", options: ["উচ্চ", "নিম্ন", "মধ্যম", "শীর্ষ"], correct: 1, ref: "বরিশাল বোর্ড ২০১৭", exp: "মাঠ পর্যায়ে সরাসরি তদারকি করা নিম্ন স্তরের কাজ।" },
            { q: "১০. পদোন্নতি কোন ধরনের প্রেষণা?", options: ["আর্থিক", "অ-আর্থিক", "উভয়ই", "কোনটিই নয়"], correct: 2, ref: "ঢাকা বোর্ড ২০২১", exp: "পদোন্নতিতে মর্যাদা ও বেতন উভয়ই বাড়ে।" },
            { q: "১১. বৈজ্ঞানিক ব্যবস্থাপনার জনক কে?", options: ["ফেয়ল", "টেলর", "মাসলো", "ম্যাকগ্রেগর"], correct: 1, ref: "কুমিল্লা বোর্ড ২০১৮", exp: "এফ ডব্লিউ টেলর বৈজ্ঞানিক ব্যবস্থাপনার জনক।" },
            { q: "১২. কর্মী সংস্থানের প্রথম ধাপ কোনটি?", options: ["নিয়োগ", "প্রশিক্ষণ", "কর্মী সংগ্রহ", "নির্বাচন"], correct: 2, ref: "যশোর বোর্ড ২০১৭", exp: "সম্ভাব্য প্রার্থীদের আবেদন করতে উৎসাহিত করা হলো কর্মী সংগ্রহ।" },
            { q: "১৩. সরলরৈখিক সংগঠনে কর্তৃত্বের প্রবাহ কেমন?", options: ["নিচ থেকে উপরে", "আড়াআড়ি", "উপর থেকে নিচে", "বক্ররেখা"], correct: 2, ref: "রাজশাহী বোর্ড ২০১৭", exp: "কর্তৃত্ব সরাসরি উপর থেকে নিচে প্রবাহিত হয়।" },
            { q: "১৪. কোনটি অ-আর্থিক প্রেষণা?", options: ["বোনাস", "বেতন বৃদ্ধি", "কাজের প্রশংসা", "মুনাফা"], correct: 2, ref: "চট্টগ্রাম বোর্ড ২০১৯", exp: "প্রশংসা বা স্বীকৃতি সরাসরি অর্থ নয়।" },
            { q: "১৫. মাসলোর চাহিদা সোপানের দ্বিতীয় স্তর কোনটি?", options: ["জৈবিক", "নিরাপত্তা", "সামাজিক", "আত্মতৃপ্তি"], correct: 1, ref: "সকল বোর্ড ২০১৭", exp: "জৈবিকের পরেই মানুষ নিরাপত্তার চাহিদা অনুভব করে।" },
            { q: "১৬. সমন্বয় সাধনের মূল উদ্দেশ্য কী?", options: ["কাজে ঐক্য আনা", "শ্রমিক ছাঁটাই", "ব্যয় বৃদ্ধি", "বেতন কমানো"], correct: 0, ref: "দিনাজপুর বোর্ড ২০১৯", exp: "বিভিন্ন বিভাগের কাজের মধ্যে মিল আনাই সমন্বয়।" },
            { q: "১৭. ডগলাস ম্যাকগ্রেগর কোন তত্ত্বের প্রবর্তক?", options: ["X ও Y তত্ত্ব", "Z তত্ত্ব", "সোপান তত্ত্ব", "দ্বি-উপাদান"], correct: 0, ref: "ঢাকা বোর্ড ২০১৭", exp: "তিনি মানুষের মানসিকতা নিয়ে X ও Y তত্ত্ব দিয়েছেন।" },
            { q: "১৮. নিচের কোনটি একার্থক পরিকল্পনা?", options: ["নীতি", "বাজেট", "পদ্ধতি", "কৌশল"], correct: 1, ref: "সিলেট বোর্ড ২০১৭", exp: "বাজেট সাধারণত একটি নির্দিষ্ট সময়ের জন্য একবারই তৈরি হয়।" },
            { q: "১৯. নিয়ন্ত্রণের ভিত্তি কোনটি?", options: ["সংগঠন", "পরিকল্পনা", "কর্মী সংস্থান", "নেতৃত্ব"], correct: 1, ref: "রাজশাহী বোর্ড ২০২১", exp: "পরিকল্পনা ছাড়া নিয়ন্ত্রণ সম্ভব নয়।" },
            { q: "২০. টেলিকনফারেন্সিং কোন ধরনের যোগাযোগ?", options: ["লিখিত", "মৌখিক", "আড়াআড়ি", "কোনটিই নয়"], correct: 1, ref: "সকল বোর্ড ২০১৭", exp: "ফোনে কথা বলা একটি মৌখিক যোগাযোগ।" },
            { q: "২১. ব্যবস্থাপনার কোন কাজকে 'শক্তির আধার' বলা হয়?", options: ["সংগঠন", "প্রেষণা", "পরিকল্পনা", "কর্মী নির্বাচন"], correct: 1, ref: "কুমিল্লা বোর্ড ২০১৯", exp: "প্রেষণা কর্মীদের কাজের গতি বা শক্তি যোগায়।" },
            { q: "২২. পদের বিপরীতে উপযুক্ত ব্যক্তি খোঁজা কী?", options: ["কর্মী সংগ্রহ", "নির্বাচন", "নিয়োগ", "প্রশিক্ষণ"], correct: 1, ref: "বরিশাল বোর্ড ২০১৯", exp: "অনেক প্রার্থীর মধ্যে যোগ্য একজনকে বেছে নেওয়াই নির্বাচন।" },
            { q: "২৩. 'ম্যাট্রিক্স সংগঠন' বেশি দেখা যায় কোথায়?", options: ["ব্যাংকে", "প্রজেক্ট ম্যানেজমেন্টে", "ছোট দোকানে", "এক মালিকানায়"], correct: 1, ref: "ঢাকা বোর্ড ২০১৮", exp: "জটিল ও বড় প্রজেক্টে এই দ্বৈত কর্তৃত্বের সংগঠন ব্যবহৃত হয়।" },
            { q: "২৪. হেনরি ফেয়ল পেশায় কী ছিলেন?", options: ["খনি প্রকৌশলী", "ডাক্তার", "শিক্ষক", "আইনজীবী"], correct: 0, ref: "যশোর বোর্ড ২০১৮", exp: "ফেয়ল ছিলেন একজন ফরাসি খনি প্রকৌশলী।" },
            { q: "২৫. প্রশিক্ষণ কর্মীদের কী বৃদ্ধি করে?", options: ["দক্ষতা", "উচ্চতা", "বয়স", "দায়িত্ব"], correct: 0, ref: "রাজশাহী বোর্ড ২০২১", exp: "প্রশিক্ষণের মাধ্যমে কর্মীদের কাজের দক্ষতা বাড়ে।" }
        ];

        let timeLeft = 900;
        let timerId;

        function startExam() {
            const name = document.getElementById('name').value;
            const college = document.getElementById('college').value;
            if(!name || !college) return alert("দয়া করে নাম ও কলেজের নাম লিখুন।");

            document.getElementById('page1').classList.remove('active');
            document.getElementById('page2').classList.add('active');
            document.getElementById('timer-box').style.display = 'block';

            const container = document.getElementById('questions-area');
            questions.forEach((q, i) => {
                container.innerHTML += `
                    <div class="q-card">
                        <span class="board-tag">${q.ref}</span>
                        <p><strong>${q.q}</strong></p>
                        ${q.options.map((opt, idx) => `
                            <label class="option">
                                <input type="radio" name="q${i}" value="${idx}"> ${opt}
                            </label>
                        `).join('')}
                    </div>
                `;
            });

            timerId = setInterval(() => {
                if(timeLeft <= 0) {
                    clearInterval(timerId);
                    submitExam();
                } else {
                    timeLeft--;
                    let m = Math.floor(timeLeft / 60);
                    let s = timeLeft % 60;
                    document.getElementById('timer-box').innerText = `${m}:${s < 10 ? '0' : ''}${s}`;
                }
            }, 1000);
        }

        function submitExam() {
            clearInterval(timerId);
            document.getElementById('timer-box').style.display = 'none';
            let score = 0;
            const reviewArea = document.getElementById('review-area');

            questions.forEach((q, i) => {
                const selected = document.querySelector(`input[name="q${i}"]:checked`);
                const isCorrect = selected && parseInt(selected.value) === q.correct;
                if(isCorrect) score++;

                reviewArea.innerHTML += `
                    <div class="review-item ${isCorrect ? 'correct' : 'wrong'}">
                        <p><strong>${q.q}</strong></p>
                        <p>আপনার উত্তর: ${selected ? q.options[selected.value] : 'দেননি'} | সঠিক: ${q.options[q.correct]}</p>
                        <p class="exp">ব্যাখ্যা: ${q.exp}</p>
                    </div>
                `;
            });

            document.getElementById('page2').classList.remove('active');
            document.getElementById('page3').classList.add('active');
            document.getElementById('final-score').innerText = score + "/25";
            document.getElementById('msg').innerText = score > 15 ? "চমৎকার!" : "আরও পড়ুন!";

            // Formspree Submit
            document.getElementById('fs-name').value = document.getElementById('name').value;
            document.getElementById('fs-college').value = document.getElementById('college').value;
            document.getElementById('fs-score').value = score;
            fetch(document.getElementById('fs-form').action, {
                method: 'POST',
                body: new FormData(document.getElementById('fs-form')),
                headers: { 'Accept': 'application/json' }
            });
        }
    </script>
</body>
</html>
