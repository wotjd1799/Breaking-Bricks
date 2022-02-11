<template>
<div id="board">
  <h1>Breaking Bricks!</h1>
  <h1>{{score}}</h1>
  <canvas ref="canvas" width="500" height="500" id="canvas"></canvas>
  <button id="reset" v-on:click="reset">Re Start</button>
</div>
</template>

<script>
export default {
  data() {
    return {
      WIDTH: 0,
      HEIGHT: 0,
      x: 230, //공의 시작 좌표
      y: 450,
      radius: 10, //공의 반지름
      dx: 6, //공의 이동속도
      dy: 6,
      anim: null,
      paddlex: 0,
      paddleh: 0,
      paddlew: 0,
      is_leftPannel: false,
      is_rightPannel: false,
      bricks: [0][0],
      NROWS: 0,
      NCOLS: 0,
      BRICKWIDTH: 0,
      BRICKHEIGHT: 0,
      PADDING: 0,
      score: 0,
      last: 0,
    }
  },
  methods: {
    draw() {
      this.clear();
      this.ball(this.x, this.y, this.radius);
      this.rect(this.paddlex, this.HEIGHT - this.paddleh, this.paddlew, this.paddleh);
      this.y += this.dy;
      this.x += this.dx;

      if (this.x + this.dx + this.radius > this.WIDTH || this.x + this.dx - this.radius < 0) this.dx = -this.dx;
      if (this.y <= 0 + this.radius) this.dy = -this.dy;
      else if (this.y >= this.HEIGHT - this.radius) {
        if (this.x > this.paddlex && this.x < this.paddlex + this.paddlew) {
          this.dx = -((this.paddlex + (this.paddlew / 2) - this.x) / this.paddlew) * 8;
          this.dy = -this.dy;
        } else {
          return;
        }
      }

      if (this.last <= this.score) {
        alert("Clear!");
        return;
      }

      for (let i = 0; i < this.NROWS; i++) {
        for (let j = 0; j < this.NCOLS; j++) {
          if (this.bricks[i][j] == 1) {
            this.rect(j * this.BRICKWIDTH,
              i * this.BRICKHEIGHT,
              this.BRICKWIDTH - this.PADDING,
              this.BRICKHEIGHT - this.PADDING);
          }
        }
      }

      let row = Math.floor(this.y / (this.BRICKHEIGHT + this.PADDING));
      let col = Math.floor(this.x / (this.BRICKWIDTH + this.PADDING));
      if (row < this.NROWS) {
        if (this.bricks[row][col] == 1) {
          this.score++;
          this.dy = -this.dy;
          this.bricks[row][col] = 0;
        }
      }

      if (this.is_leftPannel && this.paddlex > 0) this.paddlex -= 5;
      if (this.is_rightPannel && this.paddlex + this.paddlew < this.WIDTH) this.paddlex += 5;

      this.anim = window.requestAnimationFrame(this.draw);
    },
    clear() {
      this.ctx.clearRect(0, 0, this.WIDTH, this.HEIGHT);
    },
    ball(x, y, r) {
      this.ctx.fillStyle = 'black';
      this.ctx.beginPath();
      this.ctx.arc(x, y, r, 0, Math.PI * 2, true);
      this.ctx.closePath();
      this.ctx.fill();
    },
    init() {
      this.ctx = this.$refs.canvas.getContext('2d');
      this.WIDTH = this.$refs.canvas.width; //캔버스 크기
      this.HEIGHT = this.$refs.canvas.height;

      this.anim = window.requestAnimationFrame(this.draw);
    },
    init_paddle() {
      this.paddlex = (this.WIDTH / 2) - (75 / 2); //패들 x좌표값
      this.paddleh = 10; //패들 높이 
      this.paddlew = 70; //패들 길이
    },
    init_bricks() {
      this.NROWS = 2; //블럭 개수
      this.NCOLS = 2;
      this.last = this.NROWS * this.NCOLS; // 게임종료 카운트 수
      this.PADDING = 1; //블럭 간격
      this.BRICKWIDTH = (this.WIDTH / this.NCOLS);
      this.BRICKHEIGHT = 30; //블럭 높이

      this.bricks = new Array(this.NROWS);
      for (let i = 0; i < this.NROWS; i++) {
        this.bricks[i] = new Array(this.NCOLS);
        for (let j = 0; j < this.NCOLS; j++) {
          this.bricks[i][j] = 1;
        }
      }
    },
    rect(x, y, w, h) { //사각형 그리기
      this.ctx.beginPath();
      this.ctx.rect(x, y, w, h);
      this.ctx.closePath();
      this.ctx.fill();
    },
    keyUp(e) {
      if (e.keyCode == 37) this.is_leftPannel = false;
      else if (e.keyCode == 39) this.is_rightPannel = false;
    },
    keyDown(e) {
      if (e.keyCode == 37) this.is_leftPannel = true;
      else if (e.keyCode == 39) this.is_rightPannel = true;
    },
    mouseMove(e) {
      this.paddlex = e.pageX - ((window.innerWidth - this.WIDTH) / 2) - (75 / 2);
    },
    reset() {
      location.reload();
    }
  },
  mounted() {
    this.init();
    this.init_paddle();
    this.init_bricks();
    window.addEventListener('keyup', this.keyUp);
    window.addEventListener('keydown', this.keyDown);
    this.$refs.canvas.addEventListener('mousemove', this.mouseMove);
    console.log(this.last);
  }
}
</script>

<style scoped>
#canvas {
  border: 1px solid #000;
}

#board {
  display: flex;
  align-items: center;
  flex-direction: column;
  row-gap: 20px;
}

h1 {
  margin: 0;
}
</style>
