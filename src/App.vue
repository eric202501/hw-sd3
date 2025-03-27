<script setup>
import MapCanvas from './components/MapCanvas.vue'
</script>

<template>
  <div class="d-flex flex-column min-vh-100">
    <header class="bg-light shadow py-3">
      <div class="container d-flex justify-content-between align-items-center">
        <h2 class="fw-bold m-0">NCU Map</h2>
        <button class="btn btn-link"><i class="bi bi-search fs-5 text-black"></i></button>
      </div>
    </header>

    <main class="container d-flex flex-grow-1 justify-content-center align-items-center my-5">
      <div class="d-flex rounded-4 overflow-hidden">
        <MapCanvas />
      </div>
    </main>

    <footer class="bg-dark text-white text-center mt-auto py-3">
      <p class="m-0">Copyright &copy; 2025 NCU Map</p>
    </footer>
  </div>
</template>
