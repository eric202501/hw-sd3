<script>
  import MapCanvas from './components/MapCanvas.vue';
  import SearchBar from './components/SearchBar.vue';
  import ButtonMenu from './components/ButtonMenu.vue';
  export default {
    components: {
      MapCanvas,
      SearchBar,
      ButtonMenu,
    },
    data() {
      return {
        searchbarVisible: false,
        buttonMenuVisible: false,
      };
    },
    methods: {
      toggleSearchBar() {
        this.searchbarVisible = !this.searchbarVisible;
      },
      toggleButtonMenu() {
        this.buttonMenuVisible = !this.buttonMenuVisible;
      },
      showResult(result) {
        this.$refs.MapCanvas.showResult(result);
      },
      showDetail(loc) {
        this.$refs.MapCanvas.showDetail?.(loc);
      },
    },

    provide() {
      return {
        showResult: this.showResult,
        showDetail: this.showDetail,
      }
    },
  }
</script>

<template>
  <div class="d-flex flex-column h-100">
    <header class="bg-white shadow py-3">
      <div class="container d-flex justify-content-between">
        <a href="/" class="fs-2 text-black fw-bold m-0 px-2 text-decoration-none">NCU Map</a>
        <div class="d-flex gap-2">
          <button class="btn btn-link" @click="toggleSearchBar"><i class="bi bi-search fs-5 text-black"></i></button>
          <button class="btn btn-link" @click="toggleButtonMenu"><i class="bi bi-list fs-5 text-black"></i></button>
        </div>
      </div>
      <SearchBar :visible="searchbarVisible" @close="searchbarVisible = false" />
      <ButtonMenu :visible="buttonMenuVisible" @close="buttonMenuVisible = false" />

    </header>

    <main class="d-flex flex-grow-1 justify-content-center">
      <MapCanvas ref="MapCanvas" />
    </main>

    <footer class="bg-dark text-white text-center py-3">
      <p class="m-0">Copyright © 2025 NCU Map</p>
    </footer>
  </div>
</template>
