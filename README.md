# sansuu1nen-imananji
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>とけいれんしゅう</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    @keyframes pop {
      0% { transform: scale(0.9); opacity: 0; }
      100% { transform: scale(1); opacity: 1; }
    }
    .pop-anim { animation: pop 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards; }
  </style>
</head>
<body class="bg-emerald-50 min-h-screen flex items-center justify-center p-4 text-slate-800">

  <!-- Main Container -->
  <div class="w-full max-w-3xl bg-white rounded-3xl shadow-lg border-2 border-emerald-300 p-5 sm:p-6 flex flex-col gap-4 relative overflow-hidden">

    <!-- Header Bar -->
    <header class="flex items-center justify-between border-b border-emerald-100 pb-3">
      <div class="flex items-center gap-2.5">
        <span class="text-3xl">⏰</span>
        <div>
          <h1 class="text-lg sm:text-xl font-bold text-emerald-700 leading-none">いま なんじ？</h1>
          <p class="text-xs sm:text-sm text-slate-400 font-bold mt-1">しょうがっこう1ねんせい さんすう</p>
        </div>
      </div>

      <!-- Score & Badges -->
      <div class="flex items-center gap-3 bg-emerald-50 px-3.5 py-2 rounded-2xl border border-emerald-200">
        <div class="text-xs sm:text-sm font-extrabold text-emerald-800 border-r border-emerald-200 pr-2.5">
          <span id="q-counter">だい 1 / 10 もん</span>
        </div>
        <div class="flex items-center gap-1.5 text-base font-bold text-slate-700">
          <span class="text-yellow-500 text-lg">⭐</span>
          <span id="score-text" class="text-emerald-800">0てん</span>
        </div>
        <div id="badge-container" class="hidden sm:flex gap-1 items-center border-l border-emerald-200 pl-3">
          <span class="text-base text-slate-300">⭐</span>
          <span class="text-base text-slate-300">⭐</span>
          <span class="text-base text-slate-300">⭐</span>
          <span class="text-base text-slate-300">⭐</span>
          <span class="text-base text-slate-300">⭐</span>
        </div>
      </div>
    </header>

    <!-- Top Mode Switcher & Difficulty Bar -->
    <div class="flex flex-col md:flex-row items-stretch md:items-center justify-between gap-3 text-xs sm:text-sm">
      <!-- Mode Switcher Tabs -->
      <div class="flex bg-slate-100 p-1 rounded-2xl border border-slate-200">
        <button id="tab-quiz" onclick="switchMode('quiz')" class="flex-1 sm:flex-none px-4 py-2 rounded-xl font-bold transition-all bg-emerald-600 text-white shadow-sm text-xs sm:text-sm">
          ❓ なんじかな？
        </button>
        <button id="tab-set" onclick="switchMode('set')" class="flex-1 sm:flex-none px-4 py-2 rounded-xl font-bold transition-all text-slate-600 hover:text-emerald-700 text-xs sm:text-sm">
          🎯 とけいをあわせよう
        </button>
      </div>

      <!-- Difficulty & Question Count Settings -->
      <div class="flex flex-wrap items-center justify-between sm:justify-start gap-2">
        <!-- Difficulty Buttons -->
        <div class="flex items-center gap-1.5 bg-amber-50 px-3 py-1.5 rounded-2xl border border-amber-200">
          <span class="text-xs font-bold text-amber-900 whitespace-nowrap">⚙️ むずかしさ:</span>
          <div class="flex gap-1.5">
            <button id="diff-easy" onclick="setDifficulty('easy')" class="px-3 py-1 text-xs rounded-xl font-bold bg-emerald-600 text-white shadow-sm border border-emerald-600 transition-all">
              〇じ
            </button>
            <button id="diff-normal" onclick="setDifficulty('normal')" class="px-3 py-1 text-xs rounded-xl font-bold bg-white text-slate-600 hover:bg-slate-100 border border-slate-300 transition-all">
              〇じはん
            </button>
          </div>
        </div>

        <!-- Question Count Selector -->
        <div class="flex items-center gap-1.5 bg-sky-50 px-3 py-1.5 rounded-2xl border border-sky-200">
          <span class="text-xs font-bold text-sky-900 whitespace-nowrap">📝 もんだいすう:</span>
          <div class="flex gap-1.5">
            <button id="count-10" onclick="setQuestionLimit(10)" class="px-3 py-1 text-xs rounded-xl font-bold bg-sky-600 text-white shadow-sm border border-sky-600 transition-all">
              10もん
            </button>
            <button id="count-20" onclick="setQuestionLimit(20)" class="px-3 py-1 text-xs rounded-xl font-bold bg-white text-slate-600 hover:bg-slate-100 border border-slate-300 transition-all">
              20もん
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Main Content Area: Left Clock + Right Panel -->
    <div class="grid grid-cols-1 sm:grid-cols-12 gap-5 items-center my-1">

      <!-- Left Column: SVG Clock -->
      <div class="sm:col-span-5 flex flex-col items-center justify-center bg-emerald-50/60 p-4 rounded-2xl border border-emerald-100">
        
        <!-- Target Prompt for Set Mode -->
        <div id="target-prompt" class="hidden text-center mb-2.5 bg-indigo-100 px-3 py-1.5 rounded-lg border border-indigo-200 w-full">
          <span class="text-xs text-indigo-600 font-bold block">もんだい：</span>
          <span id="target-time-text" class="text-xl font-extrabold text-indigo-700 block leading-tight">3じ</span>
        </div>

        <!-- Clock SVG -->
        <div class="relative w-56 h-56 sm:w-64 sm:h-64">
          <svg viewBox="0 0 200 200" class="w-full h-full drop-shadow-sm">
            <circle cx="100" cy="100" r="95" fill="#ffffff" stroke="#10b981" stroke-width="7"/>
            <circle cx="100" cy="100" r="88" fill="none" stroke="#a7f3d0" stroke-width="2"/>

            <g stroke="#059669" stroke-width="3">
              <line x1="100" y1="12" x2="100" y2="20"/>
              <line x1="188" y1="100" x2="180" y2="100"/>
              <line x1="100" y1="188" x2="100" y2="180"/>
              <line x1="12" y1="100" x2="20" y2="100"/>
            </g>

            <g font-size="16" font-weight="bold" fill="#1e293b" text-anchor="middle" dominant-baseline="central">
              <text x="100" y="28">12</text>
              <text x="136" y="38">1</text>
              <text x="162" y="64">2</text>
              <text x="172" y="100">3</text>
              <text x="162" y="136">4</text>
              <text x="136" y="162">5</text>
              <text x="100" y="172">6</text>
              <text x="64" y="162">7</text>
              <text x="38" y="136">8</text>
              <text x="28" y="100">9</text>
              <text x="38" y="64">10</text>
              <text x="64" y="38">11</text>
            </g>

            <!-- Hour Hand (Red / Short) -->
            <line id="hour-hand" x1="100" y1="100" x2="100" y2="52" stroke="#ef4444" stroke-width="7" stroke-linecap="round"/>

            <!-- Minute Hand (Blue / Long) -->
            <line id="minute-hand" x1="100" y1="100" x2="100" y2="28" stroke="#3b82f6" stroke-width="5" stroke-linecap="round"/>

            <circle cx="100" cy="100" r="6" fill="#1e293b"/>
            <circle cx="100" cy="100" r="2.5" fill="#ffffff"/>
          </svg>
        </div>

        <!-- SET MODE Controls -->
        <div id="set-controls" class="hidden w-full flex-col gap-2 mt-3">
          <div class="grid grid-cols-2 gap-2 w-full">
            <button onclick="adjustTime(1, 0)" class="py-2 px-2.5 bg-amber-100 hover:bg-amber-200 border border-amber-300 text-amber-900 rounded-xl font-bold text-xs sm:text-sm active:scale-95 transition-all">
              ➕ 1じかん
            </button>
            <button onclick="adjustTime(0, 30)" class="py-2 px-2.5 bg-sky-100 hover:bg-sky-200 border border-sky-300 text-sky-900 rounded-xl font-bold text-xs sm:text-sm active:scale-95 transition-all">
              ➕ 30ふん
            </button>
          </div>
          <button onclick="checkSetAnswer()" class="w-full py-2.5 bg-indigo-600 hover:bg-indigo-700 text-white font-bold rounded-xl shadow active:scale-95 transition-all text-xs sm:text-sm border-b-2 border-indigo-900">
            これで OK！ ⭕
          </button>
        </div>
      </div>

      <!-- Right Column: Quiz Choices & Helper Box -->
      <div class="sm:col-span-7 flex flex-col justify-between h-full gap-4">
        
        <!-- QUIZ MODE Choices Grid -->
        <div id="quiz-panel" class="flex flex-col gap-2.5">
          <div class="text-xs sm:text-sm font-bold text-slate-600">こたえを えらんでね：</div>
          <div id="quiz-controls" class="grid grid-cols-2 gap-2.5">
            <!-- Dynamic choice buttons -->
          </div>
        </div>

        <!-- Educational Hint Box -->
        <div class="bg-amber-50 rounded-2xl p-3 border border-amber-200 text-slate-700 text-xs sm:text-sm leading-relaxed">
          <div class="font-bold text-amber-800 mb-1 flex items-center gap-1.5">
            💡 とけいの ひみつ
          </div>
          <div class="space-y-1">
            <p><span class="text-red-500 font-bold">● あかいはり</span>：じかん（じ）</p>
            <p><span class="text-blue-500 font-bold">● あおいはり</span>：「12」で<b>〇じ</b> / 「6」で<b>〇じはん</b></p>
          </div>
        </div>

      </div>

    </div>

    <!-- Overlay Feedback Modal Banner -->
    <div id="feedback-banner" class="hidden absolute inset-0 bg-white/95 flex flex-col items-center justify-center p-4 text-center pop-anim z-20">
      <div id="feedback-icon" class="text-4xl mb-2">🎉</div>
      <h2 id="feedback-title" class="text-xl font-bold text-emerald-600 mb-1">せいかい！</h2>
      <p id="feedback-msg" class="text-slate-600 text-sm font-bold mb-4">すごーい！そのちょうし！</p>
      <button id="next-btn" onclick="nextQuestion()" class="py-2 px-6 bg-emerald-500 text-white font-bold rounded-xl shadow hover:bg-emerald-600 border-b-2 border-emerald-700 active:translate-y-0.5 text-xs sm:text-sm">
        つぎへ すすむ ➡️
      </button>
    </div>

    <!-- Final Result Modal Banner -->
    <div id="result-banner" class="hidden absolute inset-0 bg-white/95 flex flex-col items-center justify-center p-6 text-center pop-anim z-30">
      <div class="text-5xl mb-2">💮</div>
      <h2 class="text-2xl font-bold text-emerald-700 mb-1">おつかれさま！</h2>
      <p id="result-score-text" class="text-lg font-bold text-slate-800 mb-2">10もんちゅう 10もん せいかい！</p>
      <p id="result-comment" class="text-slate-600 text-sm font-bold mb-6">パーフェクト！ とけいマスターだね！</p>
      <button onclick="restartGame()" class="py-2.5 px-6 bg-emerald-500 hover:bg-emerald-600 text-white font-bold rounded-xl shadow-lg border-b-2 border-emerald-700 active:translate-y-0.5 text-sm sm:text-base">
        🔄 もういちど チャレンジ！
      </button>
    </div>

  </div>

  <script>
    // Audio Synth Context
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    
    function playSound(type) {
      if (audioCtx.state === 'suspended') audioCtx.resume();
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.connect(gain);
      gain.connect(audioCtx.destination);

      if (type === 'correct') {
        osc.type = 'sine';
        osc.frequency.setValueAtTime(523.25, audioCtx.currentTime);
        osc.frequency.setValueAtTime(659.25, audioCtx.currentTime + 0.08);
        osc.frequency.setValueAtTime(783.99, audioCtx.currentTime + 0.16);
        gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.35);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.35);
      } else if (type === 'wrong') {
        osc.type = 'triangle';
        osc.frequency.setValueAtTime(220, audioCtx.currentTime);
        osc.frequency.setValueAtTime(196, audioCtx.currentTime + 0.12);
        gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.25);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.25);
      } else if (type === 'fanfare') {
        const now = audioCtx.currentTime;
        [523.25, 659.25, 783.99, 1046.50].forEach((freq, idx) => {
          const o = audioCtx.createOscillator();
          const g = audioCtx.createGain();
          o.connect(g);
          g.connect(audioCtx.destination);
          o.frequency.setValueAtTime(freq, now + idx * 0.1);
          g.gain.setValueAtTime(0.2, now + idx * 0.1);
          g.gain.exponentialRampToValueAtTime(0.01, now + idx * 0.1 + 0.3);
          o.start(now + idx * 0.1);
          o.stop(now + idx * 0.1 + 0.3);
        });
      }
    }

    // App State
    let currentMode = 'quiz';
    let difficulty = 'easy';
    let questionLimit = 10;
    let currentQuestion = 1;
    let score = 0;

    let targetHour = 3;
    let targetMinute = 0;

    let userSetHour = 12;
    let userSetMinute = 0;

    function formatTimeText(h, m) {
      const displayHour = h === 0 ? 12 : h;
      return m === 0 ? `${displayHour}じ` : `${displayHour}じはん`;
    }

    function renderClock(h, m) {
      const minAngle = m * 6;
      const hourAngle = ((h % 12) + m / 60) * 30;

      const hourHand = document.getElementById('hour-hand');
      const minuteHand = document.getElementById('minute-hand');

      hourHand.setAttribute('transform', `rotate(${hourAngle} 100 100)`);
      minuteHand.setAttribute('transform', `rotate(${minAngle} 100 100)`);
    }

    function generateRandomTime() {
      const hour = Math.floor(Math.random() * 12) + 1;
      let minute = 0;
      if (difficulty === 'normal') {
        minute = Math.random() < 0.5 ? 0 : 30;
      }
      return { hour, minute };
    }

    function loadNewQuestion() {
      document.getElementById('feedback-banner').classList.add('hidden');
      document.getElementById('result-banner').classList.add('hidden');

      document.getElementById('q-counter').innerText = `だい ${currentQuestion} / ${questionLimit} もん`;

      const time = generateRandomTime();
      targetHour = time.hour;
      targetMinute = time.minute;

      if (currentMode === 'quiz') {
        renderClock(targetHour, targetMinute);
        setupQuizChoices();
      } else {
        userSetHour = 12;
        userSetMinute = 0;
        renderClock(userSetHour, userSetMinute);
        document.getElementById('target-time-text').innerText = formatTimeText(targetHour, targetMinute);
      }
    }

    function setupQuizChoices() {
      const container = document.getElementById('quiz-controls');
      container.innerHTML = '';

      const correctAnswerText = formatTimeText(targetHour, targetMinute);
      
      const choices = new Set([correctAnswerText]);
      while (choices.size < 4) {
        const dummyHour = Math.floor(Math.random() * 12) + 1;
        const dummyMin = (difficulty === 'normal') ? (Math.random() < 0.5 ? 0 : 30) : 0;
        choices.add(formatTimeText(dummyHour, dummyMin));
      }

      const shuffled = Array.from(choices).sort(() => Math.random() - 0.5);

      shuffled.forEach(text => {
        const btn = document.createElement('button');
        btn.className = "py-2.5 px-3 bg-emerald-50 hover:bg-emerald-100 active:bg-emerald-200 border border-emerald-300 font-bold text-emerald-900 text-xs sm:text-sm rounded-xl shadow-sm active:scale-95 transition-all text-center";
        btn.innerText = text;
        btn.onclick = () => checkQuizAnswer(text === correctAnswerText);
        container.appendChild(btn);
      });
    }

    function checkQuizAnswer(isCorrect) {
      if (isCorrect) {
        playSound('correct');
        score++;
        showFeedback(true, "せいかい！", "とけいが よく よめているね！");
      } else {
        playSound('wrong');
        const correctText = formatTimeText(targetHour, targetMinute);
        showFeedback(false, "ざんねん！", `せいかいは 「${correctText}」 でした。`);
      }
      updateScoreDisplay();
    }

    function checkSetAnswer() {
      const isCorrect = (userSetHour === targetHour) && (userSetMinute === targetMinute);
      if (isCorrect) {
        playSound('correct');
        score++;
        showFeedback(true, "だいせいかい！", "ぴったりの じかんに あわせられたね！");
      } else {
        playSound('wrong');
        showFeedback(false, "おしい！", "ハリの いちを もういちど たしかめてみよう。");
      }
      updateScoreDisplay();
    }

    function adjustTime(dh, dm) {
      userSetHour = (userSetHour + dh) > 12 ? 1 : userSetHour + dh;
      userSetMinute = (userSetMinute + dm) % 60;
      if (dm > 0 && userSetMinute === 0) {
        userSetHour = userSetHour % 12 + 1;
      }
      renderClock(userSetHour, userSetMinute);
    }

    function showFeedback(isSuccess, title, msg) {
      const banner = document.getElementById('feedback-banner');
      document.getElementById('feedback-icon').innerText = isSuccess ? '🎉' : '🤔';
      document.getElementById('feedback-title').innerText = title;
      document.getElementById('feedback-title').className = `text-lg font-bold mb-0.5 ${isSuccess ? 'text-emerald-600' : 'text-amber-600'}`;
      document.getElementById('feedback-msg').innerText = msg;

      const nextBtn = document.getElementById('next-btn');
      if (currentQuestion >= questionLimit) {
        nextBtn.innerText = "けっかを みる 🏁";
      } else {
        nextBtn.innerText = "つぎへ すすむ ➡️";
      }

      banner.classList.remove('hidden');
    }

    function nextQuestion() {
      if (currentQuestion >= questionLimit) {
        showFinalResult();
      } else {
        currentQuestion++;
        loadNewQuestion();
      }
    }

    function showFinalResult() {
      document.getElementById('feedback-banner').classList.add('hidden');
      document.getElementById('result-banner').classList.remove('hidden');

      playSound('fanfare');

      document.getElementById('result-score-text').innerText = `${questionLimit}もんちゅう ${score}もん せいかい！`;

      const ratio = score / questionLimit;
      let comment = "がんばったね！ つぎも チャレンジしてみよう！";
      if (ratio === 1) {
        comment = "パーフェクト！ すごいぞ！ とけいマスターだね！ 💮";
      } else if (ratio >= 0.7) {
        comment = "とても よくできました！ あとすこしで パーフェクト！ 🌟";
      } else if (ratio >= 0.5) {
        comment = "はんぶんいじょう せいかい！ そのちょうし！ 👍";
      }
      document.getElementById('result-comment').innerText = comment;
    }

    function restartGame() {
      currentQuestion = 1;
      score = 0;
      updateScoreDisplay();
      loadNewQuestion();
    }

    function updateScoreDisplay() {
      document.getElementById('score-text').innerText = `${score}てん`;

      const container = document.getElementById('badge-container');
      if (container) {
        const badges = container.children;
        for (let i = 0; i < badges.length; i++) {
          if (i < score) {
            badges[i].className = "text-xs text-yellow-500 pop-anim";
            badges[i].innerText = "🌟";
          } else {
            badges[i].className = "text-xs text-slate-300";
            badges[i].innerText = "⭐";
          }
        }
      }
    }

    function switchMode(mode) {
      currentMode = mode;
      const tabQuiz = document.getElementById('tab-quiz');
      const tabSet = document.getElementById('tab-set');
      const quizPanel = document.getElementById('quiz-panel');
      const setControls = document.getElementById('set-controls');
      const targetPrompt = document.getElementById('target-prompt');

      if (mode === 'quiz') {
        tabQuiz.className = "flex-1 sm:flex-none px-4 py-2 rounded-xl font-bold transition-all bg-emerald-600 text-white shadow-sm text-xs sm:text-sm";
        tabSet.className = "flex-1 sm:flex-none px-4 py-2 rounded-xl font-bold transition-all text-slate-600 hover:text-emerald-700 text-xs sm:text-sm";
        quizPanel.classList.remove('hidden');
        setControls.classList.add('hidden');
        targetPrompt.classList.add('hidden');
      } else {
        tabSet.className = "flex-1 sm:flex-none px-4 py-2 rounded-xl font-bold transition-all bg-emerald-600 text-white shadow-sm text-xs sm:text-sm";
        tabQuiz.className = "flex-1 sm:flex-none px-4 py-2 rounded-xl font-bold transition-all text-slate-600 hover:text-emerald-700 text-xs sm:text-sm";
        quizPanel.classList.add('hidden');
        setControls.classList.remove('hidden');
        setControls.classList.replace('hidden', 'flex');
        targetPrompt.classList.remove('hidden');
      }
      restartGame();
    }

    function setDifficulty(diff) {
      difficulty = diff;
      const btnEasy = document.getElementById('diff-easy');
      const btnNormal = document.getElementById('diff-normal');

      const activeClass = "px-3 py-1 text-xs rounded-xl font-bold bg-emerald-600 text-white shadow-sm border border-emerald-600 transition-all";
      const inactiveClass = "px-3 py-1 text-xs rounded-xl font-bold bg-white text-slate-600 hover:bg-slate-100 border border-slate-300 transition-all";

      if (diff === 'easy') {
        btnEasy.className = activeClass;
        btnNormal.className = inactiveClass;
      } else {
        btnNormal.className = activeClass;
        btnEasy.className = inactiveClass;
      }
      restartGame();
    }

    function setQuestionLimit(limit) {
      questionLimit = limit;
      const btn10 = document.getElementById('count-10');
      const btn20 = document.getElementById('count-20');

      const activeClass = "px-3 py-1 text-xs rounded-xl font-bold bg-sky-600 text-white shadow-sm border border-sky-600 transition-all";
      const inactiveClass = "px-3 py-1 text-xs rounded-xl font-bold bg-white text-slate-600 hover:bg-slate-100 border border-slate-300 transition-all";

      if (limit === 10) {
        btn10.className = activeClass;
        btn20.className = inactiveClass;
      } else {
        btn20.className = activeClass;
        btn10.className = inactiveClass;
      }
      restartGame();
    }

    restartGame();
  </script>
</body>
</html>
