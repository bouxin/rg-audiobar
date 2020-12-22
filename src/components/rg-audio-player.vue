<template>
  <div class="rg-audio-container">
    <audio id="player" preload>
      <source :src="audio" type="audio" />
    </audio>
    <div id="controls">
      <div id="playPause" class="fa-play" @click="playMusic" />
      <span id="playtime">{{metadata.currentTimePoint}}/{{metadata.playDuration}}</span>
      <div id="timeline">
        <div id="progressbar"></div>
      </div>
      <div id="volume-slider">
        <div id="volume-bar">
          <div id="current-volume"></div>
        </div>
      </div>
      <div id="volume" class="fa-volume">
      </div>
      <div id="download" @click="downloadAudio" />
    </div>
  </div>
</template>

<script>
import {downloadFromUrl} from "@/util/downloader"
export default {
  name: "rg-audio-player",
  props: {
    audio: {
      type: String,
      require: true
    }
  },
  data() {
    return {
      audioName: "hello",
      // mp3: 'https://sub.rugoo.com.cn/music/the-all-clear.mp3',
      // mp3: "https://www.0dutv.com/upload/dance/20200316/C719452E3C7834080007662021EA968E.mp3",
      volumeDisplayed: false,
      metadata: {
        lastVolume: 0,
        currentTimePoint: "00:00",
        playDuration: "00:00",
      }
    }
  },
  mounted() {
    this.initAudio()
  },
  methods: {
    initAudio() {
      /*
     1. Initializing audio metadata, playDuration
     2. Event listener, "timeupdate", "ended"
     3. Initializing music controls
       a. Music timeline event listener, "click"
       b. Volume icon event listener, "click", "mouseenter"
       c. Volume bar event listener, "mouseleave"
       d. Volume control event listener, "click"
     */
      const music = this.getAudioPlayer()
      const timeline = this.getMusicTimeline()
      const progressbar = this.getTimelineProgressbar()
      const volumeSlider = this.getVolumeSlider()
      const volumebar = this.getVolumebar()
      const volumeIcon = this.getVolumeIcon()

      // Ensure audio source would be loaded eventually
      music.src = this.audio

      music.addEventListener("loadedmetadata", () => {
        this.metadata.playDuration = this.formatMusicPlaytime(music.duration)
        this.metadata.currentTimePoint = this.formatMusicPlaytime(music.currentTime)

        // Initializing music volume to 50%
        let initializedVolume = 0.5
        music.volume = initializedVolume
        this.metadata.lastVolume = initializedVolume
        this.getVolume().style.width = this.toCssAccept(initializedVolume * 100)
      }, false)

      music.addEventListener("timeupdate", (ev) => {
        let playtimePercentage = music.currentTime / music.duration
        // refresh views
        this.metadata.currentTimePoint = this.formatMusicPlaytime(music.currentTime)
        progressbar.style.width = this.toCssAccept(playtimePercentage * timeline.offsetWidth)
      }, false)

      music.addEventListener("ended", ev => {
        const paused = document.getElementById("playPause")
        paused.setAttribute("class", 'fa-play')
      }, false)

      timeline.addEventListener("click", ev => {
        let timelineXCoordinate = timeline.getBoundingClientRect().left
        let currentProgressbarLength = ev.clientX - timelineXCoordinate
        let playtimePercentage = currentProgressbarLength / timeline.offsetWidth
        let newPlaytime = this.newMusicPlaytimeByPercentage(playtimePercentage)

        music.currentTime = newPlaytime
        this.metadata.currentTime = this.formatMusicPlaytime(newPlaytime)
        if (!music.paused) {
          // call music.play() when timeline changed immediately
          music.play()
        }

        progressbar.style.width = this.toCssAccept(currentProgressbarLength)
      })

      volumeIcon.addEventListener("click", () => {
        if (music.volume === 0) {
          let lastVolume = this.metadata.lastVolume
          music.volume = lastVolume
          this.getVolume().style.width = this.toCssAccept(lastVolume * 100)
          volumeIcon.setAttribute("class", 'fa-volume')
        } else {
          this.metadata.lastVolume = music.volume
          music.volume = 0
          this.getVolume().style.width = this.toCssAccept(0)
          volumeIcon.setAttribute("class", 'fa-volume-mute')
        }
      }, false)

      volumeIcon.addEventListener("mouseenter", () => {
        this.makeVolumeSliderVisible()
        volumeSlider.addEventListener("mouseleave", () => {
          this.makeVolumeSliderInvisible()
        }, false)
      }, false)

      volumebar.addEventListener("click", (ev) => {
        let volumebarXCoordinate = volumebar.getBoundingClientRect().left
        let currentVolumebarLength = ev.clientX - volumebarXCoordinate
        let percentage = currentVolumebarLength / volumebar.offsetWidth
        this.getVolume().style.width = this.toCssAccept(percentage * 100)
        music.volume = parseFloat((currentVolumebarLength / volumebar.offsetWidth).toString())
      })
    },
    playMusic() {
      const audioPlayer = this.getAudioPlayer()
      const play = document.getElementById("playPause")
      if (audioPlayer.paused) {
        audioPlayer.play()
        play.setAttribute("class", 'fa-pause')
      } else {
        audioPlayer.pause()
        play.setAttribute("class", 'fa-play')
      }
    },
    makeVolumeSliderVisible() {
      const volumeSlider = this.getVolumeSlider()
      let accurateTimelineLength = this.getMusicTimeline().offsetWidth
      let accurateProgressbarLength = this.getTimelineProgressbar().offsetWidth
      // display volume slider, this action must behind the accurate*Length return
      volumeSlider.style.display = "block"
      this.scaleMusicTimelineByOffset(accurateTimelineLength, accurateProgressbarLength, -volumeSlider.offsetWidth)
    },
    makeVolumeSliderInvisible() {
      const volumeSlider = this.getVolumeSlider()
      let accurateTimelineLength = this.getMusicTimeline().offsetWidth
      let accurateProgressbarLength = this.getTimelineProgressbar().offsetWidth

      this.scaleMusicTimelineByOffset(accurateTimelineLength, accurateProgressbarLength, volumeSlider.offsetWidth)
      volumeSlider.style.display = "none"
    },
    downloadAudio() {
      downloadFromUrl(this.audio)
    },
    newMusicPlaytimeByPercentage(percent) {
      let duration = this.metadata.playDuration
      let dur = duration.split(":")
      let seconds = parseInt(dur[0]) * 60 + parseInt(dur[1])
      return parseFloat((seconds * percent).toString())
    },
    formatMusicPlaytime(duration) {
      let date = new Date(0)
      date.setSeconds(parseInt(duration))
      return date.toISOString().substr(14, 5)
    },
    toCssAccept(num) {
      return parseInt(num).toString() + "px"
    },
    scaleMusicTimelineByOffset(accTimelineLen, accProgressbarLen, offset) {
      let scaledTimelineLength = accTimelineLen + offset
      let scale = accProgressbarLen / accTimelineLen

      this.getMusicTimeline().style.width = this.toCssAccept(scaledTimelineLength)
      this.getTimelineProgressbar().style.width = this.toCssAccept(scale * scaledTimelineLength)
    },
    getAudioPlayer() {
      return document.getElementById("player")
    },
    getVolumeSlider() {
      return document.getElementById("volume-slider")
    },
    getVolumebar() {
      return document.getElementById("volume-bar")
    },
    getVolume() {
      return document.getElementById("current-volume")
    },
    getVolumeIcon() {
      return document.getElementById("volume")
    },
    getMusicTimeline() {
      return document.getElementById("timeline")
    },
    getTimelineProgressbar() {
      return document.getElementById("progressbar")
    },
  }
}
</script>

<style scoped>
.rg-audio-container {
  margin: auto;
  font-family: sans-serif;
  text-align: left;
  font-size: 12px;
  background: #42b983;
  border-radius: 15px;
  width: 300px;
  height: 20px;
}
#controls {
  display: flex;
  flex-direction: row;
  align-items: center;
  border-radius: 15px;
  width: inherit;
  height: 20px;
}
#playPause {
  cursor: pointer;
  outline-style: none;
  border-style: none;
  padding: unset;
  margin-left: 8px;
  width: 14px;
  height: 14px;
}
.fa-play {
  background: url("../assets/play.png");
  background-size: 100% 100%;
}
.fa-pause {
  background: url("../assets/pause.png");
  background-size: 100% 100%;
}
#playtime {
  width: 65px;
  margin-left: 5px;
}
#timeline {
  margin: auto 5px;
  background: whitesmoke;
  border-radius: 10px;
  cursor: pointer;
  width: 150px;
  height: 6px;
}
#progressbar {
  background: gray;
  border-radius: inherit;
  max-width: inherit;
  width: 0;
  height: 6px;
}
#volume {
  border-style: none;
  outline-style: none;
  width: 14px;
  height: 14px;
  cursor: pointer;
  margin-left: 4px;
}
.fa-volume {
  background-image: url("../assets/volume.png");
  background-size: 100% 100%;
}
.fa-volume-mute {
  background-image: url("../assets/mute.png");
  background-size: 100% 100%;
}
#volume-slider {
  display: none;
  background: gray;
  border-radius: 10px;
  padding: 5px 10px;
}
#volume-bar {
  cursor: pointer;
  border-radius: 10px;
  width: 100px;
  height: 6px;
  background: whitesmoke;
}
#current-volume {
  border-radius: inherit;
  max-width: inherit;
  width: 0;
  height: 6px;
  background: darkgray;
}
#download {
  background-image: url("../assets/download.png");
  background-size: 100% 100%;
  border: 0;
  outline: none;
  width: 14px;
  height: 14px;
  cursor: pointer;
  margin-left: 7px;
}
</style>