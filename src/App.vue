<template>
  <div id="app">
    <ShipmentUtility/>
  </div>
</template>

<script>
import ShipmentUtility from './components/ShipmentUtility.vue'

export default {
  name: 'App',
  components: {
    ShipmentUtility
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
}
</style>
