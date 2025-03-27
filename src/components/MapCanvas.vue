<script>
  export default {
    data() {
      return {
        canvas: null,
        image: null,
        imageScale: null,
        offsetX: null,
        offsetY: null,
        velocityX: null,
        velocityY: null,
        lastX: null,
        lastY: null,
        targetX: null,
        targetY: null,
        targetScale: 1,
        scaleFactor: 1,
        dragging: false,
        scaling: false,
      };
    },
    mounted() {
      this.canvas = this.$refs.map;
      this.image = new Image();
      this.image.src = './map.png';
      this.image.onload = () => {
        this.imageScale = Math.max(this.canvas.width / this.image.width, this.canvas.height / this.image.height);
        this.scale();
      };
    },
    methods: {
      draw() {
        this.offsetX = Math.min(Math.max(this.offsetX, this.canvas.width - this.width), 0);
        this.offsetY = Math.min(Math.max(this.offsetY, this.canvas.height - this.height), 0);
        let ctx = this.canvas.getContext('2d');
        ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
        ctx.drawImage(this.image, this.offsetX, this.offsetY, this.width, this.height);
      },
      mousedown(e) {
        this.dragging = true;
        this.lastX = e.clientX;
        this.lastY = e.clientY;
      },
      mouseup() {
        this.dragging = false;
        requestAnimationFrame(this.drift);
      },
      mousemove(e) {
        if (this.dragging) {
          let dx = e.clientX - this.lastX;
          let dy = e.clientY - this.lastY;
          this.offsetX += dx;
          this.offsetY += dy;
          this.velocityX = dx;
          this.velocityY = dy;
          this.lastX = e.clientX;
          this.lastY = e.clientY;
          this.draw();
        }
      },
      wheel(e) {
        e.preventDefault();
        if (this.scaling) return;
        this.scaling = true;
        this.targetScale = e.deltaY < 0 ? Math.min(5, this.targetScale + 0.2) : Math.max(1, this.targetScale - 0.2);
        this.targetX = e.offsetX - ((e.offsetX - this.offsetX) * this.targetScale) / this.scaleFactor;
        this.targetY = e.offsetY - ((e.offsetY - this.offsetY) * this.targetScale) / this.scaleFactor;
        requestAnimationFrame(this.scale);
        this.scaling = false;
      },
      drift() {
        if (!this.dragging) {
          this.offsetX += this.velocityX;
          this.offsetY += this.velocityY;
          this.velocityX *= 0.95;
          this.velocityY *= 0.95;
          this.draw();
          if (Math.abs(this.velocityX) > 0.01 || Math.abs(this.velocityY > 0.01)) {
            requestAnimationFrame(this.drift);
          } else {
            this.velocityX = 0;
            this.velocityY = 0;
          }
        }
      },
      scale() {
        let moveDiffX = this.targetX - this.offsetX;
        let moveDiffY = this.targetY - this.offsetY;
        let scaleDiff = this.targetScale - this.scaleFactor;
        this.offsetX += moveDiffX * 0.1;
        this.offsetY += moveDiffY * 0.1;
        this.scaleFactor += scaleDiff * 0.1;
        this.width = this.image.width * this.imageScale * this.scaleFactor;
        this.height = this.image.height * this.imageScale * this.scaleFactor;
        this.draw();
        if (Math.abs(moveDiffX) > 0.01 || Math.abs(moveDiffY) > 0.01 || Math.abs(scaleDiff) > 0.01) {
          requestAnimationFrame(this.scale);
        } else {
          this.offsetX = this.targetX;
          this.offsetY = this.targetY;
          this.scaleFactor = this.targetScale;
        }
      },
    },
  };
</script>

<template>
  <canvas ref="map" width="900" height="600"
    @mousedown="mousedown"
    @mouseup="mouseup"
    @mouseleave="mouseup"
    @mousemove="mousemove"
    @wheel="wheel">
  </canvas>
</template>