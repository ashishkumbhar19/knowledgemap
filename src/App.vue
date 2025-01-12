<template>
  <div id="app">
    <video autoplay muted loop id="background-video">
      <source src="@/assets/image/bgvid1.mp4" type="video/mp4" />
      Your browser does not support the video tag.
    </video>
    <KnowledgeMap />
  </div>
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
  position: relative;
  width: 100vw;
  height: 100vh;
  margin: 0;
  overflow: hidden;
}

#background-video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover; /* Ensures the video covers the entire background */
  z-index: -1; /* Places the video behind other elements */
}

body {
  margin: 0; /* Remove margin to avoid extra white space */
}
</style>
