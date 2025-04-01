<script>
  import mapImg from "@/assets/map.png";
  import locData from "@/assets/data.json";
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
        hovering: false,
        scaling: false,
        pinOnDisplay: [],
        pinPosition: [],
      };
    },
    mounted() {
      this.canvas = this.$refs.map;
      this.pin = new Image();
      this.pin.src = "data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='red'><path d='M8 16s6-5.686 6-10A6 6 0 0 0 2 6c0 4.314 6 10 6 10m0-7a3 3 0 1 1 0-6 3 3 0 0 1 0 6'/></svg>"
      this.info = new Image();
      this.info.src = "data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='black'><path d='M 0.99999996,4.4133459 C 0.447715,4.4133459 0,4.8610611 0,5.4133458 v 5.0000002 c 0,0.552283 0.447715,0.999999 0.99999996,0.999999 0.59584904,-0.04087 5.18760884,-0.05651 5.64999984,0.2 l 0.9500001,1.2665 c 0.2,0.266666 0.6,0.266666 0.8,0 l 0.95,-1.2665 c 0.094427,-0.125903 0.2426214,-0.2 0.3999999,-0.2 H 15 c 0.552284,0 1,-0.447716 1,-0.999999 V 5.4133458 C 16,4.8610611 15.552284,4.4133459 15,4.4133459 Z' /></svg>"
      this.image = new Image();
      this.image.src = mapImg;
      this.image.onload = () => {
        this.canvas.addEventListener("mousemove", this.hover);
        this.canvas.addEventListener("click", this.click);
        this.canvas.addEventListener("touchend", this.touching);
        window.addEventListener("resize", this.resize);
        document.addEventListener("wheel", this.draw);
        this.resize();
      };
    },
    inject: ["showDetail"],
    methods: {
      draw() {
        this.width = this.imageWidth * this.scaleSize;
        this.height = this.imageHeight * this.scaleSize;
        this.offsetX = Math.min(Math.max(this.offsetX, this.canvas.width - this.width), 0);
        this.offsetY = Math.min(Math.max(this.offsetY, this.canvas.height - this.height), 0);
        let ctx = this.canvas.getContext("2d");
        ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
        ctx.drawImage(this.image, this.offsetX, this.offsetY, this.width, this.height);
        this.pinPosition = this.pinOnDisplay.map(loc => {
          let pinX = this.offsetX + loc.x * this.scaleSize;
          let pinY = this.offsetY + loc.y * this.scaleSize;
          return { name: loc.name, x1: pinX - 16, x2: pinX + 16, y1: pinY - 32, y2: pinY };
        });
        this.pinPosition.forEach(pin => {
          if (this.hovering && (pin.name === this.hovered)) {
            ctx.drawImage(this.pin, pin.x1 - 4, pin.y1 - 8, 40, 40);
            ctx.drawImage(this.info, pin.x1 - 19, pin.y1 - 72, 70, 70);
            ctx.fillStyle = "white";
            ctx.font = "15px monospace";
            ctx.font = (ctx.measureText(pin.name).width < 70) ? "15px monospace" : "12px monospace";
            ctx.fillText(pin.name, pin.x1 - (ctx.measureText(pin.name).width / 2) + 16, pin.y1 - 32);
          } else {
            ctx.drawImage(this.pin, pin.x1, pin.y1, 32, 32);
          }
        });
      },
      resize() {
        this.touch = window.matchMedia("(pointer: coarse)").matches;
        this.canvas.width = this.canvas.clientWidth;
        this.canvas.height = this.canvas.clientHeight;
        this.canvasRect = this.canvas.getBoundingClientRect();
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
      press(e) {
        if (this.touch && e.touches) {
          e.preventDefault()
          this.touchedTime = Date.now();
          this.touches = e.touches;
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
      release() {
        this.pinching = false;
        this.dragging = false;
        requestAnimationFrame(this.drift);
      },
      move(e) {
        if (this.touch && e.touches) {
          if (this.pinching) {
            let newDistance = this.distance(e.touches[0], e.touches[1]);
            this.scaleFactor *= newDistance / this.lastDistance;
            this.scaleFactor = Math.max(1, Math.min(this.scaleFactor, 5));
            this.lastDistance = newDistance;
            let centerX = (e.touches[0].clientX + e.touches[1].clientX) / 2;
            let centerY = (e.touches[0].clientY + e.touches[1].clientY) / 2;
            this.offsetX = centerX - ((centerX - this.offsetX) * this.scaleFactor) / this.scaleSize;
            this.offsetY = centerY - ((centerY - this.offsetY) * this.scaleFactor) / this.scaleSize;
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
        let deltaX = this.targetX - this.offsetX;
        let deltaY = this.targetY - this.offsetY;
        let deltaScale = this.scaleFactor - this.scaleSize;
        this.offsetX += deltaX * 0.1;
        this.offsetY += deltaY * 0.1;
        this.scaleSize += deltaScale * 0.1;
        this.draw();
        if (Math.abs(deltaX) > 0.01 || Math.abs(deltaY) > 0.01 || Math.abs(deltaScale) > 0.01) {
          requestAnimationFrame(this.scale);
        } else {
          this.offsetX = this.targetX;
          this.offsetY = this.targetY;
          this.scaleSize = this.scaleFactor;
        }
      },
      showResult(result) {
        this.pinOnDisplay = locData.filter(loc => result.includes(loc.name))
          .map(loc => ({ name: loc.name, x: loc.position.x, y: loc.position.y }));
        this.draw();
      },
      hover(e) {
        if (this.dragging) return;
        let x = e.clientX - this.canvasRect.left + window.scrollX;
        let y = e.clientY - this.canvasRect.top + window.scrollY;
        this.hovering = false;
        this.pinPosition.forEach((loc) => {
          if (x > loc.x1 && x < loc.x2 && y > loc.y1 && y < loc.y2) {
            this.hovering = true;
            this.hovered = loc.name;
          }
        });
      },
      click(e) {
        if (this.hovering) this.showDetail(this.hovered);
      },
      touching() {
        if (!this.touches) return;
        if ((Date.now() - this.touchedTime) > 500) return;
        let x = this.touches[0].clientX - this.canvasRect.left + window.scrollX;
        let y = this.touches[0].clientY - this.canvasRect.top  + window.scrollY;
        let touched = null;
        this.pinPosition.forEach((loc) => {
          if (x > loc.x1 && x < loc.x2 && y > loc.y1 && y < loc.y2) {
            touched = loc.name;
          }
        });
        if (touched) this.showDetail(touched);
      },
    },
    watch: {
      hovering() { this.draw(); },
    },
};
</script>

<template>
  <div class="canvas-container d-flex mt-4 mb-3 rounded-3 overflow-hidden">
    <canvas ref="map"
      :class="['w-100', 'h-100', 'touch-none',
        hovering ? 'cursor-pointer' : (dragging ? 'cursor-grabbing' : 'cursor-grab')]"
      @mousedown="press"
      @mouseup="release"
      @mouseleave="release"
      @mousemove="move"
      @wheel="wheel"
      @touchstart="press"
      @touchend="release"
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
