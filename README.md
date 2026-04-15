<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover, user-scalable=no" />
  <title>Fruit Math Fall Deluxe</title>
  <style>
    :root {
      --bg-top: #12213b;
      --bg-bottom: #07111f;
      --panel: rgba(8, 16, 30, 0.88);
      --line: rgba(255,255,255,0.12);
      --text: #eef4ff;
      --sub: #aec3e4;
      --good: #61e59b;
      --bad: #ff7f8d;
      --warn: #ffd969;
      --shadow: 0 12px 30px rgba(0,0,0,.28);
    }

    * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
    html, body {
      margin: 0;
      width: 100%;
      height: 100%;
      overflow: hidden;
      color: var(--text);
      background: linear-gradient(180deg, var(--bg-top), var(--bg-bottom));
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      touch-action: manipulation;
    }

    body {
      display: flex;
      justify-content: center;
      align-items: stretch;
    }

    #gameWrap {
      position: relative;
      width: min(100vw, 520px);
      height: 100vh;
      height: 100dvh;
      max-height: 100dvh;
      overflow: hidden;
      background:
        radial-gradient(circle at top, rgba(255,255,255,0.08), transparent 28%),
        linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0));
      border-left: 1px solid rgba(255,255,255,0.08);
      border-right: 1px solid rgba(255,255,255,0.08);
      --safe-top: max(10px, env(safe-area-inset-top));
      --safe-bottom: max(10px, env(safe-area-inset-bottom));
      --hud-height: 92px;
      --question-height: 130px;
    }

    canvas {
      position: absolute;
      inset: 0;
      width: 100%;
      height: 100%;
      display: block;
    }

    .hud {
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      display: flex;
      justify-content: space-between;
      gap: 8px;
      padding: calc(var(--safe-top) + 4px) 10px 8px;
      z-index: 5;
      pointer-events: none;
    }

    .panel {
      background: var(--panel);
      border: 1px solid var(--line);
      border-radius: 16px;
      box-shadow: var(--shadow);
      backdrop-filter: blur(8px);
    }

    .stats {
      display: grid;
      grid-template-columns: repeat(4, minmax(0, 1fr));
      gap: 6px;
      padding: 10px 10px;
      align-items: center;
      min-height: 58px;
      flex: 1;
      min-width: 0;
    }

    .stat {
      display: flex;
      flex-direction: column;
      align-items: center;
      min-width: 0;
    }

    .label {
      font-size: 11px;
      color: var(--sub);
      line-height: 1.1;
      white-space: nowrap;
    }

    .value {
      font-size: 18px;
      font-weight: 800;
      line-height: 1.2;
      white-space: nowrap;
    }

    .controls {
      display: flex;
      gap: 8px;
      pointer-events: auto;
      align-items: flex-start;
      flex-shrink: 0;
    }

    .audioPanel {
      display: flex;
      align-items: center;
      gap: 6px;
      padding: 8px 8px;
      pointer-events: auto;
      flex-wrap: wrap;
      justify-content: center;
      max-width: 168px;
    }

    button, input[type="range"] {
      border: 0;
      color: var(--text);
      background: rgba(255,255,255,0.08);
      border: 1px solid var(--line);
      border-radius: 14px;
      padding: 10px 12px;
      font-weight: 700;
      min-height: 44px;
      box-shadow: var(--shadow);
      font-size: 13px;
    }

    button { cursor: pointer; }
    button:active { transform: translateY(1px) scale(.99); }

    input[type="range"] {
      width: 82px;
      padding: 0;
      min-height: auto;
      height: 8px;
      background: transparent;
      box-shadow: none;
      border: 0;
    }

    .rangeWrap {
      display: flex;
      align-items: center;
      gap: 6px;
    }

    .rangeLabel {
      font-size: 11px;
      color: var(--sub);
      font-weight: 700;
      white-space: nowrap;
    }

    .questionBox {
      position: absolute;
      left: 10px;
      right: 10px;
      bottom: calc(var(--safe-bottom) + 8px);
      z-index: 5;
      padding: 12px 14px;
      pointer-events: none;
      min-height: 112px;
    }

    .questionTop {
      display: flex;
      align-items: flex-start;
      justify-content: space-between;
      gap: 10px;
    }

    .questionMain {
      min-width: 0;
      flex: 1;
    }

    .questionLabel {
      font-size: 12px;
      color: var(--sub);
      margin-bottom: 6px;
    }

    .question {
      font-size: clamp(22px, 5.5vw, 34px);
      font-weight: 900;
      line-height: 1.15;
      margin-bottom: 6px;
      word-break: break-word;
    }

    .hint {
      font-size: 12px;
      color: var(--sub);
      line-height: 1.45;
    }

    .modeTag {
      display: inline-flex;
      font-size: 11px;
      font-weight: 800;
      color: #fff;
      padding: 6px 8px;
      border-radius: 999px;
      background: rgba(255,255,255,0.12);
      border: 1px solid rgba(255,255,255,0.12);
      white-space: nowrap;
      flex-shrink: 0;
    }

    .centerOverlay {
      position: absolute;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 20px;
      z-index: 8;
      background: linear-gradient(180deg, rgba(4,8,16,.18), rgba(4,8,16,.62));
    }

    .card {
      width: min(92vw, 430px);
      padding: 18px;
      text-align: center;
    }

    .title {
      margin: 0 0 8px;
      font-size: clamp(24px, 7vw, 34px);
      font-weight: 900;
    }

    .desc {
      margin: 0 auto 14px;
      color: var(--sub);
      font-size: 14px;
      line-height: 1.6;
      max-width: 34em;
    }

    .big {
      width: 100%;
      padding: 14px 16px;
      font-size: 17px;
      background: linear-gradient(180deg, rgba(150,197,255,.28), rgba(107,184,255,.12));
    }

    .miniRow {
      margin-top: 10px;
      display: flex;
      justify-content: center;
      gap: 8px;
      flex-wrap: wrap;
    }

    .badge {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      padding: 7px 10px;
      border-radius: 999px;
      background: rgba(255,255,255,0.07);
      border: 1px solid var(--line);
      color: var(--sub);
      font-size: 12px;
    }

    .modeChooser {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
      margin: 14px 0;
      text-align: left;
    }

    .modeCard {
      padding: 12px;
      border-radius: 14px;
      border: 1px solid var(--line);
      background: rgba(255,255,255,0.04);
      cursor: pointer;
      user-select: none;
    }

    .modeCard.active {
      outline: 2px solid rgba(255,255,255,0.18);
      background: rgba(255,255,255,0.08);
    }

    .modeCardTitle {
      font-size: 14px;
      font-weight: 800;
      margin-bottom: 4px;
    }

    .modeCardDesc {
      font-size: 12px;
      color: var(--sub);
      line-height: 1.5;
    }

    .toast {
      position: absolute;
      left: 50%;
      top: calc(var(--safe-top) + 86px);
      transform: translateX(-50%);
      z-index: 7;
      min-width: 120px;
      padding: 10px 14px;
      border-radius: 999px;
      font-weight: 800;
      text-align: center;
      opacity: 0;
      pointer-events: none;
      transition: opacity .18s ease, transform .18s ease;
      background: var(--panel);
      border: 1px solid var(--line);
      box-shadow: var(--shadow);
      max-width: calc(100vw - 40px);
    }

    .toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }
    .toast.good { color: var(--good); }
    .toast.bad { color: var(--bad); }
    .toast.warn { color: var(--warn); }

    .bossBanner {
      position: absolute;
      left: 50%;
      top: calc(var(--safe-top) + 136px);
      transform: translateX(-50%);
      z-index: 7;
      padding: 10px 18px;
      border-radius: 999px;
      background: rgba(255, 217, 105, 0.12);
      color: var(--warn);
      font-weight: 900;
      border: 1px solid rgba(255, 217, 105, 0.26);
      box-shadow: var(--shadow);
      opacity: 0;
      pointer-events: none;
      transition: opacity .18s ease;
      max-width: calc(100vw - 40px);
      white-space: nowrap;
    }

    .bossBanner.show { opacity: 1; }
    .hidden { display: none; }

    @media (max-width: 480px) {
      #gameWrap {
        --hud-height: 104px;
        --question-height: 122px;
      }

      .hud {
        flex-direction: column;
        gap: 8px;
      }

      .controls {
        width: 100%;
        justify-content: space-between;
      }

      .audioPanel {
        max-width: none;
      }

      .stats {
        width: 100%;
      }

      .value {
        font-size: 16px;
      }
    }

    @media (max-width: 420px) {
      #gameWrap {
        --hud-height: 112px;
        --question-height: 126px;
      }

      .modeChooser { grid-template-columns: 1fr; }
      .question { font-size: clamp(20px, 6vw, 28px); }
      .questionLabel, .hint, .label { font-size: 11px; }
      button { font-size: 12px; }
      .toast { top: calc(var(--safe-top) + 96px); }
      .bossBanner { top: calc(var(--safe-top) + 146px); }
    }

    @media (max-height: 740px) {
      #gameWrap {
        --hud-height: 108px;
        --question-height: 118px;
      }

      .questionBox { min-height: 100px; padding: 10px 12px; }
      .question { margin-bottom: 4px; }
      .hint { line-height: 1.35; }
    }
  </style>
</head>
<body>
  <div id="gameWrap">
    <canvas id="gameCanvas"></canvas>

    <div class="hud">
      <div class="panel stats">
        <div class="stat"><div class="label">スコア</div><div class="value" id="score">0</div></div>
        <div class="stat"><div class="label">連続正解</div><div class="value" id="combo">0</div></div>
        <div class="stat"><div class="label">ライフ</div><div class="value" id="lives">3</div></div>
        <div class="stat"><div class="label">残り時間</div><div class="value" id="timer">--</div></div>
      </div>
      <div class="controls">
        <div class="panel audioPanel">
          <button id="soundBtn" aria-label="音切替">音ON</button>
          <div class="rangeWrap">
            <span class="rangeLabel">音量</span>
            <input id="volumeSlider" type="range" min="0" max="100" value="70" />
          </div>
        </div>
        <button id="pauseBtn" aria-label="一時停止">一時停止</button>
      </div>
    </div>

    <div class="toast" id="toast"></div>
    <div class="bossBanner" id="bossBanner">BOSS QUESTION!</div>

    <div class="panel questionBox">
      <div class="questionTop">
        <div class="questionMain">
          <div class="questionLabel">この答えのフルーツをタップ</div>
          <div class="question" id="questionText">2 + 3</div>
          <div class="hint" id="hintText">見た目では正解は分からない。計算して見抜こう。</div>
        </div>
        <div class="modeTag" id="modeTag">エンドレス</div>
      </div>
    </div>

    <div class="centerOverlay" id="startOverlay">
      <div class="panel card">
        <h1 class="title">Fruit Math Fall Deluxe</h1>
        <p class="desc">
          フルーツに乗った数字が落ちてくる。計算問題の答えを見抜いてタップ。色では正解が分からないので、暗算力と瞬発力がそのままスコアになります。
        </p>

        <div class="modeChooser">
          <div class="modeCard active" data-mode="endless">
            <div class="modeCardTitle">エンドレス</div>
            <div class="modeCardDesc">ライフが尽きるまで。コンボでどんどん加速。</div>
          </div>
          <div class="modeCard" data-mode="timeattack">
            <div class="modeCardTitle">タイムアタック</div>
            <div class="modeCardDesc">60秒勝負。短時間でどこまで稼げるか。</div>
          </div>
        </div>

        <button class="big" id="startBtn">ゲーム開始</button>
        <div class="miniRow">
          <div class="badge">重なりにくい配置</div>
          <div class="badge">8bit風BGM</div>
          <div class="badge">ボス問題あり</div>
        </div>
      </div>
    </div>

    <div class="centerOverlay hidden" id="gameOverOverlay">
      <div class="panel card">
        <h2 class="title">ゲーム終了</h2>
        <p class="desc" id="finalText">スコア: 0</p>
        <button class="big" id="restartBtn">もう一度遊ぶ</button>
        <div class="miniRow">
          <div class="badge">最高スコア: <span id="bestScore">0</span></div>
          <div class="badge">モード: <span id="finalMode">エンドレス</span></div>
        </div>
      </div>
    </div>
  </div>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const wrap = document.getElementById('gameWrap');

    const scoreEl = document.getElementById('score');
    const comboEl = document.getElementById('combo');
    const livesEl = document.getElementById('lives');
    const timerEl = document.getElementById('timer');
    const questionTextEl = document.getElementById('questionText');
    const hintTextEl = document.getElementById('hintText');
    const toastEl = document.getElementById('toast');
    const bossBannerEl = document.getElementById('bossBanner');
    const modeTagEl = document.getElementById('modeTag');

    const startOverlay = document.getElementById('startOverlay');
    const gameOverOverlay = document.getElementById('gameOverOverlay');
    const finalTextEl = document.getElementById('finalText');
    const bestScoreEl = document.getElementById('bestScore');
    const finalModeEl = document.getElementById('finalMode');

    const startBtn = document.getElementById('startBtn');
    const restartBtn = document.getElementById('restartBtn');
    const pauseBtn = document.getElementById('pauseBtn');
    const soundBtn = document.getElementById('soundBtn');
    const volumeSlider = document.getElementById('volumeSlider');
    const modeCards = [...document.querySelectorAll('.modeCard')];

    let dpr = Math.max(1, window.devicePixelRatio || 1);
    let width = 0;
    let height = 0;
    let topSafeZone = 110;
    let bottomSafeZone = 130;
    let playableTop = 110;
    let playableBottom = 540;
    let laneCenters = [];

    let gameState = 'ready';
    let mode = 'endless';
    let score = 0;
    let combo = 0;
    let lives = 3;
    let bestScore = Number(localStorage.getItem('fruitMathBestDeluxe') || 0);
    let timeLeft = 60;
    let soundOn = true;
    let volumeLevel = Number(localStorage.getItem('fruitMathVolume') || 70) / 100;

    let items = [];
    let particles = [];
    let slices = [];
    let currentQuestion = null;
    let spawnTimer = 0;
    let lastTs = 0;
    let difficultyTime = 0;
    let bossActive = false;
    let nextBossAt = 7;
    let shakeTime = 0;

    const config = {
      baseSpawnInterval: 0.74,
      minSpawnInterval: 0.30,
      baseSpeed: 156,
      speedGainPerSec: 5.4,
      comboSpeedGain: 4.2,
      fruitSizeMin: 54,
      fruitSizeMax: 84,
      maxVisible: 14,
      laneCount: 4,
      horizontalPadding: 10,
      overlapPadding: 10,
      spawnTries: 40,
      timeAttackDuration: 60,
    };

    const fruitThemes = [
      { type: 'apple', body1: '#ff6b6b', body2: '#d73a49', leaf: '#4caf50', stem: '#6d4c41' },
      { type: 'orange', body1: '#ffb347', body2: '#f57c00', leaf: '#5bbf63', stem: '#6d4c41' },
      { type: 'pear', body1: '#c7f36b', body2: '#84b735', leaf: '#4aa65a', stem: '#6d4c41' },
      { type: 'plum', body1: '#b388ff', body2: '#6a3fb1', leaf: '#70c26f', stem: '#6d4c41' },
      { type: 'lemon', body1: '#ffe66d', body2: '#d9bf35', leaf: '#62b55d', stem: '#6d4c41' },
      { type: 'peach', body1: '#ffb08a', body2: '#ef6c57', leaf: '#5cb85c', stem: '#6d4c41' },
      { type: 'berry', body1: '#69a7ff', body2: '#3f65d6', leaf: '#63bd72', stem: '#6d4c41' },
    ];

    let audioCtx = null;
    let masterGain = null;
    let bgmGain = null;
    let sfxGain = null;
    let bgmNodes = [];
    let bgmStarted = false;
    let bgmTimer = null;
    let bgmMode = 'normal';
    let bgmScheduleToken = 0;

    function resize() {
      const rect = wrap.getBoundingClientRect();
      width = Math.floor(rect.width);
      height = Math.floor(rect.height);
      dpr = Math.max(1, window.devicePixelRatio || 1);
      canvas.width = Math.floor(width * dpr);
      canvas.height = Math.floor(height * dpr);
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
      recalcSafeZones();
    }

    function recalcSafeZones() {
      const styles = getComputedStyle(wrap);
      const safeTop = parseFloat(styles.getPropertyValue('--safe-top')) || 10;
      const safeBottom = parseFloat(styles.getPropertyValue('--safe-bottom')) || 10;
      const hudHeight = parseFloat(styles.getPropertyValue('--hud-height')) || 96;
      const questionHeight = parseFloat(styles.getPropertyValue('--question-height')) || 126;

      topSafeZone = safeTop + hudHeight;
      bottomSafeZone = safeBottom + questionHeight;
      playableTop = Math.max(70, topSafeZone + 14);
      playableBottom = Math.max(playableTop + 120, height - bottomSafeZone - 10);

      const laneCount = width < 360 ? 3 : config.laneCount;
      const usableWidth = width - config.horizontalPadding * 2;
      const laneWidth = usableWidth / laneCount;
      laneCenters = [];
      for (let i = 0; i < laneCount; i++) {
        laneCenters.push(config.horizontalPadding + laneWidth * (i + 0.5));
      }
    }

    function rand(min, max) { return Math.random() * (max - min) + min; }
    function randInt(min, max) { return Math.floor(Math.random() * (max - min + 1)) + min; }
    function clamp(v, min, max) { return Math.max(min, Math.min(max, v)); }
    function pick(arr) { return arr[Math.floor(Math.random() * arr.length)]; }
    function distanceSq(ax, ay, bx, by) { const dx = ax - bx; const dy = ay - by; return dx * dx + dy * dy; }

    function updateHud() {
      scoreEl.textContent = score;
      comboEl.textContent = combo;
      livesEl.textContent = lives;
      bestScoreEl.textContent = bestScore;
      timerEl.textContent = mode === 'timeattack' ? Math.max(0, Math.ceil(timeLeft)) : '--';
      modeTagEl.textContent = mode === 'timeattack' ? 'タイムアタック' : 'エンドレス';
    }

    function showToast(text, kind = 'good', duration = 450) {
      toastEl.textContent = text;
      toastEl.className = `toast show ${kind}`;
      clearTimeout(showToast._timer);
      showToast._timer = setTimeout(() => {
        toastEl.className = 'toast';
      }, duration);
    }

    function showBossBanner() {
      bossBannerEl.classList.add('show');
      clearTimeout(showBossBanner._timer);
      showBossBanner._timer = setTimeout(() => bossBannerEl.classList.remove('show'), 1200);
    }

    function applyVolumeSettings() {
      if (!masterGain || !bgmGain || !sfxGain) return;
      masterGain.gain.value = soundOn ? Math.max(0, Math.min(1, volumeLevel)) : 0;
      bgmGain.gain.value = 0.06;
      sfxGain.gain.value = 0.95;
    }

    function ensureAudio() {
      if (!soundOn) return null;
      if (!audioCtx) {
        const AC = window.AudioContext || window.webkitAudioContext;
        if (!AC) return null;
        audioCtx = new AC();
        masterGain = audioCtx.createGain();
        bgmGain = audioCtx.createGain();
        sfxGain = audioCtx.createGain();
        applyVolumeSettings();
        bgmGain.connect(masterGain);
        sfxGain.connect(masterGain);
        masterGain.connect(audioCtx.destination);
      }
      if (audioCtx.state === 'suspended') audioCtx.resume();
      return audioCtx;
    }

    function stopBgm() {
      bgmScheduleToken++;
      if (bgmTimer) {
        clearTimeout(bgmTimer);
        bgmTimer = null;
      }
      bgmNodes.forEach(node => {
        try { node.stop(); } catch (_) {}
      });
      bgmNodes = [];
      bgmStarted = false;
    }

    function scheduleBgmLoop(modeName = 'normal') {
      const ac = ensureAudio();
      if (!ac || !soundOn || !bgmGain) return;
      stopBgm();
      bgmStarted = true;
      bgmMode = modeName;
      const scheduleToken = ++bgmScheduleToken;

      const now = ac.currentTime + 0.03;
      const beat = modeName === 'boss' ? 0.25 : 0.31;
      const bar = beat * 4;
      const totalBars = 8;
      const loopLength = bar * totalBars;

      const bassLine = modeName === 'boss'
        ? [98, 98, 123.47, 123.47, 164.81, 164.81, 146.83, 146.83]
        : [110, 110, 146.83, 146.83, 164.81, 164.81, 130.81, 130.81];
      const chordRoots = modeName === 'boss'
        ? [196, 196, 246.94, 246.94, 329.63, 329.63, 293.66, 293.66]
        : [220, 220, 293.66, 293.66, 329.63, 329.63, 261.63, 261.63];
      const leadPattern = modeName === 'boss'
        ? [783.99, 880.0, 987.77, 880.0, 783.99, 1046.5, 987.77, 880.0]
        : [659.25, 587.33, 523.25, 587.33, 659.25, 783.99, 698.46, 587.33];

      function addTone(freq, start, dur, type, gainValue, targetGain = bgmGain, slideTo = null, detune = 0) {
        const osc = ac.createOscillator();
        const g = ac.createGain();
        osc.type = type;
        osc.detune.value = detune;
        osc.frequency.setValueAtTime(freq, start);
        if (slideTo) osc.frequency.exponentialRampToValueAtTime(slideTo, start + dur);
        g.gain.setValueAtTime(0.0001, start);
        g.gain.exponentialRampToValueAtTime(gainValue, start + 0.01);
        g.gain.exponentialRampToValueAtTime(0.0001, start + dur);
        osc.connect(g);
        g.connect(targetGain);
        osc.start(start);
        osc.stop(start + dur + 0.03);
        bgmNodes.push(osc);
      }

      for (let barIndex = 0; barIndex < totalBars; barIndex++) {
        const barStart = now + bar * barIndex;
        const bassFreq = bassLine[barIndex];
        const chordRoot = chordRoots[barIndex];
        const leadFreq = leadPattern[barIndex];

        addTone(bassFreq, barStart, beat * 1.6, 'square', modeName === 'boss' ? 0.09 : 0.075);
        addTone(bassFreq * 2, barStart + beat * 0.08, beat * 0.32, 'triangle', 0.018);
        addTone(bassFreq, barStart + beat * 2, beat * 1.55, 'square', modeName === 'boss' ? 0.085 : 0.07);

        addTone(chordRoot, barStart, bar * 0.96, 'triangle', modeName === 'boss' ? 0.018 : 0.014, bgmGain, null, -4);
        addTone(chordRoot * 1.25, barStart, bar * 0.96, 'triangle', modeName === 'boss' ? 0.015 : 0.012, bgmGain, null, 3);
        addTone(chordRoot * 1.5, barStart, bar * 0.96, 'sine', 0.010);

        addTone(leadFreq, barStart + beat * 0.0, beat * 0.28, 'square', modeName === 'boss' ? 0.024 : 0.018);
        addTone(leadFreq * 1.122, barStart + beat * 0.5, beat * 0.22, 'square', modeName === 'boss' ? 0.020 : 0.014);
        addTone(leadFreq * 0.89, barStart + beat * 1.0, beat * 0.28, 'square', modeName === 'boss' ? 0.023 : 0.017);
        addTone(leadFreq * 1.122, barStart + beat * 1.5, beat * 0.22, 'square', modeName === 'boss' ? 0.019 : 0.013);
        addTone(leadFreq * 1.335, barStart + beat * 2.0, beat * 0.28, 'square', modeName === 'boss' ? 0.025 : 0.018);
        addTone(leadFreq * 1.5, barStart + beat * 2.5, beat * 0.22, 'square', modeName === 'boss' ? 0.020 : 0.014);
        addTone(leadFreq * 1.335, barStart + beat * 3.0, beat * 0.48, 'square', modeName === 'boss' ? 0.022 : 0.016, bgmGain, leadFreq * 1.0);

        for (let s = 0; s < 8; s++) {
          const t = barStart + beat * 0.5 * s;
          addTone(modeName === 'boss' ? 1320 : 1046.5, t, 0.026, 'square', modeName === 'boss' ? 0.006 : 0.004);
        }
      }

      bgmTimer = setTimeout(() => {
        if (scheduleToken !== bgmScheduleToken) return;
        if (soundOn && (gameState === 'running' || gameState === 'paused')) {
          scheduleBgmLoop(bgmMode);
        }
      }, Math.max(100, (loopLength - 0.12) * 1000));
    }

    function startBgmIfNeeded(forceMode = null) {
      const ac = ensureAudio();
      if (!ac || !soundOn) return;
      const desiredMode = forceMode || (currentQuestion && currentQuestion.boss ? 'boss' : 'normal');
      if (!bgmStarted || bgmMode !== desiredMode) {
        scheduleBgmLoop(desiredMode);
      }
    }

    function beep(freq, duration, type = 'sine', gain = 0.03, slideTo = null) {
      const ac = ensureAudio();
      if (!ac || !sfxGain) return;
      const now = ac.currentTime;
      const osc = ac.createOscillator();
      const g = ac.createGain();
      osc.type = type;
      osc.frequency.setValueAtTime(freq, now);
      if (slideTo) osc.frequency.exponentialRampToValueAtTime(Math.max(40, slideTo), now + duration);
      g.gain.setValueAtTime(0.0001, now);
      g.gain.exponentialRampToValueAtTime(gain, now + 0.01);
      g.gain.exponentialRampToValueAtTime(0.0001, now + duration);
      osc.connect(g);
      g.connect(sfxGain);
      osc.start(now);
      osc.stop(now + duration + 0.02);
    }

    function soundCorrect() {
      beep(560, 0.08, 'triangle', 0.035, 760);
      setTimeout(() => beep(840, 0.09, 'triangle', 0.03, 980), 45);
    }

    function soundWrong() {
      beep(240, 0.12, 'sawtooth', 0.03, 160);
    }

    function soundBoss() {
      beep(330, 0.12, 'square', 0.03, 420);
      setTimeout(() => beep(520, 0.15, 'square', 0.03, 650), 80);
    }

    function soundSlice() {
      beep(700, 0.05, 'triangle', 0.018, 980);
    }

    function setMode(nextMode) {
      mode = nextMode;
      modeCards.forEach(card => card.classList.toggle('active', card.dataset.mode === nextMode));
      updateHud();
    }

    function generateQuestion(forceBoss = false) {
      const boss = forceBoss;
      let type, a, b, c, answer, text;

      if (boss) {
        type = pick(['mix1', 'mix2', 'mix3']);
        if (type === 'mix1') {
          a = randInt(2, 12); b = randInt(2, 9); c = randInt(1, 18);
          answer = a * b + c;
          text = `${a} × ${b} + ${c}`;
        } else if (type === 'mix2') {
          b = randInt(2, 10); c = randInt(2, 10); answer = randInt(6, 24);
          a = answer + b * c;
          text = `${a} − ${b} × ${c}`;
        } else {
          a = randInt(2, 12); b = randInt(2, 6); c = randInt(2, 12);
          answer = a * (b + c);
          text = `${a} × (${b} + ${c})`;
        }
      } else {
        type = pick(['+', '-', '×', '÷']);
        if (type === '+') {
          a = randInt(1, 20); b = randInt(1, 20); answer = a + b; text = `${a} + ${b}`;
        } else if (type === '-') {
          a = randInt(5, 30); b = randInt(1, a - 1); answer = a - b; text = `${a} − ${b}`;
        } else if (type === '×') {
          a = randInt(2, 12); b = randInt(2, 12); answer = a * b; text = `${a} × ${b}`;
        } else {
          b = randInt(2, 12); answer = randInt(2, 12); a = b * answer; text = `${a} ÷ ${b}`;
        }
      }

      bossActive = boss;
      currentQuestion = { text, answer, boss };
      questionTextEl.textContent = text;
      hintTextEl.textContent = boss ? 'ボス問題。式が重いぶん高得点。' : '見た目では正解は分からない。計算して見抜こう。';
      if (boss) {
        showBossBanner();
        showToast('ボス問題！', 'warn', 700);
        soundBoss();
        startBgmIfNeeded('boss');
      } else {
        startBgmIfNeeded('normal');
      }
    }

    function randomWrongAnswer(correct) {
      let n;
      let attempts = 0;
      do {
        const spread = correct < 10 ? 8 : correct < 30 ? 14 : correct < 80 ? 22 : 30;
        n = correct + randInt(-spread, spread);
        if (Math.random() < 0.2) n = randInt(0, 180);
        attempts++;
      } while ((n === correct || n < 0 || n > 180) && attempts < 40);
      if (n === correct || n < 0) n = correct + randInt(1, 4);
      return n;
    }

    function createFruitTheme() { return { ...pick(fruitThemes) }; }

    function findSpawnPosition(radius) {
      const lanePool = [...laneCenters];
      for (let i = lanePool.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [lanePool[i], lanePool[j]] = [lanePool[j], lanePool[i]];
      }

      const upperSpawnBase = -radius - rand(10, 70);
      for (const laneX of lanePool) {
        for (let t = 0; t < config.spawnTries; t++) {
          const x = clamp(laneX + rand(-12, 12), config.horizontalPadding + radius, width - config.horizontalPadding - radius);
          const y = upperSpawnBase - t * rand(4, 10);
          let blocked = false;
          for (const item of items) {
            if (!item.alive) continue;
            const minGap = (item.r + radius + config.overlapPadding);
            const sameLaneish = Math.abs(item.x - x) < minGap * 0.9;
            const nearSpawnBand = item.y < playableTop + radius * 3.4;
            if (sameLaneish && nearSpawnBand && distanceSq(item.x, item.y, x, y) < minGap * minGap) {
              blocked = true;
              break;
            }
          }
          if (!blocked) return { x, y };
        }
      }

      return {
        x: clamp(pick(laneCenters) + rand(-14, 14), config.horizontalPadding + radius, width - config.horizontalPadding - radius),
        y: -radius - rand(40, 140),
      };
    }

    function createItem(value, isCorrect = false, options = {}) {
      const size = rand(config.fruitSizeMin, config.fruitSizeMax) * (options.big ? 1.22 : 1);
      const radius = size / 2;
      const spawn = findSpawnPosition(radius);
      const comboBoost = combo * config.comboSpeedGain;
      const speed = config.baseSpeed + difficultyTime * config.speedGainPerSec + comboBoost + rand(-14, 62);
      const drift = rand(-10, 10);
      const theme = createFruitTheme();

      items.push({
        x: spawn.x,
        y: spawn.y,
        r: radius,
        size,
        value,
        isCorrect,
        speed,
        drift,
        rotation: rand(-0.16, 0.16),
        spin: rand(-0.38, 0.38),
        pulse: rand(0, Math.PI * 2),
        shapeSeed: rand(0.88, 1.14),
        theme,
        alive: true,
        big: !!options.big,
      });
    }

    function separateOverlaps() {
      for (let pass = 0; pass < 2; pass++) {
        for (let i = 0; i < items.length; i++) {
          const a = items[i];
          if (!a.alive) continue;
          for (let j = i + 1; j < items.length; j++) {
            const b = items[j];
            if (!b.alive) continue;
            const minDist = a.r + b.r + 6;
            const dx = b.x - a.x;
            const dy = b.y - a.y;
            const dist = Math.sqrt(dx * dx + dy * dy) || 0.001;
            if (dist < minDist) {
              const push = (minDist - dist) * 0.5;
              const nx = dx / dist;
              const ny = dy / dist;
              a.x -= nx * push;
              b.x += nx * push;
              if (a.y < playableTop + 140) a.y -= ny * push * 0.3;
              if (b.y < playableTop + 140) b.y += ny * push * 0.3;
              a.x = clamp(a.x, a.r, width - a.r);
              b.x = clamp(b.x, b.r, width - b.r);
            }
          }
        }
      }
    }

    function clearCorrectFlags() {
      items.forEach(i => i.isCorrect = false);
    }

    function ensureOnlyOneCorrect(answer) {
      const alive = items.filter(i => i.alive);
      const matching = alive.filter(i => i.value === answer);
      if (matching.length === 0) {
        createItem(answer, true, { big: bossActive });
      } else {
        clearCorrectFlags();
        pick(matching).isCorrect = true;
      }
    }

    function spawnWave() {
      if (!currentQuestion) generateQuestion();
      const visibleCorrect = items.some(i => i.isCorrect && i.alive);
      const baseCount = bossActive ? randInt(1, 3) : randInt(1, 2 + Math.floor(difficultyTime / 22));

      if (!visibleCorrect) createItem(currentQuestion.answer, true, { big: bossActive });
      for (let i = 0; i < baseCount; i++) createItem(randomWrongAnswer(currentQuestion.answer), false);
      ensureOnlyOneCorrect(currentQuestion.answer);
      separateOverlaps();
      if (items.length > config.maxVisible) items.splice(0, items.length - config.maxVisible);
    }

    function burst(x, y, good, count = 14) {
      for (let i = 0; i < count; i++) {
        particles.push({
          x, y,
          vx: rand(-140, 140),
          vy: rand(-170, 70),
          life: rand(0.28, 0.64),
          t: 0,
          size: rand(3, 7),
          good,
        });
      }
    }

    function createSlicePieces(item) {
      const left = { ...item };
      const right = { ...item };
      slices.push({ ...left, half: 'left', vx: -90 - rand(0, 60), vy: -120 - rand(0, 60), vr: -2.8, life: 0.7, t: 0 });
      slices.push({ ...right, half: 'right', vx: 90 + rand(0, 60), vy: -120 - rand(0, 60), vr: 2.8, life: 0.7, t: 0 });
    }

    function maybeTriggerBoss() {
      if (combo >= nextBossAt && !bossActive) {
        generateQuestion(true);
        nextBossAt += 7;
        items = items.filter(i => i.alive && !i.isCorrect);
        createItem(currentQuestion.answer, true, { big: true });
        separateOverlaps();
      }
    }

    function handleCorrect(item) {
      item.alive = false;
      const bossBonus = currentQuestion.boss ? 18 : 0;
      score += 10 + Math.min(combo, 20) + bossBonus;
      combo += 1;
      shakeTime = 0.12;
      createSlicePieces(item);
      burst(item.x, item.y, true, currentQuestion.boss ? 26 : 16);
      showToast(currentQuestion.boss ? 'ボス撃破！' : '正解！', 'good', 520);
      soundCorrect();
      soundSlice();
      generateQuestion(false);
      maybeTriggerBoss();
      ensureOnlyOneCorrect(currentQuestion.answer);
      separateOverlaps();
      startBgmIfNeeded(currentQuestion && currentQuestion.boss ? 'boss' : 'normal');
      updateHud();
    }

    function handleWrong(item) {
      item.alive = false;
      combo = 0;
      lives -= 1;
      shakeTime = 0.18;
      burst(item.x, item.y, false, 12);
      showToast('ミス！', 'bad');
      soundWrong();
      updateHud();
      if (lives <= 0) endGame();
    }

    function endGame() {
      gameState = 'over';
      stopBgm();
      finalTextEl.textContent = mode === 'timeattack'
        ? `スコア: ${score} / 連続正解: ${combo} / 60秒終了`
        : `スコア: ${score} / 連続正解: ${combo}`;
      if (score > bestScore) {
        bestScore = score;
        localStorage.setItem('fruitMathBestDeluxe', String(bestScore));
        bestScoreEl.textContent = bestScore;
      }
      finalModeEl.textContent = mode === 'timeattack' ? 'タイムアタック' : 'エンドレス';
      gameOverOverlay.classList.remove('hidden');
      pauseBtn.textContent = '一時停止';
      bossBannerEl.classList.remove('show');
    }

    function resetGame() {
      ensureAudio();
      score = 0;
      combo = 0;
      lives = mode === 'timeattack' ? 5 : 3;
      timeLeft = config.timeAttackDuration;
      items = [];
      particles = [];
      slices = [];
      spawnTimer = 0;
      difficultyTime = 0;
      bossActive = false;
      nextBossAt = 7;
      shakeTime = 0;
      generateQuestion(false);
      startBgmIfNeeded('normal');
      createItem(currentQuestion.answer, true);
      for (let i = 0; i < 4; i++) createItem(randomWrongAnswer(currentQuestion.answer), false);
      separateOverlaps();
      updateHud();
      gameOverOverlay.classList.add('hidden');
      startOverlay.classList.add('hidden');
      gameState = 'running';
      lastTs = performance.now();
    }

    function togglePause() {
      if (gameState === 'running') {
        gameState = 'paused';
        pauseBtn.textContent = '再開';
      } else if (gameState === 'paused') {
        gameState = 'running';
        pauseBtn.textContent = '一時停止';
        lastTs = performance.now();
        startBgmIfNeeded();
      }
    }

    function update(dt) {
      difficultyTime += dt;
      if (mode === 'timeattack') {
        timeLeft -= dt;
        if (timeLeft <= 0) {
          timeLeft = 0;
          updateHud();
          endGame();
          return;
        }
      }

      const interval = Math.max(config.minSpawnInterval, config.baseSpawnInterval - difficultyTime * 0.0045 - combo * 0.0035);
      spawnTimer += dt;
      if (spawnTimer >= interval) {
        spawnTimer = 0;
        spawnWave();
      }

      if (shakeTime > 0) shakeTime -= dt;

      for (const item of items) {
        if (!item.alive) continue;
        item.y += item.speed * dt;
        item.x += item.drift * dt;
        item.rotation += item.spin * dt;
        item.pulse += dt * 3.4;

        if (item.x < item.r) {
          item.x = item.r;
          item.drift *= -1;
        } else if (item.x > width - item.r) {
          item.x = width - item.r;
          item.drift *= -1;
        }

        if (item.y + item.r > playableBottom) {
          item.alive = false;
          if (item.isCorrect && gameState === 'running') {
            combo = 0;
            lives -= 1;
            shakeTime = 0.16;
            showToast('取り逃がし！', 'bad');
            soundWrong();
            generateQuestion(false);
            ensureOnlyOneCorrect(currentQuestion.answer);
            separateOverlaps();
            startBgmIfNeeded('normal');
            updateHud();
            if (lives <= 0) {
              endGame();
              return;
            }
          }
        }
      }

      items = items.filter(i => i.alive);
      separateOverlaps();

      for (const p of particles) {
        p.t += dt;
        p.x += p.vx * dt;
        p.y += p.vy * dt;
        p.vy += 250 * dt;
      }
      particles = particles.filter(p => p.t < p.life);

      for (const s of slices) {
        s.t += dt;
        s.x += s.vx * dt;
        s.y += s.vy * dt;
        s.vy += 280 * dt;
        s.rotation += s.vr * dt;
      }
      slices = slices.filter(s => s.t < s.life);

      if (!items.some(i => i.alive && i.isCorrect)) {
        createItem(currentQuestion.answer, true, { big: currentQuestion.boss });
        ensureOnlyOneCorrect(currentQuestion.answer);
        separateOverlaps();
      }

      updateHud();
    }

    function drawBackgroundGrid() {
      ctx.save();
      ctx.globalAlpha = 0.11;
      ctx.strokeStyle = 'rgba(255,255,255,0.08)';
      ctx.lineWidth = 1;
      const gap = 42;
      for (let x = 0; x <= width; x += gap) {
        ctx.beginPath(); ctx.moveTo(x, playableTop - 40); ctx.lineTo(x, playableBottom + 20); ctx.stroke();
      }
      for (let y = playableTop - 40; y <= playableBottom + 20; y += gap) {
        ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(width, y); ctx.stroke();
      }
      ctx.restore();
    }

    function drawPlayableZoneGuides() {
      ctx.save();
      ctx.globalAlpha = 0.08;
      ctx.fillStyle = 'rgba(255,255,255,1)';
      ctx.fillRect(0, 0, width, playableTop - 8);
      ctx.fillRect(0, playableBottom + 8, width, height - (playableBottom + 8));
      ctx.restore();
    }

    function drawBottomDangerZone() {
      const h = Math.min(90, height - playableBottom + 26);
      const y = playableBottom - 20;
      const grad = ctx.createLinearGradient(0, y, 0, y + h);
      grad.addColorStop(0, 'rgba(255, 98, 120, 0.0)');
      grad.addColorStop(1, 'rgba(255, 98, 120, 0.14)');
      ctx.fillStyle = grad;
      ctx.fillRect(0, y, width, h);
    }

    function drawAppleLike(ctx, r, stretch = 1) {
      ctx.beginPath();
      ctx.moveTo(0, -r * 0.92);
      ctx.bezierCurveTo(r * 0.78, -r * 1.2, r * 1.1, -r * 0.1, r * 0.62, r * 0.7 * stretch);
      ctx.bezierCurveTo(r * 0.3, r * 1.06 * stretch, -r * 0.3, r * 1.06 * stretch, -r * 0.62, r * 0.7 * stretch);
      ctx.bezierCurveTo(-r * 1.1, -r * 0.1, -r * 0.78, -r * 1.2, 0, -r * 0.92);
      ctx.closePath();
    }

    function drawPear(ctx, r) {
      ctx.beginPath();
      ctx.moveTo(0, -r * 0.92);
      ctx.bezierCurveTo(r * 0.68, -r * 0.92, r * 0.52, -r * 0.12, r * 0.76, r * 0.42);
      ctx.bezierCurveTo(r * 0.92, r * 0.98, r * 0.3, r * 1.12, 0, r * 1.08);
      ctx.bezierCurveTo(-r * 0.3, r * 1.12, -r * 0.92, r * 0.98, -r * 0.76, r * 0.42);
      ctx.bezierCurveTo(-r * 0.52, -r * 0.12, -r * 0.68, -r * 0.92, 0, -r * 0.92);
      ctx.closePath();
    }

    function drawLemon(ctx, r) {
      ctx.beginPath();
      ctx.moveTo(0, -r * 0.92);
      ctx.bezierCurveTo(r * 1.02, -r * 0.72, r * 1.02, r * 0.72, 0, r * 0.92);
      ctx.bezierCurveTo(-r * 1.02, r * 0.72, -r * 1.02, -r * 0.72, 0, -r * 0.92);
      ctx.closePath();
    }

    function traceFruitShape(item) {
      const { type } = item.theme;
      if (type === 'pear') drawPear(ctx, item.r);
      else if (type === 'lemon') drawLemon(ctx, item.r);
      else drawAppleLike(ctx, item.r, item.shapeSeed);
    }

    function drawLeafAndStem(item) {
      const r = item.r;
      ctx.save();
      ctx.lineCap = 'round';
      ctx.strokeStyle = item.theme.stem;
      ctx.lineWidth = Math.max(3, r * 0.08);
      ctx.beginPath();
      ctx.moveTo(0, -r * 0.86);
      ctx.quadraticCurveTo(r * 0.05, -r * 1.12, r * 0.14, -r * 1.25);
      ctx.stroke();

      ctx.fillStyle = item.theme.leaf;
      ctx.beginPath();
      ctx.moveTo(r * 0.08, -r * 1.06);
      ctx.quadraticCurveTo(r * 0.6, -r * 1.22, r * 0.38, -r * 0.8);
      ctx.quadraticCurveTo(r * 0.18, -r * 0.72, r * 0.08, -r * 1.06);
      ctx.fill();
      ctx.restore();
    }

    function drawFruitCore(item, alpha = 1) {
      const { body1, body2 } = item.theme;
      const r = item.r * (1 + Math.sin(item.pulse || 0) * 0.022);
      ctx.save();
      ctx.globalAlpha = alpha;
      ctx.shadowBlur = 18;
      ctx.shadowColor = 'rgba(0,0,0,.24)';
      const temp = { ...item, r };
      traceFruitShape(temp);
      const grad = ctx.createLinearGradient(-r * 0.8, -r, r, r);
      grad.addColorStop(0, body1);
      grad.addColorStop(1, body2);
      ctx.fillStyle = grad;
      ctx.fill();
      ctx.shadowBlur = 0;
      ctx.lineWidth = Math.max(2, r * 0.05);
      ctx.strokeStyle = 'rgba(255,255,255,.22)';
      ctx.stroke();
      ctx.save();
      ctx.globalAlpha *= 0.25;
      ctx.fillStyle = 'white';
      ctx.beginPath();
      ctx.ellipse(-r * 0.22, -r * 0.28, r * 0.22, r * 0.12, -0.4, 0, Math.PI * 2);
      ctx.fill();
      ctx.restore();
      drawLeafAndStem(temp);
      ctx.fillStyle = '#ffffff';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.lineWidth = Math.max(3, r * 0.09);
      ctx.strokeStyle = 'rgba(0,0,0,.22)';
      ctx.font = `900 ${Math.max(21, r * 0.86)}px system-ui, sans-serif`;
      ctx.strokeText(String(item.value), 0, r * 0.05);
      ctx.fillText(String(item.value), 0, r * 0.05);
      ctx.restore();
    }

    function drawFruit(item) {
      ctx.save();
      ctx.translate(item.x, item.y);
      ctx.rotate(item.rotation);
      if (item.big && currentQuestion && currentQuestion.boss) {
        ctx.save();
        ctx.shadowBlur = 22;
        ctx.shadowColor = 'rgba(255,217,105,.4)';
        ctx.beginPath();
        ctx.arc(0, 0, item.r * 1.08, 0, Math.PI * 2);
        ctx.fillStyle = 'rgba(255,217,105,.12)';
        ctx.fill();
        ctx.restore();
      }
      drawFruitCore(item, 1);
      ctx.restore();
    }

    function drawSlice(item) {
      const alpha = 1 - item.t / item.life;
      ctx.save();
      ctx.translate(item.x, item.y);
      ctx.rotate(item.rotation);
      ctx.beginPath();
      if (item.half === 'left') ctx.rect(-item.r * 1.2, -item.r * 1.4, item.r * 1.2, item.r * 2.8);
      else ctx.rect(0, -item.r * 1.4, item.r * 1.2, item.r * 2.8);
      ctx.clip();
      drawFruitCore(item, alpha);
      ctx.restore();
    }

    function drawParticles() {
      for (const p of particles) {
        const alpha = 1 - p.t / p.life;
        ctx.save();
        ctx.globalAlpha = alpha;
        ctx.fillStyle = p.good ? 'rgba(88,227,148,1)' : 'rgba(255,122,136,1)';
        ctx.beginPath();
        ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
        ctx.fill();
        ctx.restore();
      }
    }

    function drawComboAura() {
      if (combo < 3) return;
      ctx.save();
      ctx.globalAlpha = Math.min(0.18, combo * 0.012);
      const radius = 50 + combo * 4;
      const grad = ctx.createRadialGradient(width / 2, playableTop - 14, 10, width / 2, playableTop - 14, radius);
      grad.addColorStop(0, 'rgba(255,255,255,.2)');
      grad.addColorStop(1, 'rgba(255,255,255,0)');
      ctx.fillStyle = grad;
      ctx.beginPath();
      ctx.arc(width / 2, playableTop - 14, radius, 0, Math.PI * 2);
      ctx.fill();
      ctx.restore();
    }

    function drawPausedLayer() {
      if (gameState !== 'paused') return;
      ctx.save();
      ctx.fillStyle = 'rgba(0,0,0,.34)';
      ctx.fillRect(0, 0, width, height);
      ctx.fillStyle = '#fff';
      ctx.textAlign = 'center';
      ctx.font = '900 32px system-ui, sans-serif';
      ctx.fillText('PAUSED', width / 2, height / 2);
      ctx.restore();
    }

    function drawTimeAttackFrame() {
      if (mode !== 'timeattack') return;
      ctx.save();
      ctx.globalAlpha = 0.16;
      ctx.strokeStyle = timeLeft <= 10 ? 'rgba(255,120,120,1)' : 'rgba(255,255,255,1)';
      ctx.lineWidth = 6;
      ctx.strokeRect(3, topSafeZone - 2, width - 6, playableBottom - topSafeZone + 6);
      ctx.restore();
    }

    function draw() {
      ctx.clearRect(0, 0, width, height);
      let ox = 0, oy = 0;
      if (shakeTime > 0) {
        ox = rand(-6, 6) * shakeTime * 7;
        oy = rand(-5, 5) * shakeTime * 7;
      }
      ctx.save();
      ctx.translate(ox, oy);
      drawPlayableZoneGuides();
      drawBackgroundGrid();
      drawBottomDangerZone();
      drawComboAura();
      for (const item of items) drawFruit(item);
      for (const s of slices) drawSlice(s);
      drawParticles();
      drawTimeAttackFrame();
      ctx.restore();
      drawPausedLayer();
    }

    function getPointFromEvent(e) {
      const rect = canvas.getBoundingClientRect();
      if (e.touches && e.touches[0]) return { x: e.touches[0].clientX - rect.left, y: e.touches[0].clientY - rect.top };
      return { x: e.clientX - rect.left, y: e.clientY - rect.top };
    }

    function handlePointer(e) {
      if (gameState !== 'running') return;
      ensureAudio();
      const p = getPointFromEvent(e);
      if (p.y < playableTop - 6 || p.y > playableBottom + 10) return;
      let tapped = null;
      for (let i = items.length - 1; i >= 0; i--) {
        const item = items[i];
        const dx = p.x - item.x;
        const dy = p.y - item.y;
        if (dx * dx + dy * dy <= item.r * item.r * 1.08) {
          tapped = item;
          break;
        }
      }
      if (!tapped) return;
      if (tapped.value === currentQuestion.answer) handleCorrect(tapped);
      else handleWrong(tapped);
    }

    canvas.addEventListener('pointerdown', handlePointer, { passive: true });
    startBtn.addEventListener('click', resetGame);
    restartBtn.addEventListener('click', resetGame);
    pauseBtn.addEventListener('click', () => {
      if (gameState === 'ready' || gameState === 'over') return;
      togglePause();
    });

    soundBtn.addEventListener('click', () => {
      soundOn = !soundOn;
      soundBtn.textContent = soundOn ? '音ON' : '音OFF';
      if (soundOn) {
        ensureAudio();
        applyVolumeSettings();
        if (gameState === 'running' || gameState === 'paused') startBgmIfNeeded();
      } else {
        applyVolumeSettings();
        stopBgm();
      }
    });

    volumeSlider.value = String(Math.round(volumeLevel * 100));
    volumeSlider.addEventListener('input', (e) => {
      volumeLevel = Number(e.target.value) / 100;
      localStorage.setItem('fruitMathVolume', String(Math.round(volumeLevel * 100)));
      if (masterGain) applyVolumeSettings();
    });

    modeCards.forEach(card => card.addEventListener('click', () => setMode(card.dataset.mode)));

    window.addEventListener('resize', resize);
    window.addEventListener('orientationchange', resize);
    window.visualViewport?.addEventListener('resize', resize);

    resize();
    updateHud();
    bestScoreEl.textContent = bestScore;
    soundBtn.textContent = soundOn ? '音ON' : '音OFF';
    setMode('endless');
    generateQuestion(false);
    requestAnimationFrame((ts) => {
      lastTs = ts;
      requestAnimationFrame(function loop(ts2) {
        const dt = clamp((ts2 - lastTs) / 1000, 0, 0.033);
        lastTs = ts2;
        if (gameState === 'running') update(dt);
        draw();
        requestAnimationFrame(loop);
      });
    });
  </script>
</body>
</html>
