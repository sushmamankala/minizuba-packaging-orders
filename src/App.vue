<template>
  <div id="app">
    <PackagingOrders />
  </div>
</template>

<script>
import PackagingOrders from './components/packagingOrders.vue'

export default {
  name: 'App',
  components: {
    PackagingOrders
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  height: 97vh;
}
</style>
