<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Academic Assessment Portal</title>
    <style>
        :root {
            --bg: #f5f5f4; 
            --card: #ffffff;
            --primary: #262626; 
            --accent-nude: #e7e5e4; 
            --hover-nude: #d6d3d1; 
            --error: #991b1b;
            --success: #166534;
        }

        body { 
            font-family: 'Inter', system-ui, sans-serif; 
            background: var(--bg); 
            margin: 0; 
            display: flex; flex-direction: column; align-items: center; min-height: 100vh;
            user-select: none; -webkit-user-select: none; 
        }

        .portal-title {
            margin: 40px 0 20px 0; font-size: 2.5rem; font-weight: 800; color: var(--primary);
            text-transform: uppercase; letter-spacing: 2px; text-shadow: 2px 4px 8px rgba(0,0,0,0.15); text-align: center;
        }

        #app-container { width: 100%; max-width: 550px; padding: 1rem; }
        .card { background: var(--card); padding: 2.5rem; border-radius: 20px; box-shadow: 0 20px 25px -5px rgba(0,0,0,0.05); }

        #camera-container { 
            width: 100%; background: #1c1917; border-radius: 12px; margin-bottom: 1.5rem; 
            height: 180px; display: flex; align-items: center; justify-content: center; color: #a8a29e;
        }
        video { width: 100%; height: 100%; object-fit: cover; }

        .subject-list { display: flex; flex-direction: column; gap: 12px; }
        .subject-btn { 
            padding: 1.25rem; background: var(--accent-nude); color: var(--primary); border: none; 
            border-radius: 12px; cursor: pointer; text-align: left; font-weight: 700;
            display: flex; justify-content: space-between; transition: 0.3s;
        }
        .subject-btn:hover:not(:disabled) { background: var(--hover-nude); transform: translateX(8px); box-shadow: -4px 0 0 var(--primary); }
        .subject-btn:disabled { opacity: 0.5; cursor: not-allowed; filter: grayscale(1); }

        .timer-row { display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem; }
        .timer { font-weight: 800; color: var(--error); font-size: 1.4rem; }
        .violation-count { font-size: 0.9rem; color: var(--error); font-weight: 600; }

        .option-btn { 
            display: block; width: 100%; padding: 1rem; margin: 10px 0; 
            border: 2px solid var(--accent-nude); border-radius: 10px; cursor: pointer; background: white; text-align: left; 
        }
        .option-btn:hover { background: var(--accent-nude); }

        #security-mask { 
            position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
            background: black; z-index: 99999; display: none; color: white; 
            flex-direction: column; justify-content: center; align-items: center; text-align: center;
        }
        .hidden { display: none; }
    </style>
</head>
<body>

<div id="security-mask">
    <h1>🛑 SECURITY VIOLATION</h1>
    <p>Unauthorized screen activity detected.<br>Exam will terminate if violations reach 3.</p>
</div>

<h1 class="portal-title">Academic Assessment</h1>

<div id="app-container">
    <div id="home-page" class="card">
        <p style="text-align: center; color: #78716c; margin-bottom: 1.5rem;">Camera verification required for entry.</p>
        <div id="camera-container">
            <video id="video" autoplay playsinline></video>
            <div id="cam-placeholder">Enabling Camera...</div>
        </div>
        <div class="subject-list">
            <button class="subject-btn" id="btn-DBMS" onclick="startExam('DBMS')" disabled>DBMS <span>→</span></button>
            <button class="subject-btn" id="btn-WT" onclick="startExam('WT')" disabled>Web Technologies <span>→</span></button>
            <button class="subject-btn" id="btn-MATH" onclick="startExam('MATH')" disabled>Mathematics <span>→</span></button>
            <button class="subject-btn" id="btn-COA" onclick="startExam('COA')" disabled>COA <span>→</span></button>
            <button class="subject-btn" id="btn-CN" onclick="startExam('CN')" disabled>Computer Networks <span>→</span></button>
        </div>
    </div>

    <div id="exam-page" class="card hidden">
        <div class="timer-row">
            <div class="violation-count">Violations: <span id="v-count">0</span>/3</div>
            <div class="timer">⏳ <span id="time-left">05:00</span></div>
        </div>
        <div class="question-box">
            <h3 id="question-text"></h3>
            <div id="options-container"></div>
        </div>
    </div>

    <div id="result-page" class="card hidden">
        <h2 id="res-title"></h2>
        <p id="res-msg"></p>
        <div style="background: var(--bg); padding: 20px; border-radius: 12px; margin: 20px 0;">
            <p style="margin:0;">Score Achieved</p>
            <h1 style="margin:0; font-size: 3rem;"><span id="res-score"></span> <small style="font-size: 1rem;">/ 10</small></h1>
        </div>
        <button class="subject-btn" onclick="location.reload()" style="justify-content: center;">Close Portal</button>
<button onclick="localStorage.clear(); location.reload();" 
        style="margin-top: 20px; background: none; border: 1px dashed #78716c; color: #78716c; cursor: pointer; padding: 5px 10px; border-radius: 5px; font-size: 0.8rem;">
    Reset All Locks (Testing Only)
</button>
    </div>
</div>

<script>
    const quizData = {
        'DBMS': [{q:"Primary Key must be?", a:["Unique & Not Null","Optional","Allows Duplicate"], c:0},{q:"ACID property 'I' stands for?", a:["Iteration","Isolation","Integer"], c:1},{q:"A row in a table is?", a:["Attribute","Tuple","Cell"], c:1},{q:"Drop table is a?", a:["DDL","DML","DCL"], c:0},{q:"SQL is?", a:["Language","Database","Server"], c:0}],
        'WT': [{q:"HTTPS port?", a:["80","443","21"], c:1},{q:"JS is?", a:["Markup","Scripting","Style"], c:1},{q:"React is by?", a:["Google","Meta","Microsoft"], c:1},{q:"Tag for link?", a:["<a>","<link>","<url>"], c:0},{q:"HTML is?", a:["Markup","Programming","Database"], c:0}],
        'MATH': [{q:"log 1?", a:["0","1","10"], c:0},{q:"5! is?", a:["60","120","24"], c:1},{q:"Pi is?", a:["3.14","2.17","1.41"], c:0},{q:"2^5?", a:["16","32","64"], c:1},{q:"Root 64?", a:["8","6","4"], c:0}],
        'COA': [{q:"Volatile memory?", a:["ROM","RAM","SSD"], c:1},{q:"1 Byte?", a:["4 bits","8 bits","16 bits"], c:1},{q:"Brain of PC?", a:["ALU","CPU","RAM"], c:1},{q:"Non-volatile?", a:["Cache","ROM","RAM"], c:1},{q:"Smallest unit?", a:["Bit","Byte","Word"], c:0}],
        'CN': [{q:"IP Layer?", a:["Network","Transport","Data"], c:0},{q:"DNS is?", a:["Name System","Network Sys","Data Sys"], c:0},{q:"Router is?", a:["Layer 1","Layer 2","Layer 3"], c:2},{q:"HTTP is?", a:["App Layer","Net Layer","Phys Layer"], c:0},{q:"MAC bits?", a:["32","48","64"], c:1}]
    };

    let stream, timerInterval, currentSubject, score, qIdx, violations = 0;
    let isExamActive = false;

    async function initCamera() {
        try {
            stream = await navigator.mediaDevices.getUserMedia({ video: true });
            document.getElementById('video').srcObject = stream;
            document.getElementById('cam-placeholder').style.display = 'none';
            Object.keys(quizData).forEach(id => checkLockout(id));
            document.querySelectorAll('.subject-btn').forEach(b => { if(!b.dataset.locked) b.disabled = false; });
        } catch (err) { alert("Camera Required. Verification failed."); }
    }

    function checkLockout(subId) {
        const lockTime = localStorage.getItem('lock_' + subId);
        if (lockTime) {
            const hoursLeft = (parseInt(lockTime) + (24*60*60*1000) - new Date().getTime()) / (1000*60*60);
            if (hoursLeft > 0) {
                const btn = document.getElementById('btn-' + subId);
                if(btn) { btn.disabled = true; btn.dataset.locked = "true"; btn.innerHTML = `${subId} (Locked: ${Math.ceil(hoursLeft)}h)`; }
            } else { localStorage.removeItem('lock_' + subId); }
        }
    }

    function startExam(sub) {
        currentSubject = sub; score = 0; qIdx = 0; violations = 0; isExamActive = true;
        document.getElementById('home-page').classList.add('hidden');
        document.getElementById('exam-page').classList.remove('hidden');
        startTimer(); loadQuestion();
    }

    function startTimer() {
        let time = 300; // 5 minutes in seconds
        timerInterval = setInterval(() => {
            time--;
            let mins = Math.floor(time / 60);
            let secs = time % 60;
            document.getElementById('time-left').innerText = `${mins < 10 ? '0' : ''}${mins}:${secs < 10 ? '0' : ''}${secs}`;
            if (time <= 0) terminateExam("Time Expired");
        }, 1000);
    }

    function loadQuestion() {
        const questions = quizData[currentSubject];
        const q = questions[qIdx];
        document.getElementById('question-text').innerText = `${qIdx + 1}. ${q.q}`;
        const container = document.getElementById('options-container');
        container.innerHTML = '';
        q.a.forEach((opt, i) => {
            const btn = document.createElement('button');
            btn.className = 'option-btn'; btn.innerText = opt;
            btn.onclick = () => { if(i === q.c) score += 2; qIdx++; if(qIdx < questions.length) loadQuestion(); else terminateExam(); };
            container.appendChild(btn);
        });
    }

    function terminateExam(reason) {
        isExamActive = false; clearInterval(timerInterval);
        if(stream) stream.getTracks().forEach(t => t.stop());
        document.getElementById('exam-page').classList.add('hidden');
        document.getElementById('result-page').classList.remove('hidden');
        document.getElementById('res-score').innerText = score;

        if (reason === "Violation" || score < 5) {
            document.getElementById('res-title').innerText = "FAILED / TERMINATED";
            document.getElementById('res-title').style.color = "var(--error)";
            document.getElementById('res-msg').innerText = reason === "Violation" ? "Security breach limit reached. Locked for 24h." : "Score too low. Locked for 24h.";
            localStorage.setItem('lock_' + currentSubject, new Date().getTime());
        } else {
            document.getElementById('res-title').innerText = "PASSED";
            document.getElementById('res-title').style.color = "var(--success)";
            document.getElementById('res-msg').innerText = "Assessment successful.";
        }
    }

    // Security Monitoring
    setInterval(() => {
        if (!document.hasFocus() && isExamActive) {
            document.getElementById('security-mask').style.display = 'flex';
        } else {
            document.getElementById('security-mask').style.display = 'none';
        }
    }, 100);

    window.addEventListener('blur', () => {
        if(isExamActive) {
            violations++;
            document.getElementById('v-count').innerText = violations;
            if(violations >= 3) terminateExam("Violation");
        }
    });

    document.addEventListener('keydown', e => {
        if (e.key === 'PrintScreen' || (e.ctrlKey && (e.key === 'p' || e.key === 's' || e.key === 'u'))) {
            e.preventDefault();
            if(isExamActive) {
                violations++;
                document.getElementById('v-count').innerText = violations;
                if(violations >= 3) terminateExam("Violation");
            }
        }
    });

    document.addEventListener('contextmenu', e => e.preventDefault());
    initCamera();
</script>
</body>
</html>
