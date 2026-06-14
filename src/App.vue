<script setup>
import HomeView from './views/HomeView.vue'
</script>

<template>
  <HomeView />
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&family=Fira+Code:wght@300;400;500;600;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: 'Fira Code', 'Courier New', monospace;
  transition: background-color 0.3s ease, color 0.3s ease;
  overflow-x: hidden;
  image-rendering: pixelated;
}

#app {
  width: 100%;
  min-height: 100vh;
}

/* Retro scrollbar */
::-webkit-scrollbar {
  width: 12px;
}

::-webkit-scrollbar-track {
  background: #0a0a0a;
  border-left: 2px solid #1a1a2e;
}

::-webkit-scrollbar-thumb {
  background: #39ff14;
  border: 2px solid #0a0a0a;
  box-shadow: inset 0 0 6px rgba(57, 255, 20, 0.5);
}

::-webkit-scrollbar-thumb:hover {
  background: #00ffff;
  box-shadow: inset 0 0 10px rgba(0, 255, 255, 0.7);
}

/* Retro selection */
::selection {
  background: rgba(57, 255, 20, 0.4);
  color: #39ff14;
  text-shadow: 0 0 8px rgba(57, 255, 20, 0.6);
}
</style>
