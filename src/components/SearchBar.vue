<script>
  export default {
    data() {
      return {
        searchbarVisible: false,
        searchInput: null,
      };
    },
    inject: ['showResult'],
    methods: {
      search() {
        if (this.searchInput) {
          this.searchbarVisible = false;
          // 程式碼
          this.showResult([this.searchInput]);
        }
      },
    },
  }
</script>

<template>
  <transition name="searchbar">
    <div v-if="searchbarVisible" class="d-flex position-absolute w-100 mt-4 px-5">
      <input type="text" placeholder="搜尋地點或資訊"
        class="search-input form-control" v-model="searchInput"
        @keydown.enter="search" />
    </div>
  </transition>
</template>

<style scoped>
  .search-input {
    box-shadow: 0 -1rem 2rem 2rem rgba(0, 0, 0, .3);
    clip-path: inset(-0.5rem -5rem -5rem -5rem);
  }
  .searchbar-enter-active, .searchbar-leave-active {
    transition: max-height 0.15s ease-out, opacity 0.15s ease-out;
  }
  .searchbar-enter-from, .searchbar-leave-to {
    max-height: 0;
    opacity: 0;
  }
  .searchbar-enter-to, .searchbar-leave-from {
    max-height: 5rem;
    opacity: 1;
  }
</style>