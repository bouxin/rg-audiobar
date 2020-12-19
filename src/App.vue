<template>
  <div id="app">
    <hello-world />
  </div>
</template>

<script>
import RugooAudiobar from "@/components/rg-audiobar";
import HelloWorld from "@/components/HelloWorld";

export default {
  name: 'App',
  components: {
    RugooAudiobar,
    HelloWorld
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
