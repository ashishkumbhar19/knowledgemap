<template>
  <KnowledgeMap />
</template>

<script>
import KnowledgeMap from './components/KnowledgeMap.vue';

export default {
  components: {
    KnowledgeMap,
  },
};
</script>

<style>
#app {
  background-image: url('@/assets/image/bg2.png'); /* Correct path */
  background-size: cover; /* Ensure the background covers the entire area */
  background-position: center; /* Center the image */
  background-repeat: no-repeat; /* No repeating background */
  width: 100vw; /* Full viewport width */
  height: 100vh; /* Full viewport height */
  overflow: hidden; /* Prevent scrolling */
  margin: 0; /* Remove default body margin */
  display: flex;
  justify-content: center;
  align-items: center;
}

body {
  margin: 0; /* Remove margin to avoid extra white space */
}
</style>
