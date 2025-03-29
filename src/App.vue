<script setup>
  import MapCanvas from './components/MapCanvas.vue'
</script>

<template>
  <div id="app" class="d-flex flex-column">
    <header class="bg-light shadow py-3">
      <div class="container d-flex justify-content-between align-items-center">
        <h2 class="fw-bold m-0 px-2">NCU Map</h2>
        <button class="btn btn-link"><i class="bi bi-search fs-5 text-black"></i></button>
      </div>
    </header>
    <main class="d-flex flex-grow-1 justify-content-center align-items-center">
      <MapCanvas />
    </main>
    <footer class="bg-dark text-white text-center py-3">
      <p class="m-0">Copyright &copy; 2025 NCU Map</p>
    </footer>
  </div>
</template>

<style>
  html, body, #app {
    height: 100%;
  }
</style>