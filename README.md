<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Birlikte Geçen Zaman</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #b2f2bb, #d0bfff); /* Yeşil & Mor */
      color: #3d004d;
      display: flex;
      flex-direction: row;
      padding: 30px;
      min-height: 100vh;
      overflow-x: hidden;
    }

    .left {
      flex: 2;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      text-align: center;
    }

    h1 {
      font-size: 2.5em;
      margin-bottom: 10px;
      color: #2e004d;
    }

    .heart {
      font-size: 2em;
      animation: pulse 1.2s infinite;
      color: #a020f0;
    }

    @keyframes pulse {
      0% { transform: scale(1); }
      50% { transform: scale(1.2); }
      100% { transform: scale(1); }
    }

    #counter {
      margin-top: 30px;
      display: flex;
      flex-direction: column;
      gap: 20px;
      font-size: 1.5em;
      width: 100%;
      max-width: 320px;
    }

    .time-box {
      background: #e6ffe6;
      border: 3px solid #adf7b6;
      border-radius: 15px;
      padding: 15px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    }

    .right {
      flex: 1;
      padding-left: 40px;
      display: flex;
      flex-direction: column;
      justify-content: center;
    }

    .password-box {
      background: #f3e6ff;
      padding: 25px;
      border-radius: 15px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      border: 2px solid #daafff;
    }

    .password-box p {
      font-size: 1.3em;
      margin-bottom: 10px;
      color: #550055;
    }

    input[type="password"] {
      padding: 12px;
      width: 100%;
      border: 2px solid #999;
      border-radius: 10px;
      font-size: 1em;
    }

    .hidden-message {
      margin-top: 20px;
      font-size: 1.4em;
      color: #006600;
      display: none;
      font-weight: bold;
    }

    @media (max-width: 768px) {
      body {
        flex-direction: column;
        padding: 20px;
      }

      .right {
        padding-left: 0;
        margin-top: 30px;
      }
    }

    canvas {
      position: fixed;
      top: 0;
      left: 0;
      pointer-events: none;
      z-index: 9999;
    }
  </style>
</head>
<body>
  <canvas id="confettiCanvas"></canvas>

  <div class="left">
    <h1>💑 Birlikte Geçen Süre</h1>
    <div class="heart">💓</div>

    <div id="counter">
      <div class="time-box" id="days"></div>
      <div class="time-box" id="minutes"></div>
      <div class="time-box" id="seconds"></div>
    </div>
  </div>

  <div class="right">
    <div class="password-box">
      <p>🔐 Kalbimize girmek için şifreyi gir</p>
      <input type="password" id="passwordInput" placeholder="Şifreyi yaz..." />
      <div class="hidden-message" id="successMessage">💖 Kalbimize hoş geldin! 💖</div>
    </div>
  </div>

  <script>
    const startDate = new Date(2024, 0, 14, 2, 51, 0);

    function updateCounter() {
      const now = new Date();
      const diff = now - startDate;

      const totalSeconds = Math.floor(diff / 1000);
      const totalMinutes = Math.floor(totalSeconds / 60);
      const totalDays = Math.floor(totalSeconds / (60 * 60 * 24));

      document.getElementById("days").innerHTML = `🗓️ <strong>${totalDays}</strong> gün geçti`;
      document.getElementById("minutes").innerHTML = `⏱️ <strong>${totalMinutes}</strong> dakika geçti`;
      document.getElementById("seconds").innerHTML = `⏳ <strong>${totalSeconds}</strong> saniye geçti`;
    }

    updateCounter();
    setInterval(updateCounter, 1000);

    // Şifre kontrol
    const passwordInput = document.getElementById("passwordInput");
    const successMessage = document.getElementById("successMessage");

    let confettiStarted = false;

    passwordInput.addEventListener("input", function() {
      if (passwordInput.value === "14.01.2024") {
        successMessage.style.display = "block";
        if (!confettiStarted) {
          startConfetti();
          confettiStarted = true;
        }
      } else {
        successMessage.style.display = "none";
      }
    });

    // Konfeti Animasyonu (Kalpli, Yeşil-Mor)
    const canvas = document.getElementById("confettiCanvas");
    const ctx = canvas.getContext("2d");

    function resizeCanvas() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    window.addEventListener('resize', resizeCanvas);
    resizeCanvas();

    let hearts = [];

    function createHeart() {
      return {
        x: Math.random() * canvas.width,
        y: -20,
        size: Math.random() * 20 + 10,
        speed: Math.random() * 3 + 1,
        color: Math.random() > 0.5 ? '#8aff80' : '#d580ff',
        opacity: Math.random() * 0.5 + 0.5
      };
    }

    function startConfetti() {
      for (let i = 0; i < 150; i++) {
        hearts.push(createHeart());
      }

      animateHearts();
    }

    function animateHearts() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      hearts.forEach((heart, index) => {
        ctx.globalAlpha = heart.opacity;
        ctx.fillStyle = heart.color;
        drawHeart(ctx, heart.x, heart.y, heart.size);
        heart.y += heart.speed;

        if (heart.y > canvas.height) {
          hearts[index] = createHeart();
          hearts[index].y = -20;
        }
      });

      requestAnimationFrame(animateHearts);
    }

    function drawHeart(ctx, x, y, size) {
      ctx.beginPath();
      const topCurveHeight = size * 0.3;
      ctx.moveTo(x, y + topCurveHeight);
      ctx.bezierCurveTo(
        x, y,
        x - size / 2, y,
        x - size / 2, y + topCurveHeight
      );
      ctx.bezierCurveTo(
        x - size / 2, y + (size + topCurveHeight) / 2,
        x, y + (size + topCurveHeight) / 1.1,
        x, y + size
      );
      ctx.bezierCurveTo(
        x, y + (size + topCurveHeight) / 1.1,
        x + size / 2, y + (size + topCurveHeight) / 2,
        x + size / 2, y + topCurveHeight
      );
      ctx.bezierCurveTo(
        x + size / 2, y,
        x, y,
        x, y + topCurveHeight
      );
      ctx.closePath();
      ctx.fill();
    }
  </script>
</body>
</html>
