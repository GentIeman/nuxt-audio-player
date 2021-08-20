<template>
  <section class="page">
    <section class="base">
      <div class="base__circle circle" :class="{'slide-up' : isPlayed}"></div>
      <div class="base__circle circle " :class="{'slide-down' : isPlayed}"></div>
      <div class="technology">
        <img class="technology__logo" src="/icons/nuxt.svg" alt="">
        <p class="technology__text text">Create with Nuxt.js</p>
      </div>
      <section class="slider__wrapper">
        <div class="slider" :style="sliderLength" v-for="song in trackData" :key="song.id">
          <div class="slider__item" :style="slidePosition">
            <v-slider-items :data="song"/>
          </div>
        </div>
      </section>
      <section class="info-track">
        <header class="info-track__header">
          <h3 class="info-track__title title"> {{ currentSong.title }}</h3>
        </header>
      </section>
      <section class="timeline">
        <div class="timeline__base" ref="progressContainer" @click="setProgress">
          <div class="timeline__progress" :style="{width: progress + '%'}">
            <div class="range" v-if="progress > 1"></div>
          </div>
        </div>
        <div class="time-code">
          <span class="begin-time time" v-html="currentTime()"> 00:00 </span>
          <span class="end-time time" v-html="totalTime()"> 00:00 </span>
        </div>
      </section>
      <audio id="audio-player" ref="player" :loop="loop"
             :src="'/music/'+ currentSong.src +'.mp3'">
        Your browser does not support audio tag.
      </audio>
      <section class="panel">
        <div class="panel__shuffle">
          <img :src="'/icons/'+ shuffleIcon +'.svg'" alt="shuffle" width="30px" title="shuffle"
               @click="shuffleTracks()" @mousedown="shuffleIcon = 'shuffle_active'"
               @mouseup=" shuffleIcon = 'shuffle'">
        </div>
        <div class="main-btns">
          <div class="main-btns__previous-song">
            <img src="/icons/previous-song.svg" alt="previous song" width="30px" title="previous song"
                 @click="songListStepper(-1)">
          </div>
          <div class="main-btns__play-song" v-if="isPlayed === false" @click="playToggle()">
            <img src="/icons/play.svg" alt="play" title="play" width="40px">
          </div>
          <div class="main-btns__pause-song" v-if="isPlayed === true" @click="playToggle()">
            <img src="/icons/pause.svg" alt="pause" title="pause" width="40px">
          </div>
          <div class="main-btns__next-song">
            <img src="/icons/next-song.svg" alt="next song" title="next song" width="30px" @click="songListStepper(1)">
          </div>
        </div>
        <div class="panel__repeat">
          <img src="/icons/repeat.svg" alt="repeat" width="30px" title="repeat" v-if="loop === false"
               @click="loopTrack()">
          <img src="/icons/repeat_active.svg" alt="repeat" title="repeat active" width="30px" v-else
               @click="loopTrack()">
        </div>
      </section>
      <section class="music-panel" @mouseleave="showSoundSlider = false">
        <div class="music-panel__slider">
          <input type="range" min="0" max="1" step="0.1" class="sound-slider" v-model="volume"
                 :style="progressSoundSlider"
                 @input="setVolume()" :class="{'sound-slider_show': showSoundSlider}">
        </div>
        <div class="music-panel__sound-icon">
          <img :src='`/icons/${soundIcon}.svg`' alt="" @click="soundToggle()" @mouseover="showSoundSlider = true">
        </div>
      </section>
    </section>
  </section>
</template>

<script>
export default {
  data: () => ({
    isPlayed: false,
    progress: 0,
    currentSong: '',
    loop: false,
    shuffleIcon: 'shuffle',
    volume: 0.5,
    soundIcon: 'sound_min',
    showSoundSlider: false,
    trackData: [
      {
        id: 0,
        album: 'one_quiet_evening',
        title: 'One Quiet Evening (wood.)',
        src: 'One_Quiet_Evening-wood',
        author: 'wood.'
      },
      {
        id: 1,
        album: 'beneath_the_trees',
        title: 'Beneath the Trees (mell-ø)',
        src: 'Beneath_the_Trees-mell-ø',
        author: 'mello-Ø'
      },
      {
        id: 2,
        album: 'december',
        title: 'December (Magic Mondays)',
        src: 'December-Magic_Mondays',
        author: 'Magic Mondays'
      }
    ]
  }),
  created() {
    this.currentSong = this.trackData[0]
  },
  watch: {
    volume(value) {
      switch (true) {
        case value >= 0.7 :
          this.soundIcon = 'sound'
          break;
        case value > 0.1 && value < 0.7:
          this.soundIcon = 'sound_min'
          break;
        case value == 0:
          this.soundIcon = 'sound_off'
      }
    }
  },
  computed: {
    sliderLength() {
      return {width: `${this.trackData.length * 100}%`}
    },
    slidePosition() {
      return {transform: `translateX(-${this.currentSlideIndex * 100}%)`}
    },
    progressSoundSlider() {
      return {background: `linear-gradient(to right, #1DD1A1 ${this.volume * 100}%, #dbd5d5 0%)`}
    },
    currentSlideIndex() {
      return this.trackData.findIndex(item => item.id === this.currentSong.id)
    },

  },
  methods: {
    songListStepper(dir) {
      let pos = this.trackData.findIndex(item => item === this.currentSong)
      this.currentSong = this.trackData[(pos + dir) > this.trackData.length - 1 ? 0 : (pos + dir) < 0 ? this.trackData.length - 1 : pos + dir]
      setTimeout(() => this.playToggle())
    },
    convertTime(seconds) {
      const format = val => `0${Math.floor(val)}`.slice(-2)
      let hours = seconds / 3600
      let minutes = (seconds % 3600) / 60
      return [minutes, seconds % 60].map(format).join(":")
    },
    totalTime() {
      let audio = this.$refs.player

      if (!audio) return '00:00'

      let seconds = audio.duration
      if (isNaN(seconds)) {
        return '00:00'
      }
      return this.convertTime(seconds)
    },
    currentTime() {
      let audio = this.$refs.player

      if (!audio) return '00:00'

      let seconds = audio.currentTime
      return this.convertTime(seconds)
    },
    playToggle() {
      let audio = this.$refs.player;

      if (audio.paused) {
        audio.play();
        this.isPlayed = true
        this.updateProgress()
      } else {
        audio.pause()
        this.isPlayed = false
      }
    },
    updateProgress() {
      let audio = this.$refs.player
      setInterval(() => this.progress = (audio.currentTime / audio.duration) * 100, audio.currentTime)
      audio.onended = () => this.songListStepper(1)
    },
    setProgress(e) {
      let audio = this.$refs.player
      let width = this.$refs.progressContainer.clientWidth
      let clickX = e.offsetX;
      audio.currentTime = (clickX / width) * audio.duration;
      if (audio.currentTime > 0) this.updateProgress()
    },
    loopTrack() {
      this.loop = this.loop === false;
    },
    shuffleTracks() {
      let audio = this.$refs.player
      setTimeout(() => audio.play())
      this.isPlayed = true
      let tempIndex = this.currentSlideIndex
      let currentIndex = this.trackData.length, temporaryValue, randomIndex
      while (0 !== currentIndex) {
        currentIndex -= 1
        randomIndex = Math.floor(Math.random() * currentIndex);
        temporaryValue = this.trackData[currentIndex];
        this.trackData[currentIndex] = this.trackData[randomIndex];
        this.trackData[randomIndex] = temporaryValue;
      }
      this.currentSong = this.trackData[tempIndex]
    },
    soundToggle() {
      let audio = this.$refs.player
      if (!audio.muted) {
        audio.muted = true
        this.volume = 0
      } else {
        audio.muted = false
        this.volume = audio.volume
      }
    },
    setVolume() {
      let audio = this.$refs.player
      audio.volume = this.volume
    }
  },
  mounted() {
    let audio = this.$refs.player
    audio.volume = this.volume
  }
}
</script>

<style lang="stylus">
@import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap');

*,
*:before,
*:after {
  margin 0
  padding 0
}

.page {
  display flex
  width 100vw
  height 100vh

  audio {
    display none
  }

  .base {
    position relative
    width 1000px
    height 520px
    margin auto
    border-radius 18px
    border solid 1px #B7B3B3
    z-index 1
    overflow hidden

    .technology {
      display flex
      justify-content center
      align-items center
      position absolute
      top 27px
      left 36px
      z-index 1

      &__text {
        margin-left 10px
      }

      .text {
        color #929090
        font normal 1em 'Roboto', sans-serif
      }
    }

    &:before {
      content ''
      position absolute
      top 0
      left 0
      width 110%
      height 110%
      background rgba(255, 255, 255, 0.7)
      border-radius 18px
      backdrop-filter blur(5px)
      z-index 1
    }

    .circle {
      position absolute
      width 450px
      height 450px
      border-radius 100%
      background-color #2ED573

      &:first-child {
        width 550px
        height 550px
        bottom 0
        left 0
        transform translate(-40%, 30%)
      }

      &:nth-child(2) {
        top 0
        right 0
        transform translate(30%, -30%)
      }
    }

    @media screen and (max-width 465px) {
      .circle {
        width 250px
        height 250px

        &:first-child {
          width 250px
          height 250px
        }
      }
    }

    @media (prefers-reduced-motion: no-preference) {
      .slide-up {
        animation slide-up 10s linear infinite
      }

      .slide-down {
        animation slide-down 10s linear infinite
      }
    }

    @keyframes slide-up {
      0%, 100% {
        transform translate(-40%, 30%)
      }
      50% {
        transition 5s
        transform translate(-40%, -20%)
      }
    }

    @keyframes slide-down {
      0%, 100% {
        transform translate(30%, -30%)
      }
      50% {
        transition 5s
        transform translate(40%, 60%)
      }
    }

    .slider {
      position absolute
      top 35%
      left 50%
      transform translate(-50%, -50%)
      width 300px
      height 200px
      z-index 1
      overflow hidden

      &__list {
        display flex
        list-style none

        .slider__item {
          padding 0 50px
          transition all .8s ease
        }
      }
    }

    .info-track {
      position absolute
      top 61%
      left 50%
      transform translate(-50%, -50%)
      z-index 2
      max-width 800px
      width 500px

      &__title {
        display flex
        justify-content center
        align-items center
      }

      .title {
        font normal 1.2em sans-serif
        color #333
      }
    }

    .timeline {
      position absolute
      top 75%
      left 50%
      transform translate(-50%, -50%)
      width 486px
      height 5px
      border-radius 6px
      z-index 2

      &__base {
        background #dbd5d5
        border-radius 5px
        height 6px
        width 100%

        .timeline__progress {
          display flex
          justify-content flex-end
          align-items center
          position relative
          background-color #1DD1A1
          border-radius 5px
          height 100%
          transition width 0s linear

          .range {
            opacity 0
            position absolute
            width 0
            height 0
            border-radius 50%
            background-color #fff
            cursor pointer
            box-shadow rgba(0, 0, 0, 0.24) 0px 3px 8px
            transition all .2s ease
          }
        }

        &:hover .range {
          opacity 1
          width 13px
          height 13px
        }
      }

      .time-code {
        display flex
        position absolute
        top -25px
        justify-content space-between
        width 100%
        height auto
        cursor default

        .time {
          font normal 0.9em sans-serif
          color #B7B3B3
        }
      }
    }

    .panel {
      display flex
      justify-content space-between
      align-items center
      position absolute
      bottom 5%
      left 50%
      transform translate(-50%, -40%)
      width 486px
      height 40px
      z-index 2

      & > div {
        cursor pointer
      }

      .main-btns {
        display flex
        justify-content space-between
        align-items center
        width 200px
        cursor default

        & > div {
          cursor pointer
        }
      }
    }

    @media screen and (max-width 465px ) {
      .panel {
        width 370px
      }
    }

    .sound {
      display flex
      justify-content center
      align-items center
      position absolute
      top 79%
      left 85%
      transform translate(-50%, -50%) rotate(-90deg)
      z-index 2

      &__slider-wrapper {
        display flex
        justify-content center
        align-items center
        position relative
        order 1

        .sound__slider {
          opacity 0
          width 80px
          visibility hidden
          transition all .2s ease
        }

        .sound__slider_show {
          opacity 1
          visibility visible
        }
      }

      &__icon-wrapper {
        position relative
        margin-right 10px
        width 100%
        height 100%
        transform rotate(90deg)
        cursor pointer
      }
    }
    @media screen and (max-width 1023px ) {
      .sound {
        display none
      }
    }
  }
}

input[type=range] {
  -webkit-appearance none
  border-radius 20px
  outline none
}


input[type=range]::-webkit-slider-thumb {
  -webkit-appearance none
  position relative
  width 13px
  height 13px
  margin-top -4px
  background-color #fff
  border-radius 50%
  box-shadow rgba(0, 0, 0, 0.24) -3px 0px 8px
}

input[type=range]::-webkit-slider-runnable-track {
  width 100%
  height 5px
  cursor pointer
}
</style>
