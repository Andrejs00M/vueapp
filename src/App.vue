<template>
  <Locations/>
</template>

<script>
import Locations from './components/locations-component.vue'

export default {
  name: 'App',
  components: {
    Locations: Locations
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
