<script>
export default {
  props: {
    visible: Boolean,
  },
  data() {
    return {
      searchInput: "",
    };
  },
  inject: ['showResult'],
  methods: {
    search() {
      if (this.searchInput && this.searchInput.trim()) {
        const result = [this.searchInput.trim()];
        this.showResult(result);
        this.$emit('close');
        this.searchInput = '';
      }
    },
  },
}
</script>
<template>
  <transition name="searchbar">
    <div v-if="visible" class="d-flex position-absolute w-100 mt-4 px-5">
      <input
        type="text"
        placeholder="搜尋地點或資訊"
        class="search-input form-control"
        v-model="searchInput"
        @keydown.enter="search"
      />
    </div>
  </transition>
</template>
