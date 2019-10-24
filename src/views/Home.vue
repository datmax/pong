<template>
  <div class="home">
    <canvas id="layer0"></canvas>
    <canvas id="layer1"></canvas>
  </div>
</template>

<script>
// @ is an alias to /src
const width = window.innerWidth - 100;
const height = window.innerHeight - 200;
const velocity = 6;
const blockSize = {
  x: 40,
  y: 15
};
export default {
  data() {
    return {
      hit: 0,
      ctx: null,
      l2: null,
      rect: {
        x: width / 2 - 200,
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
          x: 4,
          y: 4
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
      this.ctx = document.getElementById("layer0").getContext("2d");
      this.l2 = document.getElementById("layer1").getContext("2d");
      this.l2.canvas.width = width;
      this.l2.canvas.height = height;
      this.ctx.imageSmoothingEnabled = false;
      this.ctx.canvas.width = width;
      this.ctx.canvas.height = height;
      this.ctx.clearRect(0, 0, 300, 300);
      this.initBlocks();
      window.requestAnimationFrame(() => {
        this.draw();
      });
      //this.initBall();
      window.addEventListener("keyup", e => {
        if (e.key == "ArrowLeft" || e.key == "ArrowRight") {
          this.rect.speed = 0;
        }
      });
      window.addEventListener("keydown", e => {
        switch (e.key) {
          case "ArrowLeft":
            this.rect.speed = -velocity;
            break;
          case "ArrowRight":
            this.rect.speed = velocity;
            break;
        }
      });
    },

    //----- RECT CONTROL-----//
    moveRect() {
      if (this.rect.x >= width) {
        this.rect.x = 0;
      } else if (this.rect.x < -this.rect.rectWidth) {
        this.rect.x = width - this.rect.rectWidth;
      } else {
        this.rect.x += 1 * this.rect.speed;
      }
      this.rect.x += 1 * this.rect.speed;
    },
    //-----BALL CONTROL-----//

    //-----INITIALIZATIONS-----//
    initBlocks() {
      for (let i = 0; i < width - blockSize.x; i += blockSize.x + 10) {
        for (let j = 1; j < 4; j++) {
          this.blocks.push({
            x: 10 + i,
            y: 10 + j * 40,
            width: blockSize.x,
            height: blockSize.y
          });
        }
      }
    },
    //-----COLLISIONS-----//
    ballCollision() {
      if (this.ball.y > height - 20) {
        if (
          this.ball.y + this.ball.radius >= this.rect.y &&
          this.ball.y < height - this.rect.rectHeight / 2
        ) {
          if (
            this.ball.x +this.ball.radius >= this.rect.x &&
            this.ball.x - this.ball.radius <= this.rect.x + this.rect.rectWidth
          ) {
            if (Math.abs(this.ball.x - this.rect.x) < this.ball.radius * 2){
              if (
                this.ball.x < this.rect.x + this.ball.radius * 2 &&
                this.ball.velocity.x > 0
              ) {
                this.ball.velocity.x = -this.ball.velocity.x;
              }
            }
            if (
              Math.abs(this.ball.x - this.rect.x - this.rect.rectWidth) <
              this.ball.radius * 2
            ) {
              if (
                this.ball.x >
                  this.rect.x + (this.rect.rectWidth - this.ball.radius * 2) &&
                this.ball.velocity.x < 0
              ) {
                this.ball.velocity.x = -this.ball.velocity.x;
              }
            }
            this.ball.velocity.y = -this.ball.velocity.y;
            this.ball.y -= 5;
          }
        }
      }
      if (this.ball.x >= width - this.ball.radius) {
        this.ball.velocity.x = -this.ball.velocity.x;
      }
      if (this.ball.x <= 0 + this.ball.radius) {
        this.ball.velocity.x = -this.ball.velocity.x;
      }
      if (this.ball.y <= 0 + this.ball.radius) {
        this.ball.velocity.y = -this.ball.velocity.y;
      }
      if (this.ball.y > height) {
        //this.collided = "you lost";
      }

      this.ball.x += this.ball.velocity.x;
      this.ball.y += this.ball.velocity.y;
    },
    blockCollision() {
      if (this.ball.y < height / 2) {
        for (let i = this.blocks.length - 1; i >= 0; i--) {
          let block = this.blocks[i];
          if (
            this.ball.x - this.ball.radius > block.x &&
            this.ball.x + this.ball.radius < block.x + block.width
          ) {
            if (
              this.ball.y + this.ball.radius >= block.y &&
              this.ball.y - this.ball.radius < block.y + block.height
            ) {
              this.ball.y += 2;
              this.ball.velocity.y = -this.ball.velocity.y;
              this.blocks.splice(i, 1);
            }
          }
        }
      }
    },
    draw() {
      this.ctx.clearRect(0, 0, width, height);
      this.moveRect();
      this.blockCollision();
      this.ballCollision();
      this.drawBricks();
      this.drawBall();
      this.drawPlayer();

      window.requestAnimationFrame(() => {
        this.draw();
      });
    },
    drawBricks() {
      for (let i = 0; i < this.blocks.length; i++) {
        this.ctx.fillRect(
          this.blocks[i].x,
          this.blocks[i].y,
          this.blocks[i].width,
          this.blocks[i].height
        );
      }
    },
    drawPlayer() {
      this.ctx.fillRect(
        this.rect.x,
        this.rect.y,
        this.rect.rectWidth,
        this.rect.rectHeight
      );
    },
    drawBall() {
      this.ctx.beginPath();
      this.ctx.arc(this.ball.x, this.ball.y, this.ball.radius, 0, 2 * Math.PI);
      this.ctx.fill();
      this.ctx.stroke();
    }
  }
};
</script>
<style>
#layer0 {
  z-index: 0;
}
#layer1 {
  z-index: 1;
}
</style>
