<script>
  export default {
    data() {
      return {
        visible: true,
      };
    },
    mounted() {
      document.addEventListener("click", this.click);
    },
    unmount() {
      document.removeEventListener("click", this.click);
    },
    methods: {
      click() {
        this.visible = false;
      }
    }
  }
</script>

<template>
  <transition name="WelcomePage">
    <div v-if="visible" class="position-fixed">
      <div class="welcome-page position-fixed z-1 d-flex flex-column p-5 justify-content-top align-items-center bg-white rounded-4 shadow-lg">
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/NCULogo.svg/1200px-NCULogo.svg.png" width="300">
        <h1 class="mt-4 mb-1 fw-bold">國立中央大學地標導覽</h1>
        <span class="my-2 font-monospace fs-5">Vite + Vue.js + Bootstrap5</span>
        <span class="my-1 text-gray">Dev： <a class="text-decoration-none" href="https://github.com/eric202501">羅宣祐</a> ／ <a class="text-decoration-none" href="https://github.com/ccwangg">汪宸宇</a> ／ <a class="text-decoration-none" href="https://github.com/hunteryang0108">楊昊</a></span>
        <span class="mt-4 mb-2 blink">點擊任意處進入</span>
      </div>
      <div class="position-fixed z-0 w-100 vh-100 bg-black opacity-50"></div>
    </div>
  </transition>
</template>

<style scoped>
  .welcome-page {
    width: 800px;
    max-width: 90%;
    max-height: 90%;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    overflow-y: auto;
    overscroll-behavior: contain;
    scrollbar-width: none;
  }
  .welcome-page img {
    max-width: 70%;
  }
  @media (max-width: 1024px) {
    .welcome-page h1 {
      font-size: 1.6rem !important;
    }
    .welcome-page span {
      font-size: 0.8rem !important;
    }
  }
  @media (max-width: 425px) {
    .welcome-page h1 {
      font-size: 1.2rem !important;
    }
    .welcome-page span {
      font-size: 0.8rem !important;
    }
  }
  .blink {
    animation: blink-effect 2s infinite;
  }
  @keyframes blink-effect {
    0% { opacity: 1; }
    50% { opacity: 0.5; }
    100% { opacity: 1; }
  }
  .WelcomePage-enter-active, .WelcomePage-leave-active {
    transition: opacity 0.3s ease-in-out;
  }
  .WelcomePage-enter, .WelcomePage-leave-to {
    opacity: 0;
  }
  .WelcomePage-enter-to, .WelcomePage-leave {
    opacity: 1;
  }
</style>