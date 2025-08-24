<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>Slot Machine 3x3 - Mobile</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      background: #222;
      color: white;
      font-family: sans-serif;
      overflow-x: hidden;
      padding: 10px;
    }

    .slot-container {
      display: flex;
      gap: 20px;
      margin-bottom: 20px;
      align-items: flex-start;
      flex-wrap: wrap;
      justify-content: center;
    }

    .reel-column {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
      width: 25vw;
      max-width: 100px;
    }

    .reel {
      width: 100%;
      height: 100px;
      border: 3px solid #fff;
      border-radius: 10px;
      background: black;
      font-size: 8vw;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: background 0.3s, transform 0.3s;
      user-select: none;
    }

    .stop-btn, #startBtn {
      padding: 12px 20px;
      font-size: 18px;
      border-radius: 12px;
      color: white;
      border: none;
      cursor: pointer;
      user-select: none;
    }

    .stop-btn { background: blue; }
    #startBtn { background: green; margin-bottom: 10px; }

    .buttons-wrapper {
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      align-items: center;
      margin-bottom: 10px;
      width: 100%;
    }

    /* 当たりアニメーション */
    @keyframes flash {
      0%, 100% { background: gold; }
      50% { background: orange; }
    }

    .win-flash { animation: flash 0.5s infinite; }

    @keyframes pop {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.5); }
    }

    .win-pop { animation: pop 0.5s ease-in-out 3; }

    /* 花火用 */
    .firework {
      position: fixed;
      font-size: 20px;
      pointer-events: none;
      z-index: 1000;
      user-select: none;
    }

    @media (max-width: 600px) {
      .slot-container {
        flex-direction: column;
      }
      .reel-column { width: 60vw; max-width: 120px; }
      .reel { font-size: 12vw; height: 120px; }
    }
  </style>
</head>
<body>

  <div class="buttons-wrapper">
    <button id="startBtn">START</button>
  </div>

  <div class="slot-container">
    <div class="reel-column">
      <div class="reel" id="reel0_0">❔</div>
      <div class="reel" id="reel0_1">❔</div>
      <div class="reel" id="reel0_2">❔</div>
      <button class="stop-btn" id="stop0">Stop</button>
    </div>

    <div class="reel-column">
      <div class="reel" id="reel1_0">❔</div>
      <div class="reel" id="reel1_1">❔</div>
      <div class="reel" id="reel1_2">❔</div>
      <button class="stop-btn" id="stop1">Stop</button>
    </div>

    <div class="reel-column">
      <div class="reel" id="reel2_0">❔</div>
      <div class="reel" id="reel2_1">❔</div>
      <div class="reel" id="reel2_2">❔</div>
      <button class="stop-btn" id="stop2">Stop</button>
    </div>
  </div>

  <script>
    const symbols = ["🍒","🍋","⭐","🔔","7️⃣"];
    const reels = [
      [document.getElementById("reel0_0"), document.getElementById("reel0_1"), document.getElementById("reel0_2")],
      [document.getElementById("reel1_0"), document.getElementById("reel1_1"), document.getElementById("reel1_2")],
      [document.getElementById("reel2_0"), document.getElementById("reel2_1"), document.getElementById("reel2_2")]
    ];

    const intervals = [null, null, null];
    let spinning = false;

    function startReels() {
      if (spinning) return;
      spinning = true;

      reels.flat().forEach(reel => {
        reel.style.background = "black";
        reel.classList.remove("win-flash","win-pop");
      });

      reels.forEach((reelColumn, i) => {
        intervals[i] = setInterval(() => {
          reelColumn.forEach(reel => {
            reel.textContent = symbols[Math.floor(Math.random() * symbols.length)];
          });
        }, 125);
      });
    }

    function firework() {
      const fireworks = ["🎉","✨","💥","⭐","🍀"];
      for(let i=0;i<30;i++){
        const span = document.createElement("span");
        span.textContent = fireworks[Math.floor(Math.random()*fireworks.length)];
        span.className = "firework";
        span.style.left = Math.random()*100 + "vw";
        span.style.top = Math.random()*100 + "vh";
        span.style.fontSize = Math.random()*30 + 20 + "px";
        document.body.appendChild(span);
        setTimeout(() => span.remove(), 1000);
      }
    }

    function checkWin() {
      const result = reels.map(col => col.map(r => r.textContent));
      let win = false;

      // 横揃い
      for (let row = 0; row < 3; row++) {
        if (result[0][row] === result[1][row] && result[1][row] === result[2][row]) {
          win = true;
          reels.forEach(col => col[row].classList.add("win-flash","win-pop"));
        }
      }

      // 縦揃い
      for (let col = 0; col < 3; col++) {
        if (result[col][0] === result[col][1] && result[col][1] === result[col][2]) {
          win = true;
          reels[col].forEach(r => r.classList.add("win-flash","win-pop"));
        }
      }

      // 斜め揃い
      if (result[0][0] === result[1][1] && result[1][1] === result[2][2]) {
        win = true;
        [reels[0][0], reels[1][1], reels[2][2]].forEach(r => r.classList.add("win-flash","win-pop"));
      }
      if (result[0][2] === result[1][1] && result[1][1] === result[2][0]) {
        win = true;
        [reels[0][2], reels[1][1], reels[2][0]].forEach(r => r.classList.add("win-flash","win-pop"));
      }

      if(win){
        firework();
        setTimeout(()=>alert("🎉 当たり！ 🎉"), 200);
      }
    }

    document.getElementById("startBtn").addEventListener("click", startReels);

    for (let i = 0; i < 3; i++) {
      document.getElementById("stop" + i).addEventListener("click", () => {
        if (!spinning || intervals[i] === null) return;
        clearInterval(intervals[i]);
        intervals[i] = null;
        reels[i].forEach(reel => {
          reel.textContent = symbols[Math.floor(Math.random() * symbols.length)];
        });

        if (intervals.every(iv => iv === null)) {
          spinning = false;
          checkWin();
        }
      });
    }
  </script>

</body>
</html>
