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
      showResult(result) {
        this.$refs.MapCanvas.showResult(result);
      },
      showDetail(loc) {
        //this.$refs.?.showDetail(loc);
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
        <button class="btn btn-link" @click="toggleSearchbar"><i class="bi-search fs-5 text-black"></i></button>
      </div>
      <SearchBar ref="SearchBar" />
    </header>

    <main class="container d-flex flex-grow-1 justify-content-center align-items-center my-5" >
      <div class="d-flex rounded-4 overflow-hidden">
        <MapCanvas />
        
      </div>

      <div class="d-flex flex-column w-25 bg-light shadow p-3 rounded-3 gap-3"  >
        <button class="btn btn-outline-primary w-100 py-3 fw-bold rounded-3">美食 Delicacies</button>
        <button class="btn btn-outline-success w-100 py-3 fw-bold rounded-3">系所 Department</button>
        <button class="btn btn-outline-warning w-100 py-3 fw-bold rounded-3">宿舍 Dormitory</button>
        <button class="btn btn-outline-danger w-100 py-3 fw-bold rounded-3">行政 Administration</button>
      </div>
    </main>

    <footer class="bg-dark text-white text-center mt-auto py-3">
      <p class="m-0">Copyright &copy; 2025 NCU Map</p>
    </footer>
  </div>
</template>
