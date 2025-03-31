<script>
  import locData from "@/assets/data.json";
  export default {
    data() {
      return {
        visible: false,
        classifyCode: {"美食 Delicacies": "0", "系所 Department": "1", "宿舍 Dormitory": "2" , "行政 Administration": "3"},
      };
    },
    inject: ["showResult"],
    mounted() {
      document.addEventListener("click", this.hide);
      document.addEventListener("touchstart", this.hide);
    },
    methods: {
      hide(e) {
        if (e.touches) e = e.touches[0];
        if (this.visible && !this.$refs.menu.contains(e.target)
          && !document.querySelector(".bi-list").parentElement.contains(e.target)) {
          this.visible = false;
        }
      },
      click(e) {
        let result = locData
          .filter(loc => loc.classify.includes(this.classifyCode[e.target.innerHTML]))
          .map(loc => loc.name);
        this.showResult(result);
        this.visible = false;
      }
    },
  }
</script>

<template>
  <transition name="ButtonMenu">
    <div ref="menu" v-if="visible" class="position-absolute d-flex flex-column gap-3 end-0 mt-3 p-3 bg-white shadow rounded-bottom-3">
      <button class="btn btn-outline-primary w-100 py-3 fw-bold rounded-3" @click="click">美食 Delicacies</button>
      <button class="btn btn-outline-success w-100 py-3 fw-bold rounded-3" @click="click">系所 Department</button>
      <button class="btn btn-outline-warning w-100 py-3 fw-bold rounded-3" @click="click">宿舍 Dormitory</button>
      <button class="btn btn-outline-danger w-100 py-3 fw-bold rounded-3" @click="click">行政 Administration</button>
    </div>
  </transition>
</template>

<style scoped>
  .ButtonMenu-enter-active, .ButtonMenu-leave-active {
    transition: transform 0.2s ease-out;
    transform-origin: top;
  }
  .ButtonMenu-enter-from, .ButtonMenu-leave-to {
    transform: scaleY(0);
  }
  .ButtonMenu-enter-to, .ButtonMenu-leave-from {
    transform: scaleY(100%);
  }
</style>