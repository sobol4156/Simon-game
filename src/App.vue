
<template>
  <div id="app">
    <MyGame/>
  </div>
</template>

<script>
/* eslint-disable vue/multi-word-component-names */
import MyGame from './components/MyGame.vue';

export default {
  components: {
    MyGame
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
