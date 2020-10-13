<template>
  <div>
    <div class="overlay"></div>
    <div class="loader"></div>
  </div>
</template>

<script>
export default {};
</script>

<style>
@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.loader {
  animation: spin 0.6s linear infinite;
  border: 0.8rem solid var(--white);
  border-radius: 50%;
  border-top: 0.8rem solid var(--blue);
  height: 8rem;
  left: calc(50vw - 4rem);
  position: fixed;
  top: calc(50vh - 4rem);
  width: 8rem;
  z-index: 999;
}

.overlay {
  background-color: black;
  opacity: 0.5;
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  width: 100vw;
  z-index: 998;
}
</style>
