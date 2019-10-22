<template>
  <div class="home">
    <canvas id="canvas"></canvas>
  </div>
</template>

<script>
// @ is an alias to /src
const width = window.innerWidth - 100;
const height = window.innerHeight - 200;
const blockSize = {
  x: 40,
  y: 15
};
export default {
  data() {
    return {
      ctx: null,
      rect: {
        x: width / 2,
        y: height - 10
      },
      ball: {
        x: 200.5,
        y: 200.5,
        radius: 8
      },
      blocks: []
    };
  },

  mounted() {
    this.init();
  },

  methods: {
    init() {
      this.ctx = document.getElementById("canvas").getContext("2d");
      this.ctx.imageSmoothingEnabled = false;
      this.ctx.canvas.width = width;
      this.ctx.canvas.height = height;
      this.ctx.clearRect(0, 0, 300, 300);
      this.ctx.fillRect(this.rect.x, this.rect.y, 70, 10);
      this.initBlocks();
      this.initBall();
      window.addEventListener("keydown", e => {
        switch (e.key) {
          case "ArrowLeft":
            window.requestAnimationFrame(() => this.moveRect(-10));
            break;
          case "ArrowRight":
            window.requestAnimationFrame(() => this.moveRect(10));
            break;
        }
      });
    },
    moveRect(x) {
      this.ctx.save();
      console.log(x);
      this.ctx.clearRect(this.rect.x, this.rect.y, 70, 10);
      this.rect.x += x;
      this.ctx.fillRect(this.rect.x, this.rect.y, 70, 10);
      this.ctx.restore();
    },
    initBlocks() {
      this.ctx.fillStyle = "#0033cc";
      for (let i = 0; i < width - blockSize.x; i += blockSize.x + 10) {
        for (let j = 1; j < 4; j++) {
          this.ctx.fillRect(10 + i, 10 + j * 40, blockSize.x, blockSize.y);
          this.blocks.push({
            x: 10 + i,
            y: 10 + j * 20,
            width: blockSize.x,
            height: blockSize.y
          });
        }
      }
    },
    initBall() {
      this.ctx.beginPath();
      this.ctx.fillStyle = "#000000";
      this.ctx.arc(this.ball.x, this.ball.y, this.ball.radius, 0, Math.PI * 2);
      //this.ctx.fill();
      this.ctx.stroke();
      window.requestAnimationFrame(() => {
        this.moveBall();
      });
    },
    moveBall() {
      this.ctx.clearRect(
        this.ball.x - this.ball.radius * 2,
        this.ball.y - this.ball.radius * 2,
        this.ball.radius * 3,
        this.ball.radius * 3
      );
      this.ball.x += 2;
      this.ball.y += 2;
      this.ctx.beginPath();
      this.ctx.arc(this.ball.x, this.ball.y, this.ball.radius, 0, Math.PI * 2);
      this.ctx.fill();
      this.ctx.stroke();
      window.requestAnimationFrame(() => {
        this.moveBall();
      });
    }
  }
};
</script>
<style>
canvas {
  border: 1px solid black;
}
</style>
