<template>
  <article class="bg">
    
    <WebcamViewer/>
  </article>
 
</template>

<script>

import WebcamViewer from './components/WebcamViewer.vue'

export default {
  name: 'App',
  components: {
    WebcamViewer,
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  align-items: center;


}
body {
  background-image: url(./assets/pinnwand-transformed.png);
}
</style>
