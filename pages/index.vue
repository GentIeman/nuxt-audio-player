<template>
  <section class="page">
    <section class="base">
      <div class="base_circle circle" :class="{'slide-up' : isAnimate}"></div>
      <div class="base_circle circle " :class="{'slide-down' : isAnimate}"></div>
      <div class="technology">
        <img class="technology__logo" src="@/static/icons/nuxt.svg" alt="">
        <p class="technology__text text">Create with Nuxt.js</p>
      </div>
      <section class="slider">
        <transition-group name="carousel-transition" class="slider__body">
          <div class="slider__slide" v-for="slide in sliderList" :key="slide.id">
            <img v-if="slide" class="album" :src="`/albums/${slide.album}.jpg`" alt="slide">
          </div>
        </transition-group>
      </section>
      <section class="timeline">
        <input type="range" class="timeline__progress" :max="audioDuration" v-model="playbackTime" >
        <div class="time-code">
          <span class="begin-time time" v-html="currentTime()"> 00:00 </span>
          <span class="end-time time" v-html="totalTime()"> 00:00 </span>
        </div>
      </section>
      <audio id="audio-player" ref="player" controls
             src="https://file-examples-com.github.io/uploads/2017/11/file_example_MP3_5MG.mp3">
        Your browser does not support audio tag.
      </audio>
      <section class="panel">
        <div class="panel__shuffle">
          <img src="@/static/icons/shuffle.svg" alt="shuffle-icon" width="30px">
        </div>
        <div class="main-btns">
          <div class="main-btns__previous-song">
            <img src="@/static/icons/previous-song.svg" alt="previous-song" width="30px" @click="proveSong()">
          </div>
          <div class="main-btns__play-song" v-if="isPlayed === false" @click="playToggle()">
            <img src="@/static/icons/play.svg" alt="play-btn" width="40px">
          </div>
          <div class="main-btns__pause-song" v-if="isPlayed === true" @click="playToggle()">
            <img src="@/static/icons/pause.svg" alt="pause-btn" width="40px">
          </div>
          <div class="main-btns__next-song">
            <img src="@/static/icons/next-song.svg" alt="next-song" width="30px" @click="nextSong()">
          </div>
        </div>
        <div class="panel__repeat">
          <img src="@/static/icons/repeat.svg" alt="repeat" width="30px">
        </div>
      </section>
    </section>
  </section>
</template>

<script>
export default {
  data: () => ({
    isAnimate: false, // the variable is responsible for the animation
    currentSlideIndex: 1,
    slideList: [
      {id: 0, album: 'beneath_the_trees', title: 'Beneath the Trees', src: 'Beneath the Trees - mell-ø', author: 'mello-Ø'},
      {id: 1, album: 'one_quiet_evening', title: 'One Quiet Evening', src: 'December - Magic Mondays', author: 'wood.'},
      {id: 2, album: 'december', title: 'December', src: 'One Quiet Evening - wood', author: 'Magic Mondays'}
    ]
  }),
  methods: {
    nextSong() {
      let firstElem = this.slideList.shift()
      this.slideList.push(firstElem)
    },
    proveSong() {
      let lastElem = this.slideList.pop()
      this.slideList.unshift(lastElem)
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

        .slider__slide {
          position relative
          border-radius 18px
          width 150px
          height 150px
          overflow hidden
          transition all .5s

          &:nth-child(even) {
            filter drop-shadow(0px 4px 4px rgba(0, 0, 0, 0.25))
            z-index 1
            width 200px
            height 200px
          }

          .album {
            position absolute
            top 0
            left 0
            width 100%
            height 100%
          }


          &:first-child,
          &:last-child {

            &:after {
              content ''
              position absolute
              top: 0
              left: 0
              width 100%
              height 100%
              background-color rgba(0, 0, 0, 0.8)
            }
          }

          &:first-child {
            position relative
            left 40px
          }

          &:last-child {
            position relative
            right 40px
          }
        }
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

      &__progress {
        display flex
        align-items center
        justify-content flex-end
        position absolute
        width 100%
        height 100%
        border-radius 6px
      }

      .time-code {
        display flex
        position absolute
        top -25px
        justify-content space-between
        width 100%
        height auto

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
    transition: transform 30s;
  }

  input[type=range] {
    -webkit-appearance none
  }


  input[type=range]::-webkit-slider-thumb {
    display block
    position relative
    appearance none
    width 12px
    height 12px
    border-radius 50%
    background #1DD1A1
    cursor pointer
  }
}

</style>
