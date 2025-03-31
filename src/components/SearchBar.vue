<script>
  import locData from "@/assets/data.json";
  export default {
    data() {
      return {
        visible: false,
        invalid: false,
        input: "",
      };
    },
    mounted() {
      document.addEventListener("click", this.hide);
      document.addEventListener("touchstart", this.hide);
    },
    inject: ['showResult'],
    methods: {
      hide(e) {
        if (e.touches) e = e.touches[0];
        if (this.visible && e.target != this.$refs.input
          && !document.querySelector(".bi-search").parentElement.contains(e.target)) {
          this.visible = false;
        }
      },
      search() {
        let keyword = this.input.trim();
        if (keyword) {
          let result = locData
            .filter(loc => {
              const nameMatch = loc.name.toLowerCase().includes(keyword);
              const introMatch = loc.intro?.toLowerCase().includes(keyword);
              const tagMatch = loc.tag?.some(tag => tag.toLowerCase().includes(keyword));
              return nameMatch || introMatch || tagMatch;
            }).map(loc => loc.name);
          if (result.length) {
            this.showResult(result);
            this.visible = false;
            this.input = "";
          } else {
            this.invalid = true;
          }
        } else {
          this.invalid = true;
        }
      },
    },
    watch: {
      visible() {
        if (this.visible) {
          this.$nextTick(() => {
            this.$refs.input.focus();
          });
        }
      },
      input() {
        this.invalid = false;
      }
    },
  }
</script>

<template>
  <transition name="SearchBar">
    <div v-if="visible" class="d-flex position-absolute w-100 mt-4 px-5">
      <input ref="input" v-model="input" type="text" placeholder="搜尋地點或資訊"
        :class="['search-input', 'form-control', { 'is-invalid': invalid }]"
        @keydown.enter="search" />
    </div>
  </transition>
</template>

<style scoped>
  .search-input {
    box-shadow: 0 -1rem 2rem 2rem rgba(0, 0, 0, .3);
    clip-path: inset(-0.5rem -5rem -5rem -5rem);
  }
  .SearchBar-enter-active, .SearchBar-leave-active {
    transition: max-height 0.15s ease-out, opacity 0.15s ease-out;
  }
  .SearchBar-enter-from, .SearchBar-leave-to {
    max-height: 0;
    opacity: 0;
  }
  .SearchBar-enter-to, .SearchBar-leave-from {
    max-height: 5rem;
    opacity: 1;
  }
</style>