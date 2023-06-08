<template>
  <div class="overlay">
    <div class="spinner" />
  </div>
</template>

<style scoped lang="scss">
.overlay {
  position: fixed;
  display: flex;
  justify-content: center;
  align-items: center;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  transition: opacity background-color 1s;
  opacity: 50%;
  background-color: lightgray;
  z-index: 1;
}
.spinner {
  width: 100px;
  height: 100px;
  background-color: #666;
  border-radius: 100%;
  animation: sk-scaleout 1s infinite ease-in-out;
}

@keyframes sk-scaleout {
  0% {
    transform: scale(0);
  }
  100% {
    transform: scale(1);
    opacity: 0;
  }
}
</style>
