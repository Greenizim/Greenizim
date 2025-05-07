<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <title>Asker vs Bloklar</title>
  <style>
    body {
      margin: 0;
      background: #111;
      overflow: hidden;
      color: #fff;
      font-family: sans-serif;
    }
    canvas {
      display: block;
      background: #222;
      margin: auto;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="800" height="500"></canvas>
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    const soldier = {
      x: canvas.width / 2 - 20,
      y: canvas.height - 40,
      width: 40,
      height: 40,
      speed: 7,
      color: "green"
    };

    let bullets = [];
    let blocks = [];

    function drawSoldier() {
      ctx.fillStyle = soldier.color;
      ctx.fillRect(soldier.x, soldier.y, soldier.width, soldier.height);
    }

    function drawBullets() {
      ctx.fillStyle = "red";
      bullets.forEach(b => {
        ctx.fillRect(b.x, b.y, b.width, b.height);
      });
    }

    function drawBlocks() {
      ctx.fillStyle = "gray";
      blocks.forEach(block => {
        ctx.fillRect(block.x, block.y, block.width, block.height);
      });
    }

    function updateBullets() {
      bullets = bullets.filter(b => b.y > 0);
      bullets.forEach(b => {
        b.y -= b.speed;
      });
    }

    function updateBlocks() {
      blocks.forEach(b => b.y += b.speed);

      // Çarpışma kontrolü
      bullets.forEach((bullet, bIndex) => {
        blocks.forEach((block, blockIndex) => {
          if (
            bullet.x < block.x + block.width &&
            bullet.x + bullet.width > block.x &&
            bullet.y < block.y + block.height &&
            bullet.y + bullet.height > block.y
          ) {
            // Blok ve mermi yok edilir
            bullets.splice(bIndex, 1);
            blocks.splice(blockIndex, 1);
          }
        });
      });
    }

    function spawnBlock() {
      const x = Math.random() * (canvas.width - 40);
      blocks.push({
        x: x,
        y: -40,
        width: 40,
        height: 40,
        speed: 2
      });
    }

    let keys = {};
    document.addEventListener("keydown", e => {
      keys[e.key] = true;
      if (e.key === " ") {
        bullets.push({
          x: soldier.x + soldier.width / 2 - 5,
          y: soldier.y,
          width: 10,
          height: 20,
          speed: 7
        });
      }
    });
    document.addEventListener("keyup", e => {
      keys[e.key] = false;
    });

    function moveSoldier() {
      if (keys["a"] || keys["ArrowLeft"]) {
        soldier.x -= soldier.speed;
      }
      if (keys["d"] || keys["ArrowRight"]) {
        soldier.x += soldier.speed;
      }
      soldier.x = Math.max(0, Math.min(canvas.width - soldier.width, soldier.x));
    }

    function gameLoop() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      moveSoldier();
      updateBullets();
      updateBlocks();
      drawSoldier();
      drawBullets();
      drawBlocks();
      requestAnimationFrame(gameLoop);
    }

    setInterval(spawnBlock, 1000);
    gameLoop();
  </script>
</body>
</html>
