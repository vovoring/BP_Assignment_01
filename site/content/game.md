---
title: "Game"
date: 2026-11-03
draft: false
---

스페이스바(`Space`), 위쪽 방향키(`↑`), 또는 화면 터치/클릭으로 점프하고, 아래쪽 방향키(`↓`)로 숙일 수 있습니다.

<style>
.dino-wrapper {
  margin: 1.5rem 0;
  user-select: none;
  -webkit-user-select: none;
}
.dino-canvas-box {
  position: relative;
  width: 100%;
  max-width: 720px;
  margin: 0 auto;
  background: #3b3e48;
  border: 2px solid rgba(238, 238, 238, 0.2);
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.35);
}
#dino-canvas {
  display: block;
  width: 100%;
  height: auto;
  cursor: pointer;
  touch-action: manipulation;
}
.dino-controls-hint {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 0.8rem;
  margin-top: 0.9rem;
  font-size: 0.85rem;
  color: #cccccc;
  font-family: monospace;
}
.dino-controls-hint span {
  background: rgba(59, 62, 72, 0.9);
  border: 1px solid rgba(238, 238, 238, 0.2);
  border-radius: 5px;
  padding: 0.2rem 0.6rem;
}
.dino-controls-hint b {
  color: #018472;
}
</style>

<div class="dino-wrapper"><div class="dino-canvas-box"><canvas id="dino-canvas" width="720" height="220"></canvas></div><div class="dino-controls-hint"><span><b>Space / ↑ / 클릭</b> 점프</span><span><b>↓</b> 숙이기 · 빠른 착지</span><span><b>ESC</b> 메인으로 나가기</span></div></div>

<script>
(() => {
  const canvas = document.getElementById("dino-canvas");
  if (!canvas) return;
  const ctx = canvas.getContext("2d");

  const WIDTH = canvas.width;
  const HEIGHT = canvas.height;
  const GROUND_Y = 182;

  let gameState = "WAITING";
  let score = 0;
  let highScore = Number(localStorage.getItem("hermit_dino_hi") || 0);
  let speed = 6;
  let frameCount = 0;
  let flashTimer = 0;
  let flashScoreValue = 0;

  const dino = {
    x: 52,
    y: GROUND_Y - 44,
    w: 40,
    h: 44,
    vy: 0,
    gravity: 0.68,
    jumpPower: -12.6,
    grounded: true,
    ducking: false,
    reset() {
      this.h = 44;
      this.w = 40;
      this.y = GROUND_Y - this.h;
      this.vy = 0;
      this.grounded = true;
      this.ducking = false;
    },
    jump() {
      if (this.grounded) {
        this.ducking = false;
        this.h = 44;
        this.w = 40;
        this.vy = this.jumpPower;
        this.grounded = false;
      }
    },
    setDuck(isDuck) {
      if (this.grounded) {
        this.ducking = isDuck;
        this.h = isDuck ? 28 : 44;
        this.w = isDuck ? 52 : 40;
        this.y = GROUND_Y - this.h;
      } else if (isDuck) {
        this.vy += 3.2;
      }
    },
    update() {
      if (!this.grounded) {
        this.vy += this.gravity;
        this.y += this.vy;
        if (this.y >= GROUND_Y - this.h) {
          this.y = GROUND_Y - this.h;
          this.vy = 0;
          this.grounded = true;
        }
      }
    },
    draw() {
      ctx.fillStyle = "#eeeeee";
      const x = Math.round(this.x);
      const y = Math.round(this.y);

      if (this.ducking && this.grounded) {
        ctx.fillRect(x, y + 6, 34, 14);
        ctx.fillRect(x + 30, y + 2, 22, 14);
        ctx.fillStyle = "#3b3e48";
        ctx.fillRect(x + 44, y + 5, 3, 3);
        ctx.fillStyle = "#eeeeee";
        const legOffset = Math.floor(frameCount / 5) % 2 === 0 ? 0 : 4;
        ctx.fillRect(x + 8 + legOffset, y + 20, 5, 8);
        ctx.fillRect(x + 22 - legOffset, y + 20, 5, 8);
        return;
      }

      ctx.fillRect(x + 18, y, 20, 14);
      ctx.fillRect(x + 22, y + 14, 12, 3);
      ctx.fillStyle = "#3b3e48";
      if (gameState === "GAMEOVER") {
        ctx.fillRect(x + 23, y + 3, 5, 5);
      } else {
        ctx.fillRect(x + 24, y + 3, 3, 3);
      }
      ctx.fillStyle = "#eeeeee";
      ctx.fillRect(x + 10, y + 14, 18, 18);
      ctx.fillRect(x + 4, y + 18, 6, 12);
      ctx.fillRect(x, y + 14, 4, 10);
      ctx.fillRect(x + 28, y + 19, 5, 3);
      ctx.fillRect(x + 31, y + 22, 2, 3);

      if (!this.grounded || gameState === "WAITING") {
        ctx.fillRect(x + 11, y + 32, 5, 12);
        ctx.fillRect(x + 21, y + 32, 5, 12);
      } else {
        const step = Math.floor(frameCount / 6) % 2;
        if (step === 0) {
          ctx.fillRect(x + 11, y + 32, 5, 12);
          ctx.fillRect(x + 21, y + 32, 5, 7);
        } else {
          ctx.fillRect(x + 11, y + 32, 5, 7);
          ctx.fillRect(x + 21, y + 32, 5, 12);
        }
      }
    }
  };

  let obstacles = [];
  let clouds = [
    { x: 140, y: 45, speed: 0.7 },
    { x: 390, y: 68, speed: 0.5 },
    { x: 610, y: 36, speed: 0.6 }
  ];
  let groundBumps = Array.from({ length: 28 }, (_, i) => ({
    x: i * 30,
    w: (i % 3 === 0) ? 6 : 2,
    yOffset: (i % 2 === 0) ? 4 : 7
  }));

  function spawnObstacle() {
    const canSpawnBird = score > 180 && Math.random() < 0.28;
    if (canSpawnBird) {
      const birdHeights = [GROUND_Y - 36, GROUND_Y - 62, GROUND_Y - 82];
      const by = birdHeights[Math.floor(Math.random() * birdHeights.length)];
      obstacles.push({ type: "bird", x: WIDTH + 20, y: by, w: 36, h: 24 });
    } else {
      const variants = [
        { w: 16, h: 34 },
        { w: 22, h: 44 },
        { w: 34, h: 36 },
        { w: 46, h: 42 }
      ];
      const v = variants[Math.floor(Math.random() * variants.length)];
      obstacles.push({ type: "cactus", x: WIDTH + 20, y: GROUND_Y - v.h, w: v.w, h: v.h });
    }
  }

  function drawObstacle(obs) {
    const x = Math.round(obs.x);
    const y = Math.round(obs.y);
    if (obs.type === "cactus") {
      ctx.fillStyle = "#018472";
      const count = obs.w > 38 ? 3 : obs.w > 26 ? 2 : 1;
      const singleW = Math.floor(obs.w / count);
      for (let i = 0; i < count; i++) {
        const cx = x + i * singleW;
        ctx.fillRect(cx + Math.floor(singleW * 0.3), y, Math.max(5, Math.floor(singleW * 0.4)), obs.h);
        ctx.fillRect(cx, y + 8, Math.floor(singleW * 0.35), 4);
        ctx.fillRect(cx, y + 3, 4, 9);
        ctx.fillRect(cx + Math.floor(singleW * 0.65), y + 12, Math.floor(singleW * 0.35), 4);
        ctx.fillRect(cx + singleW - 4, y + 6, 4, 10);
      }
    } else {
      ctx.fillStyle = "#eeeeee";
      const wingUp = Math.floor(frameCount / 10) % 2 === 0;
      ctx.fillRect(x, y + 10, 12, 6);
      ctx.fillRect(x + 10, y + 8, 22, 8);
      if (wingUp) {
        ctx.fillRect(x + 14, y, 8, 8);
      } else {
        ctx.fillRect(x + 14, y + 16, 8, 8);
      }
    }
  }

  function drawEnvironment() {
    ctx.fillStyle = "rgba(238, 238, 238, 0.22)";
    clouds.forEach((c) => {
      ctx.fillRect(c.x, c.y, 34, 8);
      ctx.fillRect(c.x + 6, c.y - 5, 20, 5);
    });
    ctx.fillStyle = "rgba(238, 238, 238, 0.45)";
    ctx.fillRect(0, GROUND_Y, WIDTH, 2);
    ctx.fillStyle = "rgba(238, 238, 238, 0.3)";
    groundBumps.forEach((b) => {
      ctx.fillRect(b.x, GROUND_Y + b.yOffset, b.w, 2);
    });
  }

  function drawHUD() {
    ctx.font = "bold 15px monospace";
    ctx.textAlign = "right";
    ctx.fillStyle = "#cccccc";
    const hiText = "HI " + String(Math.floor(highScore)).padStart(5, "0");
    ctx.fillText(hiText, WIDTH - 95, 28);

    if (flashTimer > 0 && Math.floor(flashTimer / 6) % 2 === 0) return;
    ctx.fillStyle = "#eeeeee";
    const displayNum = flashTimer > 0 ? flashScoreValue : Math.floor(score);
    ctx.fillText(String(displayNum).padStart(5, "0"), WIDTH - 20, 28);
  }

  function drawOverlay() {
    ctx.textAlign = "center";
    if (gameState === "WAITING") {
      ctx.fillStyle = "#eeeeee";
      ctx.font = "bold 16px monospace";
      ctx.fillText("PRESS SPACE OR CLICK TO START", WIDTH / 2, HEIGHT / 2 - 8);
    } else if (gameState === "GAMEOVER") {
      ctx.fillStyle = "#eeeeee";
      ctx.font = "bold 20px monospace";
      ctx.fillText("G A M E   O V E R", WIDTH / 2, HEIGHT / 2 - 22);
      ctx.fillStyle = "#018472";
      ctx.fillRect(WIDTH / 2 - 18, HEIGHT / 2 - 8, 36, 32);
      ctx.fillStyle = "#eeeeee";
      ctx.font = "bold 20px monospace";
      ctx.fillText("↻", WIDTH / 2, HEIGHT / 2 + 15);
    }
  }

  function checkCollision(a, b) {
    const padX = 6;
    const padY = 5;
    return (
      a.x + padX < b.x + b.w - padX &&
      a.x + a.w - padX > b.x + padX &&
      a.y + padY < b.y + b.h - padY &&
      a.y + a.h - padY > b.y + padY
    );
  }

  function startOrRestart() {
    score = 0;
    speed = 6;
    frameCount = 0;
    flashTimer = 0;
    obstacles = [];
    dino.reset();
    gameState = "PLAYING";
    dino.jump();
  }

  let nextSpawnGap = 75;

  function update() {
    if (gameState !== "PLAYING") return;

    frameCount++;
    const prevHundred = Math.floor(score / 100);
    score += 0.16;
    const currHundred = Math.floor(score / 100);
    if (currHundred > prevHundred && currHundred > 0) {
      flashTimer = 36;
      flashScoreValue = currHundred * 100;
    }
    if (flashTimer > 0) flashTimer--;

    speed = Math.min(13.5, 6 + score * 0.0045);

    clouds.forEach((c) => {
      c.x -= c.speed;
      if (c.x < -40) c.x = WIDTH + 20;
    });
    groundBumps.forEach((b) => {
      b.x -= speed;
      if (b.x < -10) b.x = WIDTH + Math.random() * 20;
    });

    dino.update();

    const lastObs = obstacles[obstacles.length - 1];
    if (!lastObs || WIDTH - lastObs.x >= nextSpawnGap) {
      spawnObstacle();
      nextSpawnGap = 190 + Math.random() * 210 + speed * 8;
    }

    for (let i = obstacles.length - 1; i >= 0; i--) {
      const obs = obstacles[i];
      obs.x -= speed;
      if (checkCollision(dino, obs)) {
        gameState = "GAMEOVER";
        if (score > highScore) {
          highScore = Math.floor(score);
          localStorage.setItem("hermit_dino_hi", String(highScore));
        }
        break;
      }
      if (obs.x + obs.w < -20) {
        obstacles.splice(i, 1);
      }
    }
  }

  function render() {
    ctx.clearRect(0, 0, WIDTH, HEIGHT);
    drawEnvironment();
    obstacles.forEach(drawObstacle);
    dino.draw();
    drawHUD();
    drawOverlay();
  }

  function loop() {
    update();
    render();
    requestAnimationFrame(loop);
  }

  window.addEventListener("keydown", (e) => {
    if (["Space", "ArrowUp"].includes(e.code)) {
      e.preventDefault();
      if (gameState === "PLAYING") {
        dino.jump();
      } else {
        startOrRestart();
      }
    } else if (e.code === "ArrowDown") {
      if (gameState === "PLAYING") {
        e.preventDefault();
        dino.setDuck(true);
      }
    }
  });

  window.addEventListener("keyup", (e) => {
    if (e.code === "ArrowDown") {
      dino.setDuck(false);
    }
  });

  canvas.addEventListener("pointerdown", (e) => {
    e.preventDefault();
    if (gameState === "PLAYING") {
      dino.jump();
    } else {
      startOrRestart();
    }
  });

  loop();
})();
</script>

