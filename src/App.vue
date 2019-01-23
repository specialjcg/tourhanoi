<template>
  <div id="app">

    <tictactoe/>
  </div>
</template>

<script>
import tictactoe from './components/tictactoe'

export default {
  name: 'App',
  components: {
    tictactoe
  }
}
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
