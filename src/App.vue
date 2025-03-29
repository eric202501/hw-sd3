<script>
  import MapCanvas from './components/MapCanvas.vue';
  import SearchBar from './components/SearchBar.vue';
  export default {
    components: {
      MapCanvas,
      SearchBar,
    },
    methods: {
      toggleSearchbar() {
        this.$refs.SearchBar.searchbarVisible = !this.$refs.SearchBar.searchbarVisible;
      },
      showResult(data) {
        //this.$refs.MapCanvas.showResult(data);
      },
    },
    provide() {
      return {
        showResult: this.showResult,
      }
    },
  }
</script>

<template>
  <div class="d-flex flex-column h-100">
    <header class="bg-white shadow py-3">
      <div class="container d-flex justify-content-between">
        <a href="/" class="fs-2 text-black fw-bold m-0 px-2 text-decoration-none">NCU Map</a>
        <button class="btn btn-link" @click="toggleSearchbar"><i class="bi-search fs-5 text-black"></i></button>
      </div>
      <SearchBar ref="SearchBar" />
    </header>
    <main class="d-flex flex-grow-1 justify-content-center">
      <MapCanvas ref="MapCanvas" />
    </main>
    <footer class="bg-dark text-white text-center py-3">
      <p class="m-0">Copyright © 2025 NCU Map</p>
    </footer>
  </div>
</template>
