<template>
  <div class="canvas-container position-relative d-flex mt-4 mb-3 rounded-3 overflow-hidden">
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
    <div
    v-for="(pin, index) in pinPosition"
    :key="pin.name"
    class="pin-tooltip position-absolute"
    :style="{
      left: `${pin.canvasX}px`,
      top: `${pin.canvasY}px`,
      transform: 'translate(-50%, -100%)',
    }"
    data-bs-toggle="tooltip"
    data-bs-placement="top"
    :title="pin.name"
></div>
  </div>
</template>

<script>
import mapImg from "@/assets/map.png";
import locData from "@/assets/data2.json";
import * as bootstrap from 'bootstrap';
import 'bootstrap/dist/css/bootstrap.min.css';

export default {
  data() {
    return {
      canvas: null,
      image: null,
      pin: null,
      offsetX: 0,
      offsetY: 0,
      velocityX: 0,
      velocityY: 0,
      scaleSize: 1,
      scaleFactor: 1,
      dragging: false,
      pinching: false,
      scaling: false,
      hovering: false,
      pinOnDisplay: [],
      pinPosition: [],
      hovered: null,
      tooltipState: {}
      
    };
  },
  inject: ["showDetail"],
  mounted() {
    this.canvas = this.$refs.map;

    this.pin = new Image();
    this.pin.src = "data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='red'><path d='M8 16s6-5.686 6-10A6 6 0 0 0 2 6c0 4.314 6 10 6 10m0-7a3 3 0 1 1 0-6 3 3 0 0 1 0 6'/></svg>";

    this.pin.onload = () => {
      this.image = new Image();
      this.image.src = mapImg;

      this.image.onload = () => {
        this.canvas.addEventListener("mousemove", this.hover);
        this.canvas.addEventListener("click", this.click);
        this.canvas.addEventListener("touchend", this.touching);
        window.addEventListener("resize", this.resize);

        this.pinOnDisplay = locData.map(loc => ({
          name: loc.name,
          x: loc.position[0],
          y: loc.position[1],
        }));

        this.resize();
      };
    };
  },
  methods: {
    
    updateAllTooltips() {
      const containerRect = document.querySelector('.canvas-container').getBoundingClientRect();
      const tooltipEls = document.querySelectorAll('[data-bs-toggle="tooltip"]');
      tooltipEls.forEach(el => {
      const instance = bootstrap.Tooltip.getInstance(el);
      if (instance) {
        instance.update();
        const refRect = el.getBoundingClientRect();
        const tip = instance.tip;
        const inRange = !(refRect.right < containerRect.left ||
                          refRect.left > containerRect.right ||
                          refRect.bottom < containerRect.top ||
                          refRect.top > containerRect.bottom);

        const key = el.getAttribute('data-bs-original-title') || '';
        if (this.tooltipState[key] === undefined) {
        this.tooltipState[key] = true;
      }
        if (inRange) {
          if (!this.tooltipState[key]) {
            instance.show();
            this.tooltipState[key] = true;
          }
        } 
        else {
            if (this.tooltipState[key]) {
              instance.hide();
              this.tooltipState[key] = false;
            }
          
}
      }
    });
  },
    draw() {
      if (!this.canvas || !this.image || !this.image.width || !this.image.height) return;

      this.imageWidth = this.image.width * this.scaleSize;
      this.imageHeight = this.image.height * this.scaleSize;
      this.width = this.imageWidth;
      this.height = this.imageHeight;

      this.offsetX = Math.min(Math.max(this.offsetX, this.canvas.width - this.width), 0);
      this.offsetY = Math.min(Math.max(this.offsetY, this.canvas.height - this.height), 0);

      let ctx = this.canvas.getContext("2d");
      ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      ctx.drawImage(this.image, this.offsetX, this.offsetY, this.width, this.height);

      this.pinPosition = [];
      this.pinOnDisplay.forEach(loc => {
        let pinX = this.offsetX + loc.x * this.scaleSize;
        let pinY = this.offsetY + loc.y * this.scaleSize;
        let isHovered = (loc.name === this.hovered);
        let size = isHovered ? 40 : 32;
        let offset = size / 2;

        this.pinPosition.push({
            name: loc.name,
            canvasX: pinX,
            canvasY: pinY,
            x1: pinX - offset,
            x2: pinX + offset,
            y1: pinY - size,
            y2: pinY,
        });
       
        ctx.drawImage(this.pin, pinX - offset, pinY - size, size, size);
      });
    },
    resize() {
      if (!this.image) return;
      this.touch = window.matchMedia("(pointer: coarse)").matches;
      this.canvas.width = this.canvas.clientWidth;
      this.canvas.height = this.canvas.clientHeight;
      this.canvasRect = this.canvas.getBoundingClientRect();

      let imageScale = Math.max(
        this.canvas.width / this.image.width,
        this.canvas.height / this.image.height
      );

      this.imageWidth = this.image.width * imageScale;
      this.imageHeight = this.image.height * imageScale;

      this.offsetX = this.canvas.width / 2 - this.imageWidth / 2;
      this.offsetY = this.canvas.height / 2 - this.imageHeight / 2;
      this.scaleSize = imageScale;
      this.scaleFactor = imageScale;

      this.draw();
    },
    press(e) {
      e.preventDefault();
      if (!this.image) return;
      if (this.touch) {
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
      if (!this.image) return;
      this.pinching = false;
      this.dragging = false;
      requestAnimationFrame(this.drift);
    },
    move(e) {
      if (!this.image) return;
      if (this.touch && this.pinching) {
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
        
        return;
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
      if (!this.image) return;
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
      if (!this.image || this.dragging) return;
      this.offsetX += this.velocityX;
      this.offsetY += this.velocityY;
      this.velocityX *= 0.95;
      this.velocityY *= 0.95;
      this.draw();
      if (Math.abs(this.velocityX) > 0.01 || Math.abs(this.velocityY) > 0.01) {
        requestAnimationFrame(this.drift);
      } else {
        this.velocityX = 0;
        this.velocityY = 0;
        
      }
      this.updateAllTooltips();
      
    },
    scale() {
      if (!this.image || this.dragging) return;
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
      this.updateAllTooltips();
      
    },
    distance(a, b) {
      return Math.sqrt((b.clientX - a.clientX) ** 2 + (b.clientY - a.clientY) ** 2);
    },
    showResult(result) {
  
  if (result.length === 1) {
    const keyword = result[0].toLowerCase();

    this.pinOnDisplay = locData.filter(loc => {
      const inName = loc.name.toLowerCase().includes(keyword);
      const inIntro = loc.intro?.toLowerCase().includes(keyword);
      const inTag = loc.tag?.some(tag => tag.toLowerCase().includes(keyword));
      return inName || inIntro || inTag;
    }).map(loc => ({
      name: loc.name,
      x: loc.position[0],
      y: loc.position[1]
    }));
  } 
  else {
    this.pinOnDisplay = locData
      .filter(loc => result.includes(loc.name))
      .map(loc => ({
        name: loc.name,
        x: loc.position[0],
        y: loc.position[1]
      }));
  }


      this.draw();
    },
    hover(e) {
      if (this.dragging) return;
      let x = e.clientX - this.canvasRect.left;
      let y = e.clientY - this.canvasRect.top;
      this.hovered = null;
      this.pinPosition.forEach((loc) => {
        if (x > loc.x1 && x < loc.x2 && y > loc.y1 && y < loc.y2) {
          this.hovered = loc.name;
        }
      });
      this.draw();
    },
    click(e) {
        if (this.hovering) this.showDetail(this.hovered);
    },

  },
    watch: {
    pinOnDisplay(newVal, oldVal) {
      if (JSON.stringify(newVal) !== JSON.stringify(oldVal)) {
        this.$nextTick(() => {
          document.querySelectorAll('.tooltip').forEach(el => el.remove());
          const tooltipTriggerList = Array.from(document.querySelectorAll('[data-bs-toggle="tooltip"]'));
          tooltipTriggerList.forEach(el => {
            const instance = bootstrap.Tooltip.getInstance(el);
            if (instance) {
              instance.dispose();
            }
            const newInstance = new bootstrap.Tooltip(el);
            newInstance.show();
          });
        });
      }
    }
  }
  
};
</script>

<style scoped>

.canvas-container {
    max-width: 95%;
    max-height: 95%;
  }
  canvas {
  z-index: 0;
  position: relative;
  }
  .pin-tooltip {
  width: 40px;
  height: 40px;
  z-index: 150;
  pointer-events: none;
  
}
  @media (min-width: 768px) {
    .canvas-container {
      width: 900px;
      height: 600px;
    }
  }
</style>