<script>
import locData from "@/assets/data.json";
export default {
  data() {
    return {
      visible: false,
      imageSrc: import.meta.glob('@/assets/img/*.jpg'),
    };
  },
  methods: {
    async showDetail(name) {
      this.loc = locData.find(loc => loc.name == name);
      this.img = (await this.imageSrc[this.loc.pic]?.())?.default;
      this.visible = true;
    },
  },
}
</script>

<template>
  <transition name="DetailCard">
    <div v-if="visible" class="position-fixed">
      <div class="detail-card position-fixed z-1 d-flex flex-column flex-md-row p-5 align-items-start bg-white rounded-4 shadow-lg">
        <div class="d-flex flex-column">
          <div class="image-container d-flex ratio ratio-1x1 align-items-center justify-content-center rounded-3 shadow-sm overflow-hidden">
            <img :src="img" class="w-100 h-100 object-fit-cover" />
          </div>
          <div class="d-flex flex-wrap mt-4 gap-2 overflow-hidden">
            <div v-for="(tag, index) in loc.tag" :key="index" class="badge bg-secondary fs-6 cursor-pointer">{{ tag }}</div>
          </div>
        </div>
        <div class="flex-grow-1 mt-3 ms-md-4 text-start">
          <h1 class="fw-bold">{{ loc.name }}</h1>
          <p class="text-muted my-3">{{ loc.intro }}</p>
        </div>
      </div>
      <div class="position-fixed z-0 w-100 vh-100 bg-black opacity-50" @click="visible=false" @touchstart="visible=false" @wheel="(e)=>e.preventDefault()"></div>
    </div>
  </transition>
</template>

<style scoped>
  .detail-card {
    width: 800px;
    max-width: 90%;
    max-height: 70vh;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    overflow-y: auto;
    overscroll-behavior: contain;
    scrollbar-width: none;
  }
  .image-container {
    width: 300px;
    max-width: 60vw;
  }
  .DetailCard-enter-active, .DetailCard-leave-active {
    transition: opacity 0.15s ease-in-out;
  }

  .DetailCard-enter, .DetailCard-leave-to {
    opacity: 0;
  }

  .DetailCard-enter-to, .DetailCard-leave {
    opacity: 1;
  }
</style>