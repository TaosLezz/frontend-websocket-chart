<template>
  <div id="app">
    <!-- Sử dụng component EChartsComponent ở đây -->
    <EChartsComponent />
  </div>
</template>

<script>
import EChartsComponent from './components/EChartsComponent.vue'
export default {
  name: 'App',
  components: {
    EChartsComponent
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
