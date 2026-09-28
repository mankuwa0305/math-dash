[制限時間付き計算ゲームindex.html](https://github.com/user-attachments/files/32749425/index.html)
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Math & Dash - 中1脳トレ決定版</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&family=Noto+Sans+JP:wght@500;700;900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Fredoka', 'Noto Sans JP', sans-serif;
            touch-action: manipulation;
            user-select: none;
            -webkit-user-select: none;
        }
        
        .arcade-btn {
            transition: transform 0.05s ease, box-shadow 0.05s ease;
            box-shadow: 0 6px 0 rgba(0, 0, 0, 0.25);
        }
        .arcade-btn:active {
            transform: translateY(4px);
            box-shadow: 0 2px 0 rgba(0, 0, 0, 0.25);
        }

        .pulse-timer {
            animation: pulse-red 0.5s infinite alternate;
        }

        @keyframes pulse-red {
            from { color: #ef4444; transform: scale(1); }
            to { color: #dc2626; transform: scale(1.08); }
        }

        /* Glassmorphism overlays */
        .glass-panel {
            background: rgba(255, 255, 255, 0.92);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
        }

        /* Custom Scrollbar for Ranking */
        .custom-scroll::-webkit-scrollbar {
            width: 6px;
        }
        .custom-scroll::-webkit-scrollbar-track {
            background: rgba(0, 0, 0, 0.05);
            border-radius: 4px;
        }
        .custom-scroll::-webkit-scrollbar-thumb {
            background: rgba(99, 102, 241, 0.5);
            border-radius: 4px;
        }

        canvas {
            touch-action: none;
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col items-center justify-center p-2 sm:p-4 overflow-hidden select-none">

    <!-- Main Game Container -->
    <div class="relative w-full max-w-md bg-slate-800 border-4 border-slate-700 rounded-3xl shadow-2xl overflow-hidden flex flex-col h-[92vh] max-h-[850px]">
        
        <!-- Header Bar: Stats -->
        <div class="bg-slate-900/90 border-b border-slate-700 p-3 flex justify-between items-center z-10">
            <div>
                <div class="text-xs text-slate-400 font-bold tracking-wider">SCORE</div>
                <div id="scoreDisplay" class="text-2xl font-black text-amber-400">0</div>
            </div>
            
            <div class="text-center">
                <div class="text-xs text-slate-400 font-bold tracking-wider">TIME</div>
                <div id="timeDisplay" class="text-3xl font-black text-emerald-400 font-mono">30.0</div>
            </div>

            <div class="text-right">
                <div class="text-xs text-slate-400 font-bold tracking-wider">TOP SCORE</div>
                <div id="highScoreDisplay" class="text-xl font-extrabold text-cyan-400">0</div>
            </div>
        </div>

        <!-- Middle Section: Math Question Board -->
        <div class="bg-gradient-to-b from-indigo-950 to-slate-900 p-4 border-b border-slate-700 flex flex-col justify-center items-center shrink-0 min-h-[190px] relative z-10 shadow-lg">
            
            <!-- 問題制限時間バー (5秒カウントダウン) -->
            <div class="w-full bg-slate-800 h-2.5 rounded-full mb-2 overflow-hidden border border-slate-700/80">
                <div id="questionProgressBar" class="bg-amber-400 h-full w-full transition-all duration-75 ease-linear"></div>
            </div>

            <div class="flex justify-between items-center w-full mb-1">
                <div class="text-xs font-bold text-indigo-300 uppercase tracking-widest bg-indigo-900/60 px-3 py-0.5 rounded-full border border-indigo-500/30">
                    脳トレ計算問題
                </div>
                <div id="questionTimerText" class="text-xs font-black text-amber-400 font-mono bg-slate-900/80 px-2 py-0.5 rounded-md border border-amber-500/30">
                    5.0s
                </div>
            </div>

            <div id="questionDisplay" class="text-3xl sm:text-4xl font-black text-white my-1 tracking-wide drop-shadow-md">
                READY?
            </div>
            
            <!-- Answer Buttons -->
            <div id="answersContainer" class="grid grid-cols-3 gap-2 sm:gap-3 w-full mt-1">
                <button class="answer-btn arcade-btn bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 text-white font-black text-xl sm:text-2xl py-2.5 rounded-2xl border-2 border-indigo-400 disabled:opacity-50 touch-none select-none" disabled>-</button>
                <button class="answer-btn arcade-btn bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 text-white font-black text-xl sm:text-2xl py-2.5 rounded-2xl border-2 border-indigo-400 disabled:opacity-50 touch-none select-none" disabled>-</button>
                <button class="answer-btn arcade-btn bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 text-white font-black text-xl sm:text-2xl py-2.5 rounded-2xl border-2 border-indigo-400 disabled:opacity-50 touch-none select-none" disabled>-</button>
            </div>
        </div>

        <!-- Bottom Section: HTML5 Canvas Action Mini-Game -->
        <div class="relative flex-grow bg-slate-950 overflow-hidden">
            <canvas id="gameCanvas" class="w-full h-full block"></canvas>
            
            <!-- On-Screen Controls Hint -->
            <div class="absolute bottom-2 left-0 right-0 text-center pointer-events-none opacity-40 text-xs text-slate-300">
                ← 指スライド または 画面左右タップで移動 →
            </div>
        </div>

        <!-- START OVERLAY -->
        <div id="startOverlay" class="absolute inset-0 z-30 glass-panel flex flex-col items-center justify-center p-6 text-slate-900 text-center">
            <div class="w-16 h-16 bg-indigo-600 rounded-2xl flex items-center justify-center text-3xl mb-3 shadow-lg transform -rotate-6 text-white">
                ⚡
            </div>
            <h1 class="text-3xl sm:text-4xl font-black text-slate-900 mb-2 tracking-tight">Math & Dash</h1>
            <p class="text-slate-600 text-sm font-semibold mb-6 max-w-xs leading-relaxed">
                <span class="text-indigo-600 font-bold">【中1向け判断力トレーニング】</span><br>
                計算を解きながら、画面下の操作で時計(+3秒)を拾い、爆弾(-5秒)を避けよう！
            </p>
            
            <button id="startBtn" class="arcade-btn bg-emerald-500 hover:bg-emerald-400 text-white font-black text-2xl py-4 px-10 rounded-2xl border-b-4 border-emerald-700 w-full max-w-xs transition-all transform hover:scale-105 cursor-pointer">
                ゲームスタート！
            </button>
        </div>

        <!-- GAME OVER OVERLAY WITH RANKING -->
        <div id="gameOverOverlay" class="absolute inset-0 z-30 glass-panel flex flex-col items-center justify-between p-4 sm:p-6 text-slate-900 text-center hidden overflow-y-auto">
            <div class="w-full flex flex-col items-center my-auto">
                
                <div id="newRecordBadge" class="hidden bg-amber-400 text-amber-950 font-black text-xs px-4 py-1 rounded-full mb-1 animate-bounce uppercase tracking-wider shadow-md">
                    🎉 TOP 10 ランクイン！ 🎉
                </div>
                
                <h2 class="text-2xl font-black text-slate-900">タイムアップ！</h2>
                
                <!-- Main Stats summary -->
                <div class="bg-white border border-slate-200 rounded-2xl p-3 w-full max-w-xs my-2 shadow-sm flex justify-around items-center">
                    <div>
                        <div class="text-[10px] text-slate-400 font-bold">SCORE</div>
                        <div id="finalScore" class="text-2xl font-black text-indigo-600">0</div>
                    </div>
                    <div class="h-8 w-px bg-slate-200"></div>
                    <div>
                        <div class="text-[10px] text-slate-400 font-bold">正解数</div>
                        <div id="accuracyDisplay" class="text-base font-bold text-slate-800">0/0</div>
                    </div>
                    <div class="h-8 w-px bg-slate-200"></div>
                    <div>
                        <div class="text-[10px] text-slate-400 font-bold">時計</div>
                        <div id="clockCountDisplay" class="text-base font-bold text-emerald-600">0個</div>
                    </div>
                </div>

                <!-- TOP 10 RANKING LIST CONTAINER -->
                <div class="w-full max-w-xs bg-white/80 border border-slate-200 rounded-2xl p-3 my-1 shadow-inner">
                    <div class="flex justify-between items-center mb-2 px-1 border-b pb-1">
                        <span class="text-xs font-extrabold text-slate-700 flex items-center gap-1">
                            🏆 TOP 10 ランキング
                        </span>
                        <span class="text-[10px] font-bold text-slate-400">過去のベスト記録</span>
                    </div>

                    <!-- Ranking List Scrollable -->
                    <div id="rankingList" class="max-h-40 overflow-y-auto custom-scroll space-y-1.5 pr-1">
                        <!-- Populated by JS -->
                    </div>
                </div>

                <button id="restartBtn" class="arcade-btn bg-indigo-600 hover:bg-indigo-500 text-white font-black text-xl py-3 px-8 rounded-2xl border-b-4 border-indigo-800 w-full max-w-xs cursor-pointer mt-3">
                    もう一度挑戦！
                </button>
            </div>
        </div>

    </div>

    <script>
        class SoundFX {
            constructor() {
                this.ctx = null;
            }

            init() {
                if (!this.ctx) {
                    this.ctx = new (window.AudioContext || window.webkitAudioContext)();
                }
                if (this.ctx.state === 'suspended') {
                    this.ctx.resume();
                }
            }

            playCorrect() {
                if (!this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(523.25, this.ctx.currentTime); // C5
                    osc.frequency.exponentialRampToValueAtTime(1046.50, this.ctx.currentTime + 0.15); // C6
                    gain.gain.setValueAtTime(0.2, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, this.ctx.currentTime + 0.15);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start();
                    osc.stop(this.ctx.currentTime + 0.15);
                } catch(e){}
            }

            playWrong() {
                if (!this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(180, this.ctx.currentTime);
                    osc.frequency.linearRampToValueAtTime(110, this.ctx.currentTime + 0.2);
                    gain.gain.setValueAtTime(0.2, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, this.ctx.currentTime + 0.2);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start();
                    osc.stop(this.ctx.currentTime + 0.2);
                } catch(e){}
            }

            playClock() {
                if (!this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(880, this.ctx.currentTime); // A5
                    osc.frequency.setValueAtTime(1318.51, this.ctx.currentTime + 0.08); // E6
                    gain.gain.setValueAtTime(0.25, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, this.ctx.currentTime + 0.25);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start();
                    osc.stop(this.ctx.currentTime + 0.25);
                } catch(e){}
            }

            playBomb() {
                if (!this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'square';
                    osc.frequency.setValueAtTime(100, this.ctx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(30, this.ctx.currentTime + 0.3);
                    gain.gain.setValueAtTime(0.3, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, this.ctx.currentTime + 0.3);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start();
                    osc.stop(this.ctx.currentTime + 0.3);
                } catch(e){}
            }

            playFanfare() {
                if (!this.ctx) return;
                try {
                    const notes = [523.25, 659.25, 783.99, 1046.50]; // C5, E5, G5, C6
                    notes.forEach((freq, idx) => {
                        const osc = this.ctx.createOscillator();
                        const gain = this.ctx.createGain();
                        osc.type = 'triangle';
                        osc.frequency.setValueAtTime(freq, this.ctx.currentTime + idx * 0.1);
                        gain.gain.setValueAtTime(0.25, this.ctx.currentTime + idx * 0.1);
                        gain.gain.exponentialRampToValueAtTime(0.01, this.ctx.currentTime + idx * 0.1 + 0.25);
                        osc.connect(gain);
                        gain.connect(this.ctx.destination);
                        osc.start(this.ctx.currentTime + idx * 0.1);
                        osc.stop(this.ctx.currentTime + idx * 0.1 + 0.25);
                    });
                } catch(e){}
            }
        }

        const sound = new SoundFX();

        let score = 0;
        let timeLeft = 30.0;
        let isPlaying = false;
        let lastTime = 0;

        // 問題タイマー用の変数
        const QUESTION_TIME_LIMIT = 5.0;
        let questionTimer = QUESTION_TIME_LIMIT;

        let totalQuestions = 0;
        let correctQuestions = 0;
        let clocksCollected = 0;

        let currentProblem = { question: '', answer: 0, options: [] };

        // Ranking System Storage Key
        const RANKING_STORAGE_KEY = 'math_dash_ranking_v1';

        // DOM Elements
        const scoreDisplay = document.getElementById('scoreDisplay');
        const timeDisplay = document.getElementById('timeDisplay');
        const highScoreDisplay = document.getElementById('highScoreDisplay');
        const questionDisplay = document.getElementById('questionDisplay');
        const answerBtns = document.querySelectorAll('.answer-btn');
        const startOverlay = document.getElementById('startOverlay');
        const gameOverOverlay = document.getElementById('gameOverOverlay');
        const startBtn = document.getElementById('startBtn');
        const restartBtn = document.getElementById('restartBtn');
        const finalScore = document.getElementById('finalScore');
        const accuracyDisplay = document.getElementById('accuracyDisplay');
        const clockCountDisplay = document.getElementById('clockCountDisplay');
        const rankingList = document.getElementById('rankingList');
        const questionProgressBar = document.getElementById('questionProgressBar');
        const questionTimerText = document.getElementById('questionTimerText');

        // Canvas Setup
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        function resizeCanvas() {
            const rect = canvas.parentElement.getBoundingClientRect();
            canvas.width = rect.width;
            canvas.height = rect.height;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Load Top Score for Header
        function getRankings() {
            try {
                return JSON.parse(localStorage.getItem(RANKING_STORAGE_KEY)) || [];
            } catch (e) {
                return [];
            }
        }

        function updateTopScoreHeader() {
            const rankings = getRankings();
            const topScore = rankings.length > 0 ? rankings[0].score : 0;
            highScoreDisplay.textContent = topScore;
        }
        updateTopScoreHeader();

        function generateMathQuestion() {
            // 問題生成時にタイマーを5秒にリセット
            questionTimer = QUESTION_TIME_LIMIT;

            let availableTypes = ['add_neg', 'sub_neg'];
            if (score >= 100) availableTypes.push('mul_neg');
            if (score >= 200) availableTypes.push('div_neg');
            if (score >= 350) availableTypes.push('linear_comb');

            const type = availableTypes[Math.floor(Math.random() * availableTypes.length)];
            let qText = '';
            let ans = 0;

            const randInt = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min;
            const randNonZero = (min, max) => {
                let v = 0;
                while (v === 0) v = randInt(min, max);
                return v;
            };

            switch (type) {
                case 'add_neg': {
                    const a = randInt(-12, 12);
                    const b = randInt(-12, 12);
                    qText = `${a >= 0 ? a : `(${a})`} + ${b >= 0 ? b : `(${b})`}`;
                    ans = a + b;
                    break;
                }
                case 'sub_neg': {
                    const a = randInt(-10, 15);
                    const b = randInt(-12, 12);
                    qText = `${a >= 0 ? a : `(${a})`} - ${b >= 0 ? b : `(${b})`}`;
                    ans = a - b;
                    break;
                }
                case 'mul_neg': {
                    const a = randNonZero(-8, 9);
                    const b = randNonZero(-8, 9);
                    qText = `${a >= 0 ? a : `(${a})`} × ${b >= 0 ? b : `(${b})`}`;
                    ans = a * b;
                    break;
                }
                case 'div_neg': {
                    const ansVal = randNonZero(-9, 9);
                    const b = randNonZero(-6, 6);
                    const a = ansVal * b;
                    qText = `${a >= 0 ? a : `(${a})`} ÷ ${b >= 0 ? b : `(${b})`}`;
                    ans = ansVal;
                    break;
                }
                case 'linear_comb': {
                    const x = randInt(-5, 8);
                    const k = randNonZero(-4, 5);
                    const c = randInt(-10, 10);
                    qText = `${k}x ${c >= 0 ? '+ ' + c : '- ' + Math.abs(c)} (x = ${x})`;
                    ans = k * x + c;
                    break;
                }
            }

            const options = [ans];
            while (options.length < 3) {
                let offset = randInt(-5, 5);
                if (offset === 0) offset = 2;
                let wrong = ans + offset;
                if (Math.random() < 0.3) wrong = -ans;
                if (!options.includes(wrong)) {
                    options.push(wrong);
                }
            }

            options.sort(() => Math.random() - 0.5);

            currentProblem = { question: qText, answer: ans, options: options };

            questionDisplay.textContent = currentProblem.question + ' = ?';
            answerBtns.forEach((btn, idx) => {
                btn.textContent = currentProblem.options[idx];
                btn.disabled = false;
                btn.className = "answer-btn arcade-btn bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 text-white font-black text-xl sm:text-2xl py-2.5 rounded-2xl border-2 border-indigo-400 cursor-pointer touch-none select-none";
            });
        }

        let lastAnswerTime = 0;

        function handleAnswer(selectedIndex) {
            // 重複判定・高速連打防止デバウンス（100ms以内は1回のみ処理）
            const now = Date.now();
            if (now - lastAnswerTime < 100) return;
            lastAnswerTime = now;

            if (!isPlaying) return;

            totalQuestions++;
            const selectedVal = currentProblem.options[selectedIndex];

            if (selectedVal === currentProblem.answer) {
                sound.playCorrect();
                score += 50;
                timeLeft += 1.5;
                correctQuestions++;
                showFloatingText(canvas.width / 2, 40, "+50pt / +1.5s!", "#34d399");
            } else {
                sound.playWrong();
                timeLeft = Math.max(0, timeLeft - 2.5);
                showFloatingText(canvas.width / 2, 40, "-2.5s!", "#f87171");
            }

            scoreDisplay.textContent = score;
            generateMathQuestion();
        }

        answerBtns.forEach((btn, idx) => {
            // pointerdownで指が触れた瞬間に即時発火（遅延・無効化を防止）
            btn.addEventListener('pointerdown', (e) => {
                if (!isPlaying || btn.disabled) return;
                e.preventDefault();
                handleAnswer(idx);
            });

            // クリックのフォールバック
            btn.addEventListener('click', (e) => {
                if (!isPlaying || btn.disabled) return;
                e.preventDefault();
                handleAnswer(idx);
            });
        });

        const player = {
            x: 150,
            y: 0,
            width: 70,
            height: 18,
            speed: 8
        };

        let items = [];

        class FallingItem {
            constructor() {
                this.radius = 16;
                this.x = Math.random() * (canvas.width - this.radius * 2) + this.radius;
                this.y = -this.radius;
                
                const baseSpeed = 2.2 + Math.min(score / 150, 2.5);
                this.speedY = baseSpeed + Math.random() * 0.8;

                const rand = Math.random();
                if (rand < 0.225) { // 22.5% Clock (+3s)
                    this.type = 'clock';
                    this.color = '#10b981';
                    this.icon = '⏱️';
                } else if (rand < 0.925) { // 70% Bomb (-5s)
                    this.type = 'bomb';
                    this.color = '#ef4444';
                    this.icon = '💣';
                } else { // 7.5% Star (+100pt)
                    this.type = 'star';
                    this.color = '#f59e0b';
                    this.icon = '⭐';
                }
            }

            update() {
                this.y += this.speedY;
            }

            draw() {
                ctx.save();
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                ctx.fillStyle = this.color;
                ctx.shadowColor = this.color;
                ctx.shadowBlur = 8;
                ctx.fill();

                ctx.font = '14px serif';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(this.icon, this.x, this.y + 1);
                ctx.restore();
            }
        }

        let floatingTexts = [];
        function showFloatingText(x, y, text, color) {
            floatingTexts.push({
                x: x,
                y: y,
                text: text,
                color: color,
                alpha: 1.0,
                life: 0.8
            });
        }

        let keys = { left: false, right: false };

        window.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') keys.left = true;
            if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') keys.right = true;
        });

        window.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') keys.left = false;
            if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') keys.right = false;
        });

        let isTouching = false;
        canvas.addEventListener('touchstart', (e) => {
            isTouching = true;
            const rect = canvas.getBoundingClientRect();
            const touchX = e.touches[0].clientX - rect.left;
            player.x = touchX;
        }, { passive: true });

        canvas.addEventListener('touchmove', (e) => {
            if (isTouching) {
                const rect = canvas.getBoundingClientRect();
                const touchX = e.touches[0].clientX - rect.left;
                player.x = touchX;
            }
        }, { passive: true });

        canvas.addEventListener('touchend', () => { isTouching = false; });

        let spawnTimer = 0;

        function updateGame(dt) {
            timeLeft -= dt;
            if (timeLeft <= 0) {
                timeLeft = 0;
                endGame();
                return;
            }

            // 問題タイマーの減算・時間切れ処理
            questionTimer -= dt;
            if (questionTimer <= 0) {
                sound.playWrong();
                totalQuestions++;
                timeLeft = Math.max(0, timeLeft - 2.5);
                showFloatingText(canvas.width / 2, 40, "-2.5s 時間切れ!", "#f87171");
                generateMathQuestion();
            }

            // 問題タイマーUI（バー＆数値）のリアルタイム更新
            if (questionProgressBar && questionTimerText) {
                const pct = Math.max(0, (questionTimer / QUESTION_TIME_LIMIT) * 100);
                questionProgressBar.style.width = pct + '%';
                questionTimerText.textContent = Math.max(0, questionTimer).toFixed(1) + 's';
                
                if (questionTimer <= 1.5) {
                    questionProgressBar.className = "bg-red-500 h-full w-full transition-all duration-75 ease-linear";
                    questionTimerText.className = "text-xs font-black text-red-400 font-mono bg-slate-900/80 px-2 py-0.5 rounded-md border border-red-500/50 animate-pulse";
                } else {
                    questionProgressBar.className = "bg-amber-400 h-full w-full transition-all duration-75 ease-linear";
                    questionTimerText.className = "text-xs font-black text-amber-400 font-mono bg-slate-900/80 px-2 py-0.5 rounded-md border border-amber-500/30";
                }
            }

            timeDisplay.textContent = timeLeft.toFixed(1);
            if (timeLeft <= 5.0) {
                timeDisplay.classList.add('pulse-timer');
            } else {
                timeDisplay.classList.remove('pulse-timer');
            }

            if (keys.left) player.x -= player.speed;
            if (keys.right) player.x += player.speed;

            const halfW = player.width / 2;
            if (player.x - halfW < 0) player.x = halfW;
            if (player.x + halfW > canvas.width) player.x = canvas.width - halfW;

            player.y = canvas.height - 25;

            spawnTimer += dt;
            const spawnInterval = Math.max(0.6, 1.4 - (score / 400));
            if (spawnTimer >= spawnInterval) {
                spawnTimer = 0;
                items.push(new FallingItem());
            }

            for (let i = items.length - 1; i >= 0; i--) {
                const item = items[i];
                item.update();

                const closestX = Math.max(player.x - halfW, Math.min(item.x, player.x + halfW));
                const closestY = Math.max(player.y - player.height / 2, Math.min(item.y, player.y + player.height / 2));
                const distX = item.x - closestX;
                const distY = item.y - closestY;
                const distanceSq = (distX * distX) + (distY * distY);

                if (distanceSq < (item.radius * item.radius)) {
                    if (item.type === 'clock') {
                        sound.playClock();
                        timeLeft += 3.0;
                        clocksCollected++;
                        showFloatingText(item.x, item.y, "+3s 秒延長!", "#34d399");
                    } else if (item.type === 'bomb') {
                        sound.playBomb();
                        timeLeft = Math.max(0, timeLeft - 5.0);
                        showFloatingText(item.x, item.y, "-5s 爆発!", "#f87171");
                    } else if (item.type === 'star') {
                        sound.playCorrect();
                        score += 100;
                        scoreDisplay.textContent = score;
                        showFloatingText(item.x, item.y, "+100pt", "#fbbf24");
                    }

                    items.splice(i, 1);
                    continue;
                }

                if (item.y - item.radius > canvas.height) {
                    items.splice(i, 1);
                }
            }

            for (let i = floatingTexts.length - 1; i >= 0; i--) {
                const ft = floatingTexts[i];
                ft.y -= 1.2;
                ft.life -= dt;
                ft.alpha = Math.max(0, ft.life / 0.8);
                if (ft.life <= 0) floatingTexts.splice(i, 1);
            }
        }

        function renderGame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Subtle Grid
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.04)';
            ctx.lineWidth = 1;
            const gridSize = 30;
            for (let x = 0; x < canvas.width; x += gridSize) {
                ctx.beginPath();
                ctx.moveTo(x, 0);
                ctx.lineTo(x, canvas.height);
                ctx.stroke();
            }

            items.forEach(item => item.draw());

            // Player Basket
            const halfW = player.width / 2;
            const halfH = player.height / 2;

            ctx.save();
            ctx.shadowColor = '#6366f1';
            ctx.shadowBlur = 12;

            const grad = ctx.createLinearGradient(player.x - halfW, player.y, player.x + halfW, player.y);
            grad.addColorStop(0, '#818cf8');
            grad.addColorStop(1, '#4f46e5');

            ctx.fillStyle = grad;
            ctx.beginPath();
            ctx.roundRect(player.x - halfW, player.y - halfH, player.width, player.height, 8);
            ctx.fill();

            ctx.fillStyle = '#c7d2fe';
            ctx.fillRect(player.x - halfW + 4, player.y - halfH + 3, player.width - 8, 3);
            ctx.restore();

            floatingTexts.forEach(ft => {
                ctx.save();
                ctx.font = 'bold 16px Noto Sans JP';
                ctx.fillStyle = ft.color;
                ctx.globalAlpha = ft.alpha;
                ctx.textAlign = 'center';
                ctx.fillText(ft.text, ft.x, ft.y);
                ctx.restore();
            });
        }

        function gameLoop(timestamp) {
            if (!isPlaying) return;

            if (!lastTime) lastTime = timestamp;
            const dt = (timestamp - lastTime) / 1000;
            lastTime = timestamp;

            updateGame(dt);
            renderGame();

            if (isPlaying) {
                requestAnimationFrame(gameLoop);
            }
        }

        function startGame() {
            resizeCanvas();
            sound.init();
            
            score = 0;
            timeLeft = 30.0;
            totalQuestions = 0;
            correctQuestions = 0;
            clocksCollected = 0;
            items = [];
            floatingTexts = [];
            player.x = canvas.width / 2;
            
            scoreDisplay.textContent = '0';
            timeDisplay.textContent = '30.0';

            startOverlay.classList.add('hidden');
            gameOverOverlay.classList.add('hidden');

            isPlaying = true;
            lastTime = 0;

            generateMathQuestion();
            requestAnimationFrame(gameLoop);
        }

        function saveAndGetRanking(newScore) {
            let rankings = getRankings();
            
            const now = new Date();
            const dateStr = `${now.getMonth() + 1}/${now.getDate()} ${now.getHours()}:${String(now.getMinutes()).padStart(2, '0')}`;

            let newRankIndex = -1;

            if (newScore > 0) {
                const newEntry = { score: newScore, date: dateStr, isNew: true };
                
                // Reset previous "isNew" flags
                rankings.forEach(r => delete r.isNew);
                
                rankings.push(newEntry);
                rankings.sort((a, b) => b.score - a.score);
                
                // Keep Top 10
                rankings = rankings.slice(0, 10);
                
                // Find index of current play
                newRankIndex = rankings.findIndex(r => r === newEntry);

                try {
                    localStorage.setItem(RANKING_STORAGE_KEY, JSON.stringify(rankings));
                } catch(e){}
            }

            return { rankings, newRankIndex };
        }

        function renderRankingUI(rankings, currentRankIndex) {
            rankingList.innerHTML = '';

            if (rankings.length === 0) {
                rankingList.innerHTML = `<div class="text-xs text-slate-400 text-center py-4">まだ記録がありません</div>`;
                return;
            }

            rankings.forEach((entry, idx) => {
                const row = document.createElement('div');
                const isCurrent = idx === currentRankIndex;

                let medal = `<span class="w-5 text-center font-bold text-slate-500">${idx + 1}</span>`;
                if (idx === 0) medal = `<span class="w-5 text-center text-sm">🥇</span>`;
                else if (idx === 1) medal = `<span class="w-5 text-center text-sm">🥈</span>`;
                else if (idx === 2) medal = `<span class="w-5 text-center text-sm">🥉</span>`;

                row.className = `flex justify-between items-center text-xs py-1.5 px-2 rounded-xl transition-all ${
                    isCurrent 
                        ? 'bg-amber-100 border-2 border-amber-400 font-extrabold text-amber-950 scale-[1.02] shadow-sm' 
                        : 'bg-slate-50/80 text-slate-700 border border-slate-100'
                }`;

                row.innerHTML = `
                    <div class="flex items-center gap-1.5">
                        ${medal}
                        <span class="font-mono text-slate-400 text-[10px]">${entry.date || ''}</span>
                    </div>
                    <div class="font-mono font-black ${isCurrent ? 'text-amber-900 text-sm' : 'text-slate-800'}">
                        ${entry.score} pt
                        ${isCurrent ? '<span class="text-[9px] bg-amber-500 text-white px-1.5 py-0.2 rounded-full ml-1">YOU</span>' : ''}
                    </div>
                `;

                rankingList.appendChild(row);
            });
        }

        function endGame() {
            isPlaying = false;

            const newRecordBadge = document.getElementById('newRecordBadge');
            
            // Process Ranking
            const { rankings, newRankIndex } = saveAndGetRanking(score);
            renderRankingUI(rankings, newRankIndex);
            updateTopScoreHeader();

            if (newRankIndex !== -1) {
                newRecordBadge.textContent = `🎉 TOP 10 ランクイン！ (#${newRankIndex + 1}) 🎉`;
                newRecordBadge.classList.remove('hidden');
                sound.playFanfare();
            } else {
                newRecordBadge.classList.add('hidden');
            }

            finalScore.textContent = score;
            accuracyDisplay.textContent = `${correctQuestions}/${totalQuestions}`;
            clockCountDisplay.textContent = `${clocksCollected}個`;

            gameOverOverlay.classList.remove('hidden');
        }

        startBtn.addEventListener('click', startGame);
        restartBtn.addEventListener('click', startGame);

    </script>
</body>
</html>
