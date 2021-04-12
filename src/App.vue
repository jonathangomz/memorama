<template>
  <Timer />
  <Board />
</template>

<script>
import Board from "./components/Board.vue";
import Timer from "./components/Timer.vue";

export default {
  name: "App",
  components: {
    Board,
    Timer,
  },
};
</script>

<style>
body {
  background-color: #e9f1f7;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
