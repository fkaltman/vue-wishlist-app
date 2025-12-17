<template>
  <div class="banner">
    <h1>The Smithmas Wishlist is closed for the season, see you again next year.</h1>
  </div>
</template>

<style scoped>
.banner {
  display: flex;
  justify-content: center;
  margin-top: 20%;
  min-height: 100vh;
  padding: 2rem;
  text-align: center;
}

h1 {
  font-family: 'Mountains of Christmas', cursive;
  font-size: 2.5rem;
  color: #2c5f2d;
  max-width: 800px;
}

@media (max-width: 640px) {
  h1 {
    font-size: 1.75rem;
  }
}
</style>
