<script setup>
import HomeView from './views/HomeView.vue'
</script>

<template>
  <HomeView />
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Fira+Code:wght@300;400;500;600;700&family=Inter:wght@300;400;500;600;700;800;900&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  transition: background-color 0.3s ease, color 0.3s ease;
  overflow-x: hidden;
}

#app {

  width: 100%;
  min-height: 100vh;
}

/* Global scrollbar styles */
::-webkit-scrollbar {
  width: 10px;
}

::-webkit-scrollbar-track {
  background: #0a192f;
}

::-webkit-scrollbar-thumb {
  background: #64ffda;
  border-radius: 5px;
}

::-webkit-scrollbar-thumb:hover {
  background: #00d9ff;
}

/* Selection color */
::selection {
  background: rgba(100, 255, 218, 0.3);
  color: #64ffda;
}
</style>
