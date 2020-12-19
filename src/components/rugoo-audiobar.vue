<template>
  <div class="rg-audio-container">
    <audio id="player" preload>
      <source :src="audiomp3" type="audio/mpeg" />
    </audio>
    <div id="controls">
      <div id="playPause" :class="playStyle" @click="playAudio" />
      <span id="playtime">{{metadata.currentTime}}/{{metadata.maxDuration}}</span>
      <div id="timeline">
        <div id="progressbar"></div>
      </div>
      <div id="volume-slider-control">
        <div id="volume-tint">
          <div id="volumebar"></div>
        </div>
      </div>
      <div id="volume">
      </div>
      <div id="download" @click="downloadAudio" />
    </div>
  </div>
</template>

<script>
import {downloadFromUrl} from "@/util/downloader";

export default {
  name: "rugoo-audiobar",
  props: {
    audio: {
      type: String,
      require: true
    }
  },
  data() {
    return {
      audioname: "hello",
      playStyle: 'fa-play',
      audioogg: "http://www.alexkatz.me/codepen/music/interlude.ogg",
      audiomp3: "http://www.alexkatz.me/codepen/music/interlude.mp3",
      metadata: {
        currentTime: "00:00",
        maxDuration: "00:00",
      }
    }
  },
  mounted() {
    this.initAudio()
  },
  methods: {
    initAudio() {
      const music = document.getElementById("player")
      const timeline = document.getElementById("timeline")
      const progressbar = document.getElementById("progressbar")
      const volumeSliderControl = document.getElementById("volume-slider-control")
      const volumeTint = document.getElementById("volume-tint")
      const volumeIcon = document.getElementById("volume")
      music.src = this.audiomp3
      music.addEventListener("loadedmetadata", () => {
        this.metadata.maxDuration = this.formatDuration(music.duration)
      }, false)
      music.addEventListener("timeupdate", (ev) => {
        if (ev) {
          let percent = music.currentTime / music.duration
          this.metadata.currentTime = this.formatDuration(music.currentTime)
          progressbar.style.width = parseInt(percent * timeline.offsetWidth) + "px"
        }
      }, false)
      music.addEventListener("ended", ev => {
        if (ev) {
          this.playStyle = 'fa-play'
        }
      }, false)
      timeline.addEventListener("click", ev => {
        let timelineXClicked = timeline.getBoundingClientRect().left
        let clickPercent = (ev.clientX - timelineXClicked) / timeline.offsetWidth
        let currentSeconds = this.calculateCurrentTime(clickPercent)
        progressbar.style.width = parseInt(timeline.offsetWidth * clickPercent) + "px"
        this.metadata.currentTime = this.formatDuration(currentSeconds)
        music.currentTime = currentSeconds
      })
      volumeTint.addEventListener("click", (ev) => {
        const music = document.getElementById("player")
        const volumebar = document.getElementById("volumebar")
        let volumeTintX = volumeTint.getBoundingClientRect().left
        let clickPercent = (ev.clientX - volumeTintX) / volumeTint.offsetWidth
        volumebar.style.width = parseInt(volumeTint.offsetWidth * clickPercent) + "px"
        music.volume = parseFloat(clickPercent)
      })
      volumeSliderControl.addEventListener("mouseleave", () => {
        this.hideVolumeSlider()
      }, false)
      volumeIcon.addEventListener("click", () => {
        this.appearVolumeSlider()
      }, false)
      // volumeIcon.addEventListener("mouseenter", () => {
      //   this.appearVolumeSlider()
      // })
    },
    playAudio() {
      const audioPlayer = document.getElementById("player")
      if (audioPlayer.paused) {
        audioPlayer.play()
        this.playStyle = 'fa-pause'
      } else {
        audioPlayer.pause()
        this.playStyle = 'fa-play'
      }
    },
    calculateCurrentTime(percent) {
      let duration = this.metadata.maxDuration
      let dur = duration.split(":")
      let seconds = parseInt(dur[0]) * 60 + parseInt(dur[1])
      return parseFloat(seconds * percent)
    },
    formatDuration(duration) {
      let date = new Date(0)
      date.setSeconds(parseInt(duration))
      return date.toISOString().substr(14, 5)
      // let min = parseInt(duration / 60).toString()
      // let sec = parseInt(duration % 60).toString()
      // if (min.length === 1) {
      //   min = "0" + min
      // }
      // if (sec.length === 1) {
      //   sec = "0" + sec
      // }
      // return min + ":" + sec
    },
    appearVolumeSlider() {
      const volumeSliderControl = document.getElementById("volume-slider-control")
      const timeline = document.getElementById("timeline")
      const progressbar = document.getElementById("progressbar")
      if (!this.onvolume.control) {
        const maxTimelineWidth = timeline.offsetWidth
        const currentProgress = progressbar.offsetWidth
        this.displayVolumeSlider()
        // 1. 保存原先timeline&progressbar长度
        this.onvolume.timelineWidth = maxTimelineWidth
        this.onvolume.progressbarWidth = currentProgress
        // 2. 设置timeline新值
        const curTimelineWidth = maxTimelineWidth - volumeSliderControl.offsetWidth
        const curProgressbarWidth = progressbar.offsetWidth * (curTimelineWidth / maxTimelineWidth)
        this.setTimeline(curTimelineWidth, curProgressbarWidth)
      } else {
        this.hideVolumeSlider()
      }
      this.onvolume.control = !this.onvolume.control
    },
    displayVolumeSlider() {
      (document.getElementById("volume-slider-control")).style.display = "block"
    },
    hideVolumeSlider() {
      (document.getElementById("volume-slider-control")).style.display = "none"
      this.recoverTimeline()
    },
    setTimeline(timelineWidth, progressbarWidth) {
      const timeline = document.getElementById("timeline")
      const progressbar = document.getElementById("progressbar")
      timeline.style.width = timelineWidth + "px"
      progressbar.style.width = progressbarWidth + "px"
    },
    recoverTimeline() {
      const timeline = document.getElementById("timeline")
      const progressbar = document.getElementById("progressbar")
      timeline.style.width = this.onvolume.timelineWidth + "px"
      progressbar.style.width = this.onvolume.progressbarWidth + "px"
    },
    downloadAudio() {
      downloadFromUrl(this.audiomp3)
    }
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
  margin-left: 10px;
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
  background-image: url("../assets/volume.png");
  background-size: 100% 100%;
  border-style: none;
  outline-style: none;
  width: 14px;
  height: 14px;
  cursor: pointer;
  margin-left: 5px;
}
#volume-slider-control {
  display: none;
  background: gray;
  border-radius: 10px;
  padding: 5px 10px;
}
#volume-tint {
  cursor: pointer;
  border-radius: 10px;
  width: 100px;
  height: 6px;
  background: whitesmoke;
}
#volumebar {
  border-radius: inherit;
  max-width: inherit;
  width: 30px;
  height: 6px;
  background: darkgray;
}
#download {
  background-image: url("../assets/download.png");
  background-size: 100% 100%;
  border: 0;
  outline: none;
  width: 13px;
  height: 13px;
  cursor: pointer;
  margin-left: 5px;
}
</style>