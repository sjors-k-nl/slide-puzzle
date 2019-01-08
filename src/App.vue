<template>
  <div id="app">
    <Board />
  </div>
</template>

<script>
import Board from './components/Board.vue';

export default {
  name: 'app',
  components: {
    Board,
  },
};
</script>

<style lang="scss">
  html, body {
    margin: 0;
    padding: 0;
    background-color: black;
  }
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0 0 0 0;
  padding: 0 0 0 0;
  height: 100vh;
  width: 100vw;
  justify-content: center;
  align-items: center;
}
</style>
