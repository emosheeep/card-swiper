<template>
  <div id="app">
    <example />
  </div>
</template>

<script>
import example from '@/pages/example.vue'
export default {
  name: 'app',
  components: {
    example
  }
}
</script>

<style lang="stylus" scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
