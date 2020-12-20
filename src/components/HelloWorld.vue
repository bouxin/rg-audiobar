<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <rg-audiobar></rg-audiobar>
  </div>
</template>

<script>
import RgAudiobar from "@/components/rg-audiobar";
export default {
  name: 'HelloWorld',
  components: {RgAudiobar},
  props: {
    msg: String,
    default() {
      return "Hello World"
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
h1 {
  margin-top: 100px;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
