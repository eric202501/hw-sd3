<script>
  import mapImg from "@/assets/map.png"
  export default {
    data() {
      return {
        canvas: null,
        offsetX: null,
        offsetY: null,
        velocityX: null,
        velocityY: null,
        scaleSize: 1,
        scaleFactor: 1,
        dragging: false,
        pinching: false,
        scaling: false,
      };
    },
    mounted() {
      this.canvas = this.$refs.map;
      this.image = new Image();
      this.image.src = mapImg;
      this.image.onload = () => {
        window.addEventListener('resize', this.resize);
        this.resize();
      };
    },
    methods: {
      draw() {
        this.width = this.imageWidth * this.scaleSize;
        this.height = this.imageHeight * this.scaleSize;
        this.offsetX = Math.min(Math.max(this.offsetX, this.canvas.width - this.width), 0);
        this.offsetY = Math.min(Math.max(this.offsetY, this.canvas.height - this.height), 0);
        let ctx = this.canvas.getContext("2d");
        ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
        ctx.drawImage(this.image, this.offsetX, this.offsetY, this.width, this.height);
      },
      resize() {
        this.touch = window.matchMedia("(pointer: coarse)").matches;
        this.canvas.width = this.canvas.clientWidth;
        this.canvas.height = this.canvas.clientHeight;
        let imageScale = Math.max(this.canvas.width / this.image.width, this.canvas.height / this.image.height);
        this.imageWidth = this.image.width * imageScale;
        this.imageHeight = this.image.height * imageScale;
        this.offsetX = this.canvas.width / 2 - this.imageWidth / 2;
        this.offsetY = this.canvas.height / 2 - this.imageHeight / 2;
        this.draw();
      },
      distance(a, b) {
        return Math.sqrt((b.clientX - a.clientX) ** 2 + (b.clientY - a.clientY) ** 2);
      },
      pressed(e) {
        e.preventDefault();
        if (this.touch) {
          if (e.touches.length > 1) {
            this.pinching = true;
            this.lastDistance = this.distance(e.touches[0], e.touches[1]);
          }
          e = e.touches[0];
        }
        this.dragging = true;
        this.lastX = e.clientX;
        this.lastY = e.clientY;
      },
      released() {
        this.pinching = false;
        this.dragging = false;
        requestAnimationFrame(this.drift);
      },
      move(e) {
        if (this.touch) {
          if (this.pinching) {
            let newDistance = this.distance(e.touches[0], e.touches[1]);
            this.scaleFactor *= newDistance / this.lastDistance;
            this.scaleFactor = Math.max(1, Math.min(this.scaleFactor, 5));
            this.lastDistance = newDistance;
            let centerX = (e.touches[0].clientX + e.touches[1].clientX) / 2;
            let centerY = (e.touches[0].clientY + e.touches[1].clientY) / 2;
            this.offsetX = centerX - ((centerX - this.offsetX) / this.scaleSize) * this.scaleFactor;
            this.offsetY = centerY - ((centerY - this.offsetY) / this.scaleSize) * this.scaleFactor;
            this.scaleSize = this.scaleFactor;
            this.draw();
          }
          e = e.touches[0];
        }
        if (this.dragging) {
          let deltaX = e.clientX - this.lastX;
          let deltaY = e.clientY - this.lastY;
          this.offsetX += deltaX;
          this.offsetY += deltaY;
          this.velocityX = deltaX;
          this.velocityY = deltaY;
          this.lastX = e.clientX;
          this.lastY = e.clientY;
          this.draw();
        }
      },
      wheel(e) {
        e.preventDefault();
        if (this.scaling || this.dragging) return;
        this.scaling = true;
        this.scaleFactor = e.deltaY < 0 ? Math.min(5, this.scaleFactor + 0.2) : Math.max(1, this.scaleFactor - 0.2);
        this.targetX = e.offsetX - ((e.offsetX - this.offsetX) * this.scaleFactor) / this.scaleSize;
        this.targetY = e.offsetY - ((e.offsetY - this.offsetY) * this.scaleFactor) / this.scaleSize;
        requestAnimationFrame(this.scale);
        this.scaling = false;
      },
      drift() {
        if (this.dragging) return;
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
      },
      scale() {
        if (this.dragging) return;
        let moveDeltaX = this.targetX - this.offsetX;
        let moveDeltaY = this.targetY - this.offsetY;
        let scaleDelta = this.scaleFactor - this.scaleSize;
        this.offsetX += moveDeltaX * 0.1;
        this.offsetY += moveDeltaY * 0.1;
        this.scaleSize += scaleDelta * 0.1;
        this.draw();
        if (Math.abs(moveDeltaX) > 0.01 || Math.abs(moveDeltaY) > 0.01 || Math.abs(scaleDelta) > 0.01) {
          requestAnimationFrame(this.scale);
        } else {
          this.offsetX = this.targetX;
          this.offsetY = this.targetY;
          this.scaleSize = this.scaleFactor;
        }
      },
    },
  };
</script>

<template>
  <div class="canvas-container d-flex mt-4 mb-3 rounded-3 overflow-hidden">
    <canvas ref="map" class="w-100 h-100"
      @mousedown="pressed"
      @mouseup="released"
      @mouseleave="released"
      @mousemove="move"
      @wheel="wheel"
      @touchstart="pressed"
      @touchend="released"
      @touchmove="move"
      @contextmenu.prevent>
    </canvas>
  </div>
</template>

<style scoped>
  .canvas-container {
    max-width: 95%;
    max-height: 95%;
  }
  @media (min-width: 768px) {
    .canvas-container {
      width: 900px;
      height: 600px;
    }
  }
</style>
