<template>
  <div id="app">
    <blackartLoader currentLang="zh-CN" url="http://10.17.148.25/staffSelector-V1.0.0/index.js"></blackartLoader>
  </div>
</template>

<script>
import blackartLoader from './blackartLoader/index.js'

export default {
  name: 'App',
  components: {
    blackartLoader
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
</style>
