<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div v-for="(aud, idx) in audios" :key="idx">
      <rg-audio-player :audio="aud" :index="idx"></rg-audio-player>
    </div>
  </div>
</template>

<script>
import RgAudioPlayer from "@/components/rg-audio-player";
export default {
  name: 'HelloWorld',
  components: {RgAudioPlayer},
  props: {
    msg: String,
    default() {
      return "Hello World"
    }
  },
  data() {
    return {
      audios: [
          "https://r3---sn-a5meknll.googlevideo.com/videoplayback?expire=1608976304&ei=ULPmX9yjBNWqkgbSlpzAAw&ip=104.243.28.172&id=o-AHxB1_z7VC_NZVRwAwCamj7DL5SbTWFn0IsAZktjE_TV&itag=140&source=youtube&requiressl=yes&mh=QV&mm=31%2C29&mn=sn-a5meknll%2Csn-a5mlrnel&ms=au%2Crdu&mv=m&mvi=3&pl=20&gcr=us&initcwndbps=1595000&vprv=1&mime=audio%2Fmp4&ns=rrWEdPHCKrzT2TPBNy0ESnYF&gir=yes&clen=2089308&dur=129.056&lmt=1608943310015364&mt=1608954512&fvip=3&keepalive=yes&beids=23886208&c=WEB&txp=6311222&n=4sjgDAfBeWaZtjWs&sparams=expire%2Cei%2Cip%2Cid%2Citag%2Csource%2Crequiressl%2Cgcr%2Cvprv%2Cmime%2Cns%2Cgir%2Cclen%2Cdur%2Clmt&sig=AOq0QJ8wRAIgBxvN43mMxCWM16Zx8sePzNk58_72HLaAvlEh2NdX5HUCIEHg0wlxNvXwh6Y5mkan9oofhDIYZp_awmc4xPRjKJJA&lsparams=mh%2Cmm%2Cmn%2Cms%2Cmv%2Cmvi%2Cpl%2Cinitcwndbps&lsig=AG3C_xAwRQIgOZxLj8nsUWnZQGDTaEf_D0tg8P6yGYLF32Bx0l9hJngCIQCk3AfhdIpl1W9msUH4zz1VaSO8gbRChDnpQQiO1JT00g%3D%3D",
          "https://sub.rugoo.com.cn/music/the-all-clear.mp3",
          "https://www.0dutv.com/upload/dance/20200316/C719452E3C7834080007662021EA968E.mp3",
      ]
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
