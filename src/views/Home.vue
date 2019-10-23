<template>
  <div class="home">
    <p>{{ hit }}</p>
    <canvas id="canvas"></canvas>
  </div>
</template>

<script>
// @ is an alias to /src
const width = window.innerWidth - 100;
const height = window.innerHeight - 200;
const velocity = 6;
var move = null;
var ball = null;
const blockSize = {
  x: 40,
  y: 15
};
export default {
  data() {
    return {
      hit: 0,
      ctx: null,
      rect: {
        x: width / 2,
        y: height - 10,
        rectWidth: 70,
        rectHeight: 10,
        speed: 0,
        moving: false
      },
      ball: {
        x: 300,
        y: 300,
        radius: 8,
        velocity: {
          x: 3,
          y: 3
        }
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
      /*window.addEventListener("keyup", e => {
        if (e.key == "ArrowLeft" || e.key == "ArrowRight") {
          cancelAnimationFrame(move);
        }
      });*/
      window.addEventListener("keydown", e => {
        switch (e.key) {
          case "ArrowLeft":
            if (move !== null) {
              cancelAnimationFrame(move);
            }
            window.requestAnimationFrame(() => this.moveRect(-velocity));
            break;
          case "ArrowRight":
            if (move !== null) {
              cancelAnimationFrame(move);
            }
            window.requestAnimationFrame(() => this.moveRect(velocity));
            break;
        }
      });
    },

    //----- RECT CONTROL-----//
    moveRect(x) {
      this.rect.speed = x;
      this.ctx.clearRect(
        this.rect.x,
        this.rect.y,
        this.rect.rectWidth,
        this.rect.rectHeight
      );
      if (this.rect.x >= width) {
        this.rect.x = 0;
      } else if (this.rect.x < -this.rect.rectWidth) {
        this.rect.x = width - this.rect.rectWidth;
      } else {
        this.rect.x += 1 * this.rect.speed;
      }
      this.rect.x += 1 * this.rect.speed;
      this.ctx.fillRect(
        this.rect.x,
        this.rect.y,
        this.rect.rectWidth,
        this.rect.rectHeight
      );
      move = window.requestAnimationFrame(() => {
        this.moveRect(x);
      });
    },
    //-----BALL CONTROL-----//
    moveBall() {
      //this.ctx.clearRect(0,0,width,height)
      //if(this.ball.y + )
      this.ctx.clearRect(
        this.ball.x - this.ball.radius * 2,
        this.ball.y - this.ball.radius * 2,
        this.ball.radius * Math.PI,
        this.ball.radius * Math.PI
      );
      this.blockCollision();
      if (this.playerCollision(this.ball)) {
        this.ball.velocity.y = -this.ball.velocity.y;
      }
      if (this.ball.x >= width - this.ball.radius) {
        this.ball.velocity.x = -this.ball.velocity.x;
      }
      if (this.ball.x <= 0) {
        this.ball.velocity.x = -this.ball.velocity.x;
      }
      if (this.ball.y <= 0 + this.ball.radius) {
        this.ball.velocity.y = -this.ball.velocity.y;
      }
      if (this.ball.y > height) {
        cancelAnimationFrame(ball);
        //this.collided = "you lost";
      }
      this.ball.x += 1 * this.ball.velocity.x;
      this.ball.y += 1 * this.ball.velocity.y;

      this.ctx.beginPath();
      this.ctx.arc(this.ball.x, this.ball.y, this.ball.radius, 0, Math.PI * 2);
      this.ctx.fill();
      this.ctx.stroke();
      ball = window.requestAnimationFrame(() => {
        this.moveBall();
      });
    },

    //-----INITIALIZATIONS-----//
    initBlocks() {
      for (let i = 0; i < width - blockSize.x; i += blockSize.x + 10) {
        for (let j = 1; j < 4; j++) {
          this.ctx.fillRect(10 + i, 10 + j * 40, blockSize.x, blockSize.y);
          this.blocks.push({
            x: 10 + i,
            y: 10 + j * 40,
            width: blockSize.x,
            height: blockSize.y,
            status: 1
          });
        }
      }
    },
    initBall() {
      this.ctx.fillStyle = "#000000";
      this.ctx.arc(this.ball.x, this.ball.y, this.ball.radius, 0, Math.PI * 2);
      //this.ctx.fill();
      this.ctx.stroke();
      window.requestAnimationFrame(() => {
        this.moveBall();
      });
    },
    //-----COLLISIONS-----//
    playerCollision(obj) {
      if (obj.y >= this.rect.y - this.rect.rectHeight && obj.y < this.rect.y) {
        if (
          obj.x >= this.rect.x &&
          obj.x <= this.rect.x + this.rect.rectWidth
        ) {
          return true;
        }
      } else {
        return false;
      }
    },
    blockCollision() {
      for (let i = this.blocks.length - 1; i >= 0; i--) {
        let block = this.blocks[i];
        if (block.status == 1) {
          if (this.ball.x-this.ball.radius > block.x && this.ball.x+this.ball.radius < block.x + block.width) {
            if (
              this.ball.y + this.ball.radius >= block.y &&
              this.ball.y - this.ball.radius <= block.y + block.height
            ) {
              this.hit++;
              block.status = 0;
              this.ball.velocity.y = -this.ball.velocity.y;
              this.ctx.clearRect(block.x, block.y, block.width, block.height);
            }
          }
          
        }
      }
    }
  }
};
</script>
<style>
canvas {
  border: 1px solid black;
}
</style>
