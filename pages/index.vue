<template>
  <section class="page" :class="{ 'page_dark': $colorMode.value == 'dark' }">
    <section class="base">
      <div class="background" :class="{ 'background_dark': $colorMode.value == 'dark' }">
        <div
          class="background__circle circle"
          :class="{ 'slide-up': isPlayed, 'background__circle_dark': $colorMode.value == 'dark' }">
        </div>
        <div
          class="background__circle circle"
          :class="{ 'slide-down': isPlayed, 'background__circle_dark': $colorMode.value == 'dark' }">
        </div>
      </div>
      <section class="body">
        <header class="body__header">
          <section class="technology" :class="{ 'technology_dark': $colorMode.value == 'dark' }">
            <img
              class="technology__logo"
              src="@/assets/icons/nuxt.svg"
              alt="technology logo"
              width="37px"
              height="37px"
            />
            <p class="technology__name name">Created with Nuxt.js</p>
          </section>
          <section class="theme" @click="themeToggle()">
            <img
              :src="require(`@/assets/icons/${themeIcon}_mode.svg`)"
              alt="theme switch"
              title="theme"
              width="33px"
              height="33px"
            />
          </section>
        </header>
        <div class="track-reference">
          <section class="slider">
            <ul class="slider__list" :style="sliderLength" v-for="song in trackData" :key="song.id">
              <li class="slider__item" :style="slidePosition">
                <v-slider-items :data="song" />
              </li>
            </ul>
          </section>
          <section class="info-track">
            <header class="info-track__header">
              <h3 class="info-track__title title" :class='{ "title_dark": $colorMode.value == "dark" }'>
                {{ currentSong.title }}
              </h3>
            </header>
          </section>
        </div>
        <section class="control-panel">
          <section class="timeline">
            <div class="time-code">
              <span class="begin-time time" v-html="currentTime()"> 00:00 </span>
              <span class="end-time time" v-html="totalTime()"> 00:00 </span>
            </div>
            <div class="timeline__base"
                 ref="progressContainer"
                 @click="setProgress"
                 :class="{'timeline__base_dark': $colorMode.value == 'dark'}">
              <div class="timeline__progress"
                   :style="{width: `${progress}%`}"
                   @draggable="true">
                <div class="range" v-if="progress > 1"></div>
              </div>
            </div>
          </section>
          <audio id="audio-player"
                 ref="player"
                 :loop="loop"
                 :src="`/music/${currentSong.src}.mp3`">
            Your browser does not support audio tag.
          </audio>
          <section class="panel">
            <div class="shuffle btn">
              <img
                :src="require(`@/assets/icons/${shuffleIcon}.svg`)"
                alt="shuffle"
                width="30px"
                height="30px"
                title="shuffle"
                @click="shuffleTracks()"
                @mousedown='shuffleIcon = "shuffle_active"'
                @mouseup='shuffleIcon = "shuffle"'
              />
            </div>
            <div class="panel__main">
              <div class="previous-song btn">
                <img
                  src="@/assets/icons/previous-song.svg"
                  alt="previous song"
                  width="30px"
                  height="30px"
                  title="previous song"
                  @click="songListStepper(-1)"
                />
              </div>
              <div class="play-song btn"
                   v-if="isPlayed === false"
                   @click="playToggle()">
                <img src="@/assets/icons/play.svg" alt="play" title="play" width="40px" height="40px" />
              </div>
              <div class="pause-song btn" v-if="isPlayed === true" @click="playToggle()">
                <img src="@/assets/icons/pause.svg" alt="pause" title="pause" width="40px" height="40px" />
              </div>
              <div class="next-song btn">
                <img
                  src="@/assets/icons/next-song.svg"
                  alt="next song"
                  title="next song"
                  width="30px"
                  height="30px"
                  @click="songListStepper(1)"
                />
              </div>
            </div>
            <div class="repeat btn">
              <img
                src="@/assets/icons/repeat.svg"
                alt="repeat"
                width="30px"
                height="30px"
                title="repeat"
                v-if="loop === false"
                @click="loopTrack()"
              />
              <img
                src="@/assets/icons/repeat_active.svg"
                alt="repeat"
                title="repeat active"
                width="30px"
                height="30px"
                v-else
                @click="loopTrack()"
              />
            </div>
          </section>
        </section>
        <section class="sound">
          <div class="sound__icon-box">
            <img
              :src="require(`@/assets/icons/${soundIcon}.svg`)"
              alt=""
              class="sound__icon"
              @click="muteToggle()"
              width="33px"
              height="33px"
            />
          </div>
          <div class="sound__slider-box">
            <input
              type="range"
              min="0"
              max="1"
              step="0.1"
              class="sound__progress progress"
              v-model="volume"
              :style="progressSoundSlider"
              @input="setVolume()"
            />
          </div>
        </section>
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
    themeIcon: 'light',
    colorMode: {
      preference: 'system',
      fallback: 'light'
    },
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
      return { width: `${this.trackData.length * 100}%` }
    },
    slidePosition() {
      return { transform: `translateX(-${this.currentSlideIndex * 100}%)` }
    },
    progressSlider() {
      if (this.$colorMode.value === 'light') {
        return { background: `linear-gradient(to right, #1DD1A1 ${this.progress}%, #dbd5d5 0%)` }
      } else {
        return { background: `linear-gradient(to right, #1DD1A1 ${this.progress}%, #353b48 0%)` }
      }
    },
    progressSoundSlider() {
      if (this.$colorMode.value === 'light') {
        return { background: `linear-gradient(to right, #1DD1A1 ${this.volume * 100}%, #dbd5d5 0%)` }
      } else {
        return { background: `linear-gradient(to right, #1DD1A1 ${this.volume * 100}%, #353b48 0%)` }
      }
    },
    currentSlideIndex() {
      return this.trackData.findIndex(item => item.id === this.currentSong.id)
    }
  },
  methods: {
    songListStepper(dir) {
      let pos = this.trackData.findIndex(item => item === this.currentSong)
      this.currentSong = this.trackData[pos + dir > this.trackData.length - 1 ? 0 : pos + dir < 0 ? this.trackData.length - 1 : pos + dir]
      setTimeout(() => this.playToggle())
    },
    convertTime(seconds) {
      const format = val => `0${Math.floor(val)}`.slice(-2)
      let hours = seconds / 3600
      let minutes = (seconds % 3600) / 60
      return [minutes, seconds % 60].map(format).join(':')
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
      let audio = this.$refs.player

      if (audio.paused) {
        audio.play()
        this.isPlayed = true
        this.updateProgress()
      } else {
        audio.pause()
        this.isPlayed = false
      }
    },
    updateProgress() {
      let audio = this.$refs.player
      setInterval(() => this.progress = (audio.currentTime / audio.duration) * 100)
      audio.onended = () => this.songListStepper(1)
    },
    setProgress(e) {
      let audio = this.$refs.player
      let width = this.$refs.progressContainer.clientWidth
      let clickX = e.offsetX
      audio.currentTime = (clickX / width) * audio.duration
      if (audio.currentTime > 0) this.updateProgress()
    },
    loopTrack() {
      this.loop = this.loop === false
    },
    shuffleTracks() {
      let audio = this.$refs.player
      setTimeout(() => audio.play())
      this.isPlayed = true
      let tempIndex = this.currentSlideIndex
      let currentIndex = this.trackData.length
      while (0 !== currentIndex) {
        currentIndex -= 1
        let randomIndex = Math.floor(Math.random() * currentIndex)
        let temporaryValue = this.trackData[currentIndex]
        this.trackData[currentIndex] = this.trackData[randomIndex]
        this.trackData[randomIndex] = temporaryValue
      }
      this.currentSong = this.trackData[tempIndex]
    },
    muteToggle() {
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
    },
    themeToggle() {
      if (this.$colorMode.value == 'dark') {
        this.$colorMode.value = 'light'
        this.themeIcon = 'dark'
      } else {
        this.$colorMode.value = 'dark'
        this.themeIcon = 'light'
      }
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
  box-sizing border-box
}

.page.page_dark {
  background-color #2f3640
}

.page {
  display flex
  width 100%
  height 100vh

  audio {
    display none
  }

  .base {
    position relative
    margin auto
    width 1000px
    height 520px
    z-index 1

    .background.background_dark {
      border solid 1px #222f3e

      &:before {
        background rgba(0, 0, 0, 0.7)
      }
    }

    .background {
      position absolute
      width 100%
      height 100%
      border-radius 20px
      border solid 2px #ded8d8
      overflow hidden

      @media screen and (max-width: 1023px) {
        & {
          border-radius 0
          border none

          &.background_dark {
            border none
          }
        }
      }

      @media screen and (max-width 465px) {
        & {
          height 50%
          border solid 2px #ded8d8
          border-radius 0 0 20px 20px
        }
      }

      @media screen and (max-width 376px) {
        & {
          height 60%
          border-radius 0 0 20px 20px
        }
      }

      &:before {
        content ""
        position absolute
        top 0
        left 0
        width 100%
        height 100%
        background rgba(255, 255, 255, 0.7)
        backdrop-filter blur(5px)
        z-index 1
      }

      .background__circle.background__circle_dark {
        background-color #535c68
      }

      .circle {
        position absolute
        width 450px
        height 450px
        border-radius 100%
        background-color #2ED573

        @media screen and (max-width 465px) {
          & {
            width 250px
            height 250px
          }
        }

        &:first-child {
          width 550px
          height 550px
          bottom 0
          left 0
          transform translate(-40%, 30%)

          @media screen and (max-width 465px) {
            & {
              width 250px
              height 250px
            }
          }
        }

        &:nth-child(2) {
          top 0
          right 0
          transform translate(30%, -30%)
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
    }

    .body {
      display grid
      grid-template-rows repeat(auto-fill, 1fr)
      place-items center
      position relative
      width 100%
      height 100%

      &__header {
        display flex
        justify-content space-between
        align-items center
        position relative
        top 10px
        width 90%
        height 100%
        z-index 2

        @media screen and (min-device-width 376px) and (max-device-width 465px) {
          & {
            top -20%
          }
        }

        .theme {
          display flex
          align-items center
          justify-content center
          position relative
          cursor pointer
          z-index 2
        }
      }

      .track-reference {
        display grid
        grid-auto-rows repeat(auto-fit, 1fr)
        grid-row-gap 30px
        place-content center
        position relative
        width 100%
        height auto
        z-index 2

        @media screen and (max-width 465px) {
          & {
            top -35%
          }
        }

        @media screen and (max-width 376px) {
          & {
            top -15%
            grid-row-gap 0
          }
        }

        .slider {
          display flex
          position relative
          width 300px
          height 200px
          z-index 2
          overflow hidden

          &__list {
            display flex
            justify-content center
            align-items center
            list-style none

            .slider__item {
              padding 0 50px
              transition all .8s ease

              @media screen and (max-width 376px) {
                & {
                  padding 0 65px
                }
              }
            }
          }
        }

        .info-track {
          position relative
          z-index 2
          width 100%
          max-width 800px

          &__header {
            display flex
            justify-content center
            align-items center
          }

          .title.title_dark {
            color #A09F9FFF
          }

          .title {
            font normal 1.2rem sans-serif
            color #333
          }
        }
      }

      .control-panel {
        display grid
        grid-auto-rows repeat(auto-fill, 1fr)
        grid-row-gap 20px
        margin-bottom 20px
        place-items center
        position relative
        width 486px
        height auto
        z-index 2

        @media screen and (max-width 767px) {
          & {
            top -35%
            width 100%
            padding 0 20px
          }
        }

        @media screen and (orientation landscape) {
          width 486px
          top 0
        }

        @media screen and (max-width: 376px) {
          & {
            top -15%
            width 100%
            padding 0 20px
          }
        }

        .timeline {
          display flex
          justify-content space-between
          align-items center
          flex-direction column
          position relative
          width 100%
          height 40px
          z-index 2

          &__base.timeline__base_dark {
            background-color #353b48
          }

          &__base {
            position relative
            background #dbd5d5
            margin auto
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
              width 0
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
            justify-content space-between
            position relative
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
          position relative
          width 100%
          height 40px

          & .btn {
            display flex
            justify-content center
            align-items center
            cursor pointer
          }

          &__main {
            display flex
            justify-content space-between
            align-items center
            width 200px
            cursor default

            @media screen and (max-width: 376px) {
              & {
                width 170px
              }
            }
          }
        }
      }

      .sound {
        display block
        position absolute
        top 89%
        left 93%
        transform translate(-50%, -50%)
        z-index 2

        @media screen and (max-width: 1023px) {
          & {
            display none
          }
        }

        &:hover > .sound__slider-box {
          opacity 1
          visibility visible
        }

        &__icon-box {
          display flex
          position relative
          width 100%
          height 100%
          cursor pointer

          .sound__icon {
            margin auto
          }
        }

        &__slider-box {
          display flex
          position absolute
          top -36px
          left 50%
          transform translate(-50%, -50%) rotate(-90deg)
          height 30px
          opacity 0
          visibility hidden
          transition all .2s linear
          padding-left 10px

          .sound__progress {
            display block
            width 60px
            margin auto
          }
        }
      }
    }

    @media screen and (max-width 1023px) {
      & {
        width 100%
        height 100%
        border-radius 0
      }
    }
  }
}

.technology {
  display flex
  align-items center
  position relative

  &__name {
    margin-left 10px
  }

  .name {
    color #929090
    font normal 1rem "Roboto", sans-serif
  }
}

.progress {
  -webkit-appearance none
  border-radius 20px
  outline none
}

.progress::-webkit-slider-thumb {
  -webkit-appearance none
  visibility hidden
  opacity 0
  position relative
  width 13px
  height 13px
  margin-top -4px
  background-color #fff
  border-radius 50%
  box-shadow rgba(0, 0, 0, 0.24) -3px 0px 8px
  transition all .2s ease
}

.progress::-webkit-slider-runnable-track {
  width 100%
  height 5px
  cursor pointer
}

.progress:hover::-webkit-slider-thumb {
  visibility hidden
  opacity 1
}
</style>
