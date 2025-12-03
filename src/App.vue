<script setup>
import WishlistForm from './components/WishlistForm.vue'
</script>

<template>
  <div class="app-container">
    <WishlistForm />
  </div>
</template>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
  font-family:
    -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: linear-gradient(135deg, #fafbf6ff 0%, #dce1c6ff 100%);
  min-height: 100vh;
}

#app {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem 1rem;
}

.app-container {
  width: 100%;
}
</style>
