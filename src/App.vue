<script setup>
  import MapCanvas from './components/MapCanvas.vue';
</script>

<template>
  <div class="d-flex flex-column h-100">
    <header class="bg-white shadow py-3">
      <div class="container d-flex justify-content-between">
        <a href="/" class="fs-2 text-black fw-bold m-0 px-2 text-decoration-none">NCU Map</a>
        <button class="btn btn-link" @click="toggleSearchbar"><i class="bi-search fs-5 text-black"></i></button>
      </div>
    </header>
    <main class="d-flex flex-grow-1 justify-content-center">
      <MapCanvas />
    </main>
    <footer class="bg-dark text-white text-center py-3">
      <p class="m-0">Copyright © 2025 NCU Map</p>
    </footer>
  </div>
</template>
