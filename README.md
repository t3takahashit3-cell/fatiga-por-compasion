<!doctype html>
<html lang="ja">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>共感疲労チェックリスト｜かわいいデザイン</title>
  <style>
    :root{
      --bg:#fffafc;
      --card:#ffffff;
      --accent:#ff7eb9;
      --accent-2:#ffd1e8;
      --text:#333;
      --muted:#666;
      --ok:#4caf50;
      --warn:#ff9800;
      --high:#e91e63;
    }
    *{box-sizing:border-box}
    body{
      margin:0; font-family: "Hiragino Sans","Noto Sans JP",system-ui,sans-serif;
      color:var(--text); background:linear-gradient(180deg,var(--bg),#fff);
    }
    header{
      padding:24px; text-align:center;
      background: radial-gradient(circle at top left, var(--accent-2), transparent 60%),
                  radial-gradient(circle at top right, #ffeef7, transparent 60%);
    }
    .title{
      display:inline-flex; align-items:center; gap:12px;
      font-weight:700; font-size:1.4rem; color:var(--accent);
    }
    .mascot{
      width:40px; height:40px; border-radius:50%; background:var(--accent-2);
      display:grid; place-items:center; position:relative; box-shadow:0 4px 12px rgba(0,0,0,.06);
    }
    .mascot::before,.mascot::after{
      content:""; position:absolute; width:8px; height:8px; background:#fff; border-radius:50%;
      top:12px;
    }
    .mascot::before{ left:10px }
    .mascot::after{ right:10px }
    .card{
      max-width:920px; margin:24px auto; background:var(--card); border-radius:16px;
      box-shadow:0 10px 30px rgba(0,0,0,.08); padding:20px;
    }
    .note{ color:var(--muted); font-size:.95rem; line-height:1.7 }
    .grid{
      display:grid; gap:12px; grid-template-columns:1fr;
    }
    @media (min-width:720px){
      .grid{ grid-template-columns:1fr 1fr }
    }
    .q{
      border:1px solid #f4d9e8; border-radius:12px; padding:12px; background:#fffdfd;
    }
    .q p{ margin:0 0 10px; }
    .choices{ display:flex; flex-wrap:wrap; gap:8px }
    .chip{
      position:relative;
      display:inline-flex; align-items:center; gap:8px;
      padding:8px 10px; border-radius:999px; border:1px solid #ffd6e8; background:#fff;
      cursor:pointer; transition:.15s ease;
    }
    .chip:hover{ box-shadow:0 4px 10px rgba(0,0,0,.06) }
    .chip input{ appearance:none; width:16px; height:16px; border:2px solid var(--accent); border-radius:50% }
    .chip input:checked{ background:var(--accent) }
    .actions{
      display:flex; gap:10px; flex-wrap:wrap; align-items:center; justify-content:center; margin:18px 0;
    }
    button{
      appearance:none; border:none; border-radius:999px; padding:12px 18px; font-weight:700;
      background:var(--accent); color:#fff; cursor:pointer; box-shadow:0 8px 20px rgba(233,30,99,.25);
    }
    button.secondary{ background:#fff; color:var(--accent); border:1.5px solid var(--accent-2) }
    .result{
      margin-top:8px; padding:16px; border-radius:12px; background:#fff7fb; border:1px dashed #ffc7e3;
    }
    .badge{
      display:inline-block; padding:6px 10px; border-radius:999px; font-weight:700; color:#fff;
    }
    .badge.ok{ background:var(--ok) }
    .badge.warn{ background:var(--warn) }
    .badge.high{ background:var(--high) }
    footer{
      text-align:center; color:var(--muted); font-size:.9rem; padding:24px 12px 40px;
    }
    a{ color:var(--accent) }
  </style>
</head>
<body>
  <header>
    <div class="title">
      <div class="mascot" aria-hidden="true">·</div>
      共感疲労チェックリスト
    </div>
    <p class="note">これは自己チェック用の簡易ツールです。体調や心の不調が続く場合は、専門家に相談してください。</p>
  </header>

  <main class="card" id="app">
    <section>
      <p class="note">
        各設問を「全くない / 時々ある / よくある / 常にある」から選んでください。計算ボタンで総合スコアとリスク目安が表示されます（0–19低、20–38注意、39–57高）。参考基準。
      </p>
    </section>

    <section class="grid" id="questions"></section>

    <div class="actions">
      <button id="calc">計算する</button>
      <button class="secondary" id="reset">リセット</button>
    </div>

    <section class="result" id="result" hidden>
      <h3 style="margin-top:0;">結果</h3>
      <p><strong>総合スコア：</strong><span id="score">0</span> 点</p>
      <p><strong>リスク目安：</strong><span id="risk"></span> <span id="badge" class="badge"></span></p>
      <p class="note" id="advice"></p>
    </section>
  </main>

  <footer>
    <p>共感疲労の高さは、医療機関で働く人にとって向いているとも言えます。</p>
    <p>参考: でもその共感疲労の高さがストレスにならないよう、セルフケアをしてきましょう！</p>
  </footer>

  <script>
    // 設問（20項目）
    const ITEMS = [
      "他人の感情に影響されやすく、自分の感情が振り回されることが多い",
      "他人の問題に深く共感しすぎて、自分自身の問題を後回しにしてしまう",
      "他人の不幸な話を聞くと、気分が落ち込み、やる気がなくなる",
      "他人の期待に応えようとしすぎて、自分の気持ちを押し殺してしまう",
      "他人の感情に同調しすぎて、自分の感情がわからなくなることがある",
      "他人の問題を解決しようと努力するあまり、自分が疲弊してしまう",
      "他人の感情に共感しすぎて、孤独感や無力感を感じる",
      "他人の悩みを聞くと、自分の過去の経験と重ね合わせてしまい、辛い気持ちになる",
      "他人の感情に振り回されて、自分の感情が安定しない",
      "他人の不幸なニュースを見ると、心が痛む",
      "他人の感情に共感しすぎて、日常生活に支障が出る",
      "他人の感情に同調しすぎて、自分の感情が麻痺してしまうことがある",
      "他人の感情に過度に責任を感じ、自分を責めてしまう",
      "他人の感情に影響されすぎて、人間関係で上手くいかないことがある",
      "他人の感情に共感しすぎて、心身ともに疲れている",
      "他人の幸せを心から喜べないことがある",
      "他人の感情に振り回されて、自分の時間が持てない",
      "他人の感情に共感しすぎて、パニックになることがある",
      "他人の感情に影響されすぎて、睡眠の質が低下している",
      "他人の感情に共感しすぎて、体調を崩しやすい",
    ];

    const CHOICES = [
      {label:"全くない", value:0},
      {label:"時々ある", value:1},
      {label:"よくある", value:2},
      {label:"常にある", value:3},
    ];

    const container = document.getElementById("questions");

    ITEMS.forEach((text, idx) => {
      const q = document.createElement("div");
      q.className = "q";
      q.innerHTML = `<p><strong>Q${idx+1}：</strong>${text}</p>`;
      const choices = document.createElement("div");
      choices.className = "choices";

      CHOICES.forEach(c => {
        const id = `q${idx}_v${c.value}`;
        const label = document.createElement("label");
        label.className = "chip";
        label.setAttribute("for", id);
        label.innerHTML = `<input type="radio" name="q${idx}" id="${id}" value="${c.value}" /> ${c.label}`;
        choices.appendChild(label);
      });

      q.appendChild(choices);
      container.appendChild(q);
    });

    const calcBtn = document.getElementById("calc");
    const resetBtn = document.getElementById("reset");
    const resultEl = document.getElementById("result");
    const scoreEl = document.getElementById("score");
    const riskEl = document.getElementById("risk");
    const badgeEl = document.getElementById("badge");
    const adviceEl = document.getElementById("advice");

    function computeScore(){
      let score = 0;
      let unanswered = [];
      ITEMS.forEach((_, idx) => {
        const checked = document.querySelector(`input[name="q${idx}"]:checked`);
        if(!checked){ unanswered.push(idx+1); return; }
        score += Number(checked.value);
      });
      return {score, unanswered};
    }

    function interpret(score){
      if(score <= 19) return {label:"低リスク", class:"ok",
        advice:"今の調子を保てるよう、休息や趣味の時間を大切に。気になる変化が続けば相談を検討してください。"};
      if(score <= 38) return {label:"注意が必要", class:"warn",
        advice:"疲れがたまる前に境界線を整え、休息を増やして。必要なら信頼できる人や専門家へ相談を。"};
      return {label:"高リスク", class:"high",
        advice:"無理をせず休息とサポートを優先に。早めの専門家相談を検討し、負担の調整や環境の工夫を行ってください。"};
    }

    calcBtn.addEventListener("click", () => {
      const {score, unanswered} = computeScore();
      if(unanswered.length){
        alert(`未回答があります（Q${unanswered.join(", ")}）。全て回答してください。`);
        return;
      }
      const {label, class:klass, advice} = interpret(score);
      scoreEl.textContent = score;
      riskEl.textContent = label;
      badgeEl.textContent = label;
      badgeEl.className = `badge ${klass}`;
      adviceEl.textContent = advice;
      resultEl.hidden = false;
      resultEl.scrollIntoView({behavior:"smooth"});
    });

    resetBtn.addEventListener("click", () => {
      document.querySelectorAll('input[type="radio"]').forEach(r => r.checked = false);
      resultEl.hidden = true;
      scoreEl.textContent = "0";
      riskEl.textContent = "";
      badgeEl.textContent = "";
      adviceEl.textContent = "";
      badgeEl.className = "badge";
      window.scrollTo({top:0, behavior:"smooth"});
    });
  </script>
</body>
</html>
