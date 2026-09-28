<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Math & Dash - 中1速算＆判断力トレーニング</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      touch-action: manipulation;
      user-select: none;
      -webkit-user-select: none;
    }
    @keyframes pulse-fast {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.3; }
    }
    .animate-pulse-fast {
      animation: pulse-fast 0.6s infinite;
    }
  </style>
</head>
<body class="bg-slate-900 text-white min-h-screen flex flex-col items-center justify-between p-2 overflow-hidden select-none">

  <!-- ヘッダー -->
  <header id="header-area" class="w-full max-w-md text-center py-1 transition-all duration-300">
    <h1 class="text-xl font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 to-yellow-400">
      ⚡ Math & Dash ⚡
    </h1>
    <p class="text-[10px] text-slate-400">計算判断力 ✕ アクション脳トレ</p>
  </header>

  <!-- メインコンテンツ領域 -->
  <main class="w-full max-w-md flex-1 flex flex-col justify-between items-center relative">

    <!-- 1. スタート・タイトル画面 -->
    <div id="start-screen" class="w-full flex-1 flex flex-col justify-center items-center text-center p-4 bg-slate-800/80 rounded-2xl border border-slate-700 my-2">
      <div class="text-4xl mb-2">🧠⚡</div>
      <h2 class="text-lg font-bold mb-2 text-cyan-300">中1向け脳トレ・スピード計算</h2>
      <p class="text-xs text-slate-300 mb-4 leading-relaxed">
        中央の計算を<span class="text-yellow-300 font-bold">5秒以内</span>に解きつつ、<br>
        下部で<span class="text-emerald-400 font-bold">時計⏱️</span>を拾って時間延長！<br>
        <span class="text-rose-400 font-bold">爆弾💣</span>は避けよう！
      </p>
      
      <button id="start-btn" class="w-full max-w-xs py-3 px-6 bg-gradient-to-r from-cyan-500 to-blue-600 hover:from-cyan-400 hover:to-blue-500 text-white font-extrabold rounded-xl shadow-lg transform active:scale-95 transition-all text-base tracking-wider border-b-4 border-blue-800">
        ゲームスタート！
      </button>

      <!-- TOP10 ランキング表示領域 -->
      <div class="w-full mt-4 text-left bg-slate-900/80 p-3 rounded-lg border border-slate-700 max-h-48 overflow-y-auto">
        <h3 class="text-xs font-bold text-yellow-400 mb-2 flex items-center gap-1">
          <span>🏆</span> ハイスコア ランキング (TOP 10)
        </h3>
        <ol id="ranking-list" class="text-xs space-y-1 text-slate-300 divide-y divide-slate-800">
          <li class="text-center text-slate-500 py-2">記録がまだありません</li>
        </ol>
      </div>
    </div>

    <!-- 2. ゲームプレイ画面 -->
    <div id="game-screen" class="w-full flex-1 flex flex-col justify-between items-center hidden relative">
      
      <!-- ステータスバー -->
      <div class="w-full flex justify-between items-center bg-slate-800/90 px-3 py-1.5 rounded-xl border border-slate-700">
        <div>
          <span class="text-[10px] text-slate-400">SCORE</span>
          <div id="score-display" class="text-lg font-black text-yellow-400 leading-none">0</div>
        </div>
        
        <div class="text-center">
          <span class="text-[10px] text-slate-400">残り時間</span>
          <div id="timer-display" class="text-xl font-black text-cyan-300 leading-none">30.0s</div>
        </div>

        <button id="pause-btn" class="p-1.5 bg-slate-700 hover:bg-slate-600 rounded-lg text-xs font-bold border border-slate-600 active:scale-95">
          ⏸️
        </button>
      </div>

      <!-- 計算問題エリア -->
      <div class="w-full bg-slate-800 rounded-xl p-2.5 border-2 border-cyan-500/50 shadow-lg my-1 flex flex-col items-center">
        <div class="w-full bg-slate-900 h-2 rounded-full overflow-hidden mb-1.5 border border-slate-700">
          <div id="q-timer-bar" class="bg-yellow-400 h-full w-full transition-all duration-75"></div>
        </div>

        <div id="question-text" class="text-xl font-black text-white tracking-widest my-0.5 min-h-[30px] flex items-center justify-center">
          -
        </div>

        <div id="options-container" class="grid grid-cols-2 gap-1.5 w-full mt-1">
        </div>
      </div>

      <!-- アクションキャンバスエリア（縦長＋最下段バー固定） -->
      <div class="w-full flex-1 relative bg-slate-950 rounded-xl overflow-hidden border border-slate-800 min-h-[280px] max-h-[400px] my-1">
        <canvas id="action-canvas" class="w-full h-full block touch-none"></canvas>
      </div>

    </div>

    <!-- 3. 一時停止（ポーズ）オーバーレイ -->
    <div id="pause-modal" class="fixed inset-0 bg-black/80 flex flex-col justify-center items-center z-50 hidden">
      <div class="bg-slate-800 p-6 rounded-2xl border border-slate-600 text-center max-w-xs w-full">
        <h2 class="text-xl font-bold mb-4 text-cyan-300">PAUSE (一時停止中)</h2>
        <div class="space-y-3">
          <button id="resume-btn" class="w-full py-2.5 bg-emerald-600 hover:bg-emerald-500 text-white font-bold rounded-lg active:scale-95">
            ゲームを再開
          </button>
          <button id="quit-btn" class="w-full py-2.5 bg-rose-600 hover:bg-rose-500 text-white font-bold rounded-lg active:scale-95">
            タイトルへ戻る
          </button>
        </div>
      </div>
    </div>

    <!-- 4. リザルト画面 -->
    <div id="result-screen" class="w-full flex-1 flex flex-col justify-center items-center text-center p-4 bg-slate-800/95 rounded-2xl border border-slate-700 hidden">
      <h2 class="text-2xl font-black text-rose-400 mb-1">GAME OVER</h2>
      <p id="new-record-badge" class="hidden text-xs bg-yellow-400 text-slate-900 font-extrabold px-3 py-1 rounded-full mb-2 animate-bounce">
        🎉 自己ベスト更新！
      </p>

      <div class="bg-slate-900/80 p-4 rounded-xl border border-slate-700 w-full mb-4 space-y-2">
        <div>
          <span class="text-xs text-slate-400">最終スコア</span>
          <div id="final-score" class="text-3xl font-black text-yellow-300">0</div>
        </div>
        <div class="flex justify-around text-xs border-t border-slate-800 pt-2 text-slate-300">
          <div>正解数: <span id="stat-correct" class="font-bold text-emerald-400">0</span></div>
          <div>誤答数: <span id="stat-wrong" class="font-bold text-rose-400">0</span></div>
        </div>
      </div>

      <button id="restart-btn" class="w-full max-w-xs py-3 bg-gradient-to-r from-emerald-500 to-teal-600 text-white font-extrabold rounded-xl shadow-lg active:scale-95 transition-all text-base mb-2">
        もう一度挑戦！
      </button>
    </div>

  </main>

  <script>
    const AudioCtx = window.AudioContext || window.webkitAudioContext;
    let audioCtx = null;

    function initAudio() {
      if (!audioCtx) audioCtx = new AudioCtx();
    }

    function playSound(type) {
      if (!audioCtx) return;
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.connect(gain);
      gain.connect(audioCtx.destination);
      const now = audioCtx.currentTime;

      if (type === 'correct') {
        osc.frequency.setValueAtTime(523.25, now);
        osc.frequency.setValueAtTime(659.25, now + 0.08);
        gain.gain.setValueAtTime(0.2, now);
        gain.gain.exponentialRampToValueAtTime(0.01, now + 0.25);
        osc.start(now);
        osc.stop(now + 0.25);
      } else if (type === 'wrong') {
        osc.type = 'sawtooth';
        osc.frequency.setValueAtTime(180, now);
        osc.frequency.setValueAtTime(130, now + 0.1);
        gain.gain.setValueAtTime(0.2, now);
        gain.gain.exponentialRampToValueAtTime(0.01, now + 0.3);
        osc.start(now);
        osc.stop(now + 0.3);
      } else if (type === 'item') {
        osc.type = 'triangle';
        osc.frequency.setValueAtTime(880, now);
        osc.frequency.setValueAtTime(1760, now + 0.08);
        gain.gain.setValueAtTime(0.2, now);
        gain.gain.exponentialRampToValueAtTime(0.01, now + 0.2);
        osc.start(now);
        osc.stop(now + 0.2);
      } else if (type === 'bomb') {
        osc.type = 'square';
        osc.frequency.setValueAtTime(120, now);
        osc.frequency.exponentialRampToValueAtTime(40, now + 0.3);
        gain.gain.setValueAtTime(0.3, now);
        gain.gain.exponentialRampToValueAtTime(0.01, now + 0.3);
        osc.start(now);
        osc.stop(now + 0.3);
      }
    }

    let gameState = 'START';
    let score = 0;
    let mainTimer = 30.0;
    let correctCount = 0;
    let wrongCount = 0;
    let currentAnswer = 0;
    let qTimer = 5.0;
    const Q_TIME_LIMIT = 5.0;

    let lastTime = 0;
    let isProcessingAnswer = false;

    const headerArea = document.getElementById('header-area');
    const startScreen = document.getElementById('start-screen');
    const gameScreen = document.getElementById('game-screen');
    const resultScreen = document.getElementById('result-screen');
    const pauseModal = document.getElementById('pause-modal');

    const scoreDisplay = document.getElementById('score-display');
    const timerDisplay = document.getElementById('timer-display');
    const questionText = document.getElementById('question-text');
    const optionsContainer = document.getElementById('options-container');
    const qTimerBar = document.getElementById('q-timer-bar');

    function generateQuestion() {
      isProcessingAnswer = false;
      qTimer = Q_TIME_LIMIT;

      const types = ['add_neg', 'sub_neg', 'mul_neg', 'div_neg'];
      const type = types[Math.floor(Math.random() * types.length)];
      
      let a, b, qStr, ans;

      if (type === 'add_neg') {
        a = Math.floor(Math.random() * 20) - 10;
        b = Math.floor(Math.random() * 20) - 10;
        qStr = `${a < 0 ? `(${a})` : a} + ${b < 0 ? `(${b})` : b}`;
        ans = a + b;
      } else if (type === 'sub_neg') {
        a = Math.floor(Math.random() * 20) - 10;
        b = Math.floor(Math.random() * 20) - 10;
        qStr = `${a < 0 ? `(${a})` : a} - ${b < 0 ? `(${b})` : b}`;
        ans = a - b;
      } else if (type === 'mul_neg') {
        a = Math.floor(Math.random() * 14) - 7;
        b = Math.floor(Math.random() * 14) - 7;
        if (a === 0) a = 2;
        if (b === 0) b = -3;
        qStr = `${a < 0 ? `(${a})` : a} × ${b < 0 ? `(${b})` : b}`;
        ans = a * b;
      } else {
        ans = Math.floor(Math.random() * 12) - 6;
        if (ans === 0) ans = 3;
        b = Math.floor(Math.random() * 8) + 1;
        if (Math.random() < 0.5) b = -b;
        a = ans * b;
        qStr = `${a < 0 ? `(${a})` : a} ÷ ${b < 0 ? `(${b})` : b}`;
      }

      currentAnswer = ans;
      questionText.textContent = `${qStr} = ?`;

      const options = new Set([ans]);
      while (options.size < 4) {
        const dummy = ans + (Math.floor(Math.random() * 7) - 3) * (Math.random() < 0.5 ? 1 : -1);
        if (dummy !== ans) options.add(dummy);
      }

      const shuffled = Array.from(options).sort(() => Math.random() - 0.5);
      optionsContainer.innerHTML = '';
      
      shuffled.forEach(val => {
        const btn = document.createElement('button');
        btn.className = "py-2 bg-slate-700 hover:bg-slate-600 active:bg-cyan-600 text-white font-bold text-base rounded-xl border border-slate-600 shadow transition-all active:scale-95";
        btn.textContent = val;
        
        btn.addEventListener('pointerdown', (e) => {
          e.preventDefault();
          checkAnswer(val, btn);
        });
        optionsContainer.appendChild(btn);
      });
    }

    function checkAnswer(selected, btnEl) {
      if (isProcessingAnswer || gameState !== 'PLAYING') return;
      isProcessingAnswer = true;

      if (selected === currentAnswer) {
        playSound('correct');
        score += 100;
        mainTimer += 1.5;
        correctCount++;
        btnEl.classList.add('bg-emerald-500');
      } else {
        playSound('wrong');
        mainTimer = Math.max(0, mainTimer - 2.5);
        wrongCount++;
        btnEl.classList.add('bg-rose-600');
      }

      scoreDisplay.textContent = score;
      setTimeout(generateQuestion, 150);
    }

    function handleQuestionTimeout() {
      if (isProcessingAnswer || gameState !== 'PLAYING') return;
      isProcessingAnswer = true;
      playSound('wrong');
      mainTimer = Math.max(0, mainTimer - 2.5);
      wrongCount++;
      generateQuestion();
    }

    // --- キャッチアクション（Canvas制御） ---
    const canvas = document.getElementById('action-canvas');
    const ctx = canvas.getContext('2d');

    let player = { x: 0, y: 0, width: 50, height: 14 };
    let items = [];
    let itemSpawnTimer = 0;

    function resizeCanvas() {
      canvas.width = canvas.clientWidth;
      canvas.height = canvas.clientHeight;
      
      // ★バーのY座標を常にキャンバス最下段（底面から18px上）に確定固定
      player.y = canvas.height - 18;
      if (player.x === 0) player.x = canvas.width / 2 - player.width / 2;
    }
    window.addEventListener('resize', resizeCanvas);

    function handleMove(clientX) {
      const rect = canvas.getBoundingClientRect();
      const touchX = clientX - rect.left;
      player.x = Math.max(0, Math.min(canvas.width - player.width, touchX - player.width / 2));
    }

    canvas.addEventListener('touchmove', (e) => {
      if (e.touches.length > 0) handleMove(e.touches[0].clientX);
    });
    canvas.addEventListener('mousemove', (e) => {
      if (e.buttons === 1) handleMove(e.clientX);
    });

    function updateAction(dt) {
      itemSpawnTimer += dt;
      if (itemSpawnTimer > 0.7) {
        itemSpawnTimer = 0;
        const rand = Math.random();
        let type = 'bomb';
        if (rand < 0.225) type = 'clock';
        else if (rand < 0.30) type = 'star';

        items.push({
          x: Math.random() * (canvas.width - 24),
          y: -24,
          type: type,
          speed: 130 + Math.random() * 70
        });
      }

      for (let i = items.length - 1; i >= 0; i--) {
        const it = items[i];
        it.y += it.speed * dt;

        // キャッチ判定（最下段のバーとの当たり判定）
        if (
          it.y + 20 >= player.y &&
          it.y <= player.y + player.height &&
          it.x + 20 >= player.x &&
          it.x <= player.x + player.width
        ) {
          if (it.type === 'clock') {
            playSound('item');
            mainTimer += 3.0;
          } else if (it.type === 'star') {
            playSound('item');
            score += 300;
            scoreDisplay.textContent = score;
          } else if (it.type === 'bomb') {
            playSound('bomb');
            mainTimer = Math.max(0, mainTimer - 5.0);
          }
          items.splice(i, 1);
          continue;
        }

        if (it.y > canvas.height) {
          items.splice(i, 1);
        }
      }
    }

    function drawAction() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // ★常に最下段に配置されたバーを描画
      ctx.fillStyle = '#38bdf8';
      ctx.beginPath();
      ctx.roundRect(player.x, player.y, player.width, player.height, 6);
      ctx.fill();

      // アイテム描画
      ctx.font = '20px sans-serif';
      items.forEach(it => {
        let icon = '💣';
        if (it.type === 'clock') icon = '⏱️';
        if (it.type === 'star') icon = '⭐';
        ctx.fillText(icon, it.x, it.y + 18);
      });
    }

    function gameLoop(timestamp) {
      if (!lastTime) lastTime = timestamp;
      const dt = (timestamp - lastTime) / 1000;
      lastTime = timestamp;

      if (gameState === 'PLAYING') {
        mainTimer -= dt;
        timerDisplay.textContent = `${Math.max(0, mainTimer).toFixed(1)}s`;

        qTimer -= dt;
        const barPercent = Math.max(0, (qTimer / Q_TIME_LIMIT) * 100);
        qTimerBar.style.width = `${barPercent}%`;
        if (qTimer <= 0) {
          handleQuestionTimeout();
        }

        updateAction(dt);
        drawAction();

        if (mainTimer <= 0) {
          endGame();
          return;
        }
      }

      if (gameState === 'PLAYING' || gameState === 'PAUSED') {
        requestAnimationFrame(gameLoop);
      }
    }

    function startGame() {
      initAudio();
      gameState = 'PLAYING';
      score = 0;
      mainTimer = 30.0;
      correctCount = 0;
      wrongCount = 0;
      items = [];

      headerArea.classList.add('hidden');
      startScreen.classList.add('hidden');
      resultScreen.classList.add('hidden');
      gameScreen.classList.remove('hidden');

      scoreDisplay.textContent = '0';
      
      // ゲーム開始時にキャンバス描画サイズとバーの位置（最下段）を再計算
      setTimeout(resizeCanvas, 50);
      generateQuestion();

      lastTime = performance.now();
      requestAnimationFrame(gameLoop);
    }

    function endGame() {
      gameState = 'GAMEOVER';
      playSound('wrong');

      const isNewRank = saveScoreToRanking(score);

      gameScreen.classList.add('hidden');
      headerArea.classList.remove('hidden');
      resultScreen.classList.remove('hidden');

      document.getElementById('final-score').textContent = score;
      document.getElementById('stat-correct').textContent = correctCount;
      document.getElementById('stat-wrong').textContent = wrongCount;

      const badge = document.getElementById('new-record-badge');
      if (isNewRank) badge.classList.remove('hidden');
      else badge.classList.add('hidden');

      loadRankingList();
    }

    function getRanking() {
      try {
        return JSON.parse(localStorage.getItem('math_dash_ranking')) || [];
      } catch (e) {
        return [];
      }
    }

    function saveScoreToRanking(newScore) {
      if (newScore <= 0) return false;
      let ranking = getRanking();
      const dateStr = new Date().toLocaleDateString('ja-JP', { month: 'numeric', day: 'numeric', hour: '2-digit', minute: '2-digit' });
      
      ranking.push({ score: newScore, date: dateStr });
      ranking.sort((a, b) => b.score - a.score);
      ranking = ranking.slice(0, 10);

      localStorage.setItem('math_dash_ranking', JSON.stringify(ranking));
      return ranking.some(item => item.score === newScore && item.date === dateStr);
    }

    function loadRankingList() {
      const ranking = getRanking();
      const listEl = document.getElementById('ranking-list');
      listEl.innerHTML = '';

      if (ranking.length === 0) {
        listEl.innerHTML = '<li class="text-center text-slate-500 py-2">記録がまだありません</li>';
        return;
      }

      ranking.forEach((item, index) => {
        const li = document.createElement('li');
        li.className = "flex justify-between items-center py-1 px-1";
        
        let medal = `<span class="w-5 text-slate-400 font-bold">${index + 1}.</span>`;
        if (index === 0) medal = `<span class="w-5">🥇</span>`;
        if (index === 1) medal = `<span class="w-5">🥈</span>`;
        if (index === 2) medal = `<span class="w-5">🥉</span>`;

        li.innerHTML = `
          <div class="flex items-center gap-1">
            ${medal}
            <span class="font-bold text-yellow-300">${item.score} pt</span>
          </div>
          <span class="text-[10px] text-slate-500">${item.date}</span>
        `;
        listEl.appendChild(li);
      });
    }

    document.getElementById('start-btn').addEventListener('click', startGame);
    document.getElementById('restart-btn').addEventListener('click', startGame);

    document.getElementById('pause-btn').addEventListener('click', () => {
      if (gameState === 'PLAYING') {
        gameState = 'PAUSED';
        pauseModal.classList.remove('hidden');
      }
    });

    document.getElementById('resume-btn').addEventListener('click', () => {
      if (gameState === 'PAUSED') {
        gameState = 'PLAYING';
        pauseModal.classList.add('hidden');
        lastTime = performance.now();
        requestAnimationFrame(gameLoop);
      }
    });

    document.getElementById('quit-btn').addEventListener('click', () => {
      gameState = 'START';
      pauseModal.classList.add('hidden');
      gameScreen.classList.add('hidden');
      headerArea.classList.remove('hidden');
      startScreen.classList.remove('hidden');
      loadRankingList();
    });

    loadRankingList();
  </script>
</body>
</html>
