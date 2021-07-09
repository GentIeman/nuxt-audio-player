<template>
  <section class="page">
    <section class="base">
      <div class="base_circle circle" :class="{'slide-up' : isAnimate}"></div>
      <div class="base_circle circle " :class="{'slide-down' : isAnimate}"></div>
      <div class="technology">
        <img class="technology__logo" src="@/static/icons/nuxt.svg" alt="">
        <p class="technology__text text">Create with Nuxt.js</p>
      </div>
      <v-control-panel @playAnimation="isAnimate = true" @stopAnimation="isAnimate = false"/>
      <v-slider />
    </section>
  </section>
</template>

<script>
export default {
  data: () => ({
    isAnimate: false // the variable is responsible for the animation
  })
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
  }
}

</style>
