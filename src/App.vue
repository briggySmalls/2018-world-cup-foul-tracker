<template>
  <div id="app">
    <img id="logo" src="./assets/football.svg" class="img-fluid">
    <FoulsCounter />
  </div>
</template>

<script>
import FoulsCounter from './components/FoulsCounter.vue'

export default {
  name: 'app',
  components: {
    FoulsCounter
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#logo {
  width: 12em;
}
</style>
