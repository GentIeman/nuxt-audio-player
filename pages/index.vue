<template>
  <section class="page">
    <section class="base">
      <div class="base_circle circle" :class="{'slide-up' : isPlayed}"></div>
      <div class="base_circle circle " :class="{'slide-down' : isPlayed}"></div>
      <div class="technology">
        <img class="technology__logo" src="/icons/nuxt.svg" alt="">
        <p class="technology__text text">Create with Nuxt.js</p>
      </div>
      <section class="slider">
        <transition-group name="carousel-transition" class="slider__body">
          <v-slider-items v-for="song in trackData" :key="song.id" :data="song" :active="song.id === currentSong.id"/>
        </transition-group>
      </section>
      <section class="title-track">
        <header class="title-track__header">
          <h3 class="title-track__title title"> {{ currentSong.title }}</h3>
        </header>
      </section>
      <section class="timeline">
        <div class="timeline__base" ref="progressContainer" @click="setProgress">
          <div class="timeline__progress" :style="{width: progress + '%'}" ref="progress">
            <div class="timeline__range"></div>
          </div>
        </div>
        <div class="time-code">
          <span class="begin-time time" v-html="currentTime()"> 00:00 </span>
          <span class="end-time time" v-html="totalTime()"> 00:00 </span>
        </div>
      </section>
      <audio id="audio-player" ref="player" controls
             :src="'/music/'+ currentSong.src +'.mp3'">
        Your browser does not support audio tag.
      </audio>
      <section class="panel">
        <div class="panel__shuffle">
          <img src="/icons/shuffle.svg" alt="shuffle" width="30px">
        </div>
        <div class="main-btns">
          <div class="main-btns__previous-song">
            <img src="/icons/previous-song.svg" alt="previous song" width="30px" @click="songListStepper(-1)">
          </div>
          <div class="main-btns__play-song" v-if="isPlayed === false" @click="playToggle()">
            <img src="/icons/play.svg" alt="play" width="40px">
          </div>
          <div class="main-btns__pause-song" v-if="isPlayed === true" @click="playToggle()">
            <img src="/icons/pause.svg" alt="pause" width="40px">
          </div>
          <div class="main-btns__next-song">
            <img src="/icons/next-song.svg" alt="next song" width="30px" @click="songListStepper(1)">
          </div>
        </div>
        <div class="panel__repeat">
          <img src="/icons/repeat.svg" alt="repeat" width="30px">
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
    this.currentSong = this.trackData[1]
  },
  methods: {
    songListStepper(dir) {
      let audio = this.$refs.player
      let pos = this.trackData.findIndex(item => item === this.currentSong)
      this.currentSong = this.trackData[(pos + dir) > this.trackData.length - 1 ? 0 : (pos + dir) < 0 ? this.trackData.length - 1 : pos + dir]

      setTimeout( () => {
        this.playToggle()
      }, audio.currentTime);
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
      setInterval(() => {
        this.progress = (audio.currentTime / audio.duration) * 100
      }, audio.currentTime)
    },
    setProgress(e) {
      let audio = this.$refs.player
      let width = this.$refs.progressContainer.clientWidth
      let clickX = e.offsetX;
      audio.currentTime = (clickX / width) * audio.duration;
      if (audio.currentTime > 0) {
        audio.play()
        this.isPlayed = true
        this.updateProgress()
      }
    }
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
  justify-content center
  align-items center
  width 100vw
  height 100vh

  audio {
    display none
  }

  .base {
    position relative
    width 1000px
    height 520px
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

    &__circle {
      position relative
    }

    .circle {
      position absolute
      width 350px
      height 350px
      border-radius 100%
      background-color #2ED573

      &:first-child {
        width 450px
        height 450px
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

    .slide-up {
      animation slide-up 10s linear infinite
    }

    .slide-down {
      animation slide-down 10s linear infinite
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
      display flex
      justify-content center
      align-items center
      position absolute
      top 35%
      left 50%
      transform translate(-50%, -50%)
      width 500px
      height 200px
      z-index 2

      &__body {
        display flex
        justify-content center
        align-items center
      }
    }
  }

  .title-track {
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
    cursor pointer
    z-index 2

    &__base {
      background #B7B3B3
      border-radius 5px
      cursor pointer
      height 4px
      width 100%

      .timeline__progress {
        display flex
        justify-content flex-end
        align-items center
        position relative
        background-color #1DD1A1
        border-radius 5px
        height 100%
        width 0
        transition width 0s linear

        &:hover .timeline__range {
          display block
        }

        .timeline__range {
          display none
          position absolute
          width 12px
          height 12px
          background-color #1DD1A1
          border-radius 50%
          box-shadow rgba(99, 99, 99, 0.2) 0px 2px 8px 0px
        }
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
    width 350px
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
}

.carousel-transition-move {
  transition transform 30s
}

</style>
