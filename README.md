<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Alien Moon Dodger</title>

<style>
body {
  margin: 0;
  background: radial-gradient(circle at top, #001d3d, #000814);
  color: #00ffea;
  text-align: center;
  font-family: Impact, Arial Black, sans-serif;
}

h1 {
  font-size: 48px;
  margin-top: 10px;
  text-shadow: 0 0 15px #00fff2;
}

button {
  padding: 12px 24px;
  font-size: 20px;
  margin: 10px;
  cursor: pointer;
  background: #00ffea;
  border: none;
  border-radius: 8px;
}

#score {
  font-size: 28px;
  color: #ffea00;
  text-shadow: 0 0 10px #ffea00;
}

canvas {
  display: block;
  margin: 20px auto;
  border: 3px solid #00ffea;
  background: black;
}
</style>
</head>
<body>

<h1>ALIEN MOON DODGER</h1>
<button onclick="startGame()">START GAME</button>
<p>Use LEFT and RIGHT arrow keys</p>
<p>Score: <span id="score">0</span></p>

<canvas id="gameCanvas" width="800" height="500"></canvas>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

let alien;
let asteroids;
let stars;
let score;
let gameRunning;

function createStars() {
  stars = [];
  for (let i = 0; i < 100; i++) {
    stars.push({
      x: Math.random() * canvas.width,
      y: Math.random() * canvas.height,
      size: Math.random() * 2
    });
  }
}

function startGame() {
  alien = {
    x: 375,
    y: 420,
    width: 50,
    height: 60,
    speed: 8
  };

  asteroids = [];
  score = 0;
  gameRunning = true;
  document.getElementById("score").textContent = score;
  createStars();

  requestAnimationFrame(gameLoop);
}

document.addEventListener("keydown", function(e) {
  if (!gameRunning) return;

  if (e.key === "ArrowLeft") alien.x -= alien.speed * 4;
  if (e.key === "ArrowRight") alien.x += alien.speed * 4;

  if (alien.x < 0) alien.x = 0;
  if (alien.x + alien.width > canvas.width)
    alien.x = canvas.width - alien.width;
});

function spawnAsteroid() {
  let size = 30 + Math.random() * 40;
  asteroids.push({
    x: Math.random() * (canvas.width - size),
    y: -size,
    size: size,
    speed: 2 + Math.random() * 3,
    rotation: Math.random() * 360
  });
}

function drawAlien() {
  // Body
  ctx.fillStyle = "#00ff88";
  ctx.beginPath();
  ctx.ellipse(alien.x + 25, alien.y + 35, 20, 25, 0, 0, Math.PI * 2);
  ctx.fill();

  // Head
  ctx.beginPath();
  ctx.ellipse(alien.x + 25, alien.y + 15, 18, 15, 0, 0, Math.PI * 2);
  ctx.fill();

  // Eyes
  ctx.fillStyle = "black";
  ctx.beginPath();
  ctx.ellipse(alien.x + 18, alien.y + 12, 5, 8, 0, 0, Math.PI * 2);
  ctx.fill();
  ctx.beginPath();
  ctx.ellipse(alien.x + 32, alien.y + 12, 5, 8, 0, 0, Math.PI * 2);
  ctx.fill();
}

function drawMoon() {
  ctx.fillStyle = "#888";
  ctx.beginPath();
  ctx.arc(canvas.width/2, 550, 300, 0, Math.PI * 2);
  ctx.fill();
}

function drawStars() {
  ctx.fillStyle = "white";
  stars.forEach(star => {
    ctx.fillRect(star.x, star.y, star.size, star.size);
  });
}

function drawAsteroid(a) {
  ctx.save();
  ctx.translate(a.x + a.size/2, a.y + a.size/2);
  ctx.rotate(a.rotation);
  ctx.fillStyle = "#6b4f3b";
  ctx.beginPath();
  ctx.arc(0, 0, a.size/2, 0, Math.PI * 2);
  ctx.fill();
  ctx.restore();
}

function gameLoop() {
  if (!gameRunning) return;

  ctx.clearRect(0, 0, canvas.width, canvas.height);

  drawStars();
  drawMoon();
  drawAlien();

  if (Math.random() < 0.03) spawnAsteroid();

  for (let i = asteroids.length - 1; i >= 0; i--) {
    let a = asteroids[i];
    a.y += a.speed;
    a.rotation += 0.02;
    drawAsteroid(a);

    if (
      a.y + a.size > alien.y &&
      a.x < alien.x + alien.width &&
      a.x + a.size > alien.x
    ) {
      alert("Game Over! Final Score: " + score);
      gameRunning = false;
      return;
    }

    if (a.y > canvas.height) {
      asteroids.splice(i, 1);
      score++;
      document.getElementById("score").textContent = score;
    }
  }

  requestAnimationFrame(gameLoop);
}
</script>

</body>
</html>
