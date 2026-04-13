<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>True Soul Breaker</title>

<style>
body {
  margin: 0;
  background: black;
  overflow: hidden;
  touch-action: none;
  font-family: Arial;
}

canvas {
  display: block;
  background: #000;
}

#ui {
  position: fixed;
  top: 10px;
  left: 10px;
  color: white;
  font-size: 18px;
}

#mobileControls {
  position: fixed;
  bottom: 20px;
  width: 100%;
  text-align: center;
}

button {
  font-size: 24px;
  margin: 5px;
  padding: 15px;
  background: white;
  border: none;
  border-radius: 10px;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="ui">HP: 100</div>

<div id="mobileControls">
  <button ontouchstart="move('up')">▲</button><br>
  <button ontouchstart="move('left')">◀</button>
  <button ontouchstart="move('right')">▶</button><br>
  <button ontouchstart="move('down')">▼</button>
</div>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

let player = { x: 200, y: 200, size: 12, hp: 100 };
let bullets = [];

// PLAYER MOVEMENT (keyboard)
document.addEventListener("keydown", e => {
  if(e.key === "ArrowUp") player.y -= 10;
  if(e.key === "ArrowDown") player.y += 10;
  if(e.key === "ArrowLeft") player.x -= 10;
  if(e.key === "ArrowRight") player.x += 10;
});

// MOBILE MOVEMENT
function move(dir) {
  if(dir === "up") player.y -= 15;
  if(dir === "down") player.y += 15;
  if(dir === "left") player.x -= 15;
  if(dir === "right") player.x += 15;
}

// BULLET SYSTEM
function spawnBullet(x, y, vx, vy) {
  bullets.push({ x, y, vx, vy });
}

// RANDOM ATTACKS
setInterval(() => {
  let angle = Math.random() * Math.PI * 2;

  spawnBullet(
    canvas.width / 2,
    canvas.height / 2,
    Math.cos(angle) * 3,
    Math.sin(angle) * 3
  );
}, 400);

// COLLISION
function checkHit(b) {
  return (
    player.x < b.x + 5 &&
    player.x + player.size > b.x &&
    player.y < b.y + 5 &&
    player.y + player.size > b.y
  );
}

// GAME LOOP
function update() {
  ctx.clearRect(0,0,canvas.width,canvas.height);

  // PLAYER
  ctx.fillStyle = "red";
  ctx.fillRect(player.x, player.y, player.size, player.size);

  // BULLETS
  ctx.fillStyle = "white";

  bullets.forEach(b => {
    b.x += b.vx;
    b.y += b.vy;

    ctx.fillRect(b.x, b.y, 6, 6);

    if(checkHit(b)) {
      player.hp -= 1;
      document.getElementById("ui").innerText = "HP: " + player.hp;
    }
  });

  // REMOVE OFFSCREEN BULLETS
  bullets = bullets.filter(b =>
    b.x > -50 && b.x < canvas.width + 50 &&
    b.y > -50 && b.y < canvas.height + 50
  );

  requestAnimationFrame(update);
}

update();
</script>

</body>
</html>
