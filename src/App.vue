<template>
  <div id="app">
    <rugoo-audiobar></rugoo-audiobar>
  </div>
</template>

<script>
import RugooAudiobar from "@/components/rugoo-audiobar";

export default {
  name: 'App',
  components: {
    RugooAudiobar,
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
