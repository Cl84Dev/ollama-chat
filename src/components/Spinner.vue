<template>
  <div class="spinner"></div>
</template>

<style scoped>
.spinner {
  width: 50px;
  height: 50px;
  margin: 0 auto;
  border: 5px solid #343942;
  border-left: 5px solid transparent;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  from {
    rotate: 0deg;
  }
  to {
    rotate: 360deg;
  }
}
</style>
