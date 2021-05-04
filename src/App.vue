<template>
  <div id="app">
    <link rel="preconnect" href="https://fonts.gstatic.com">
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:wght@300&display=swap" rel="stylesheet"> 
    <ShopifyFrontend msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import ShopifyFrontend from './components/ShopifyFrontend.vue'

export default {
  name: 'App',
  components: {
    ShopifyFrontend
  }
}
</script>

<style>
#app {
  font-family: 'IBM Plex Sans', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 2vh;
}
</style>
