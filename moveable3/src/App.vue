<template>
  <div id="app">
    <Canvas />
  </div>
</template>

<script>
import Canvas from './components/Canvas.vue'

export default {
  name: 'App',

  components: {
    Canvas
  }
}
</script>

<style>
html, body {
  margin: 0;
}
</style>
