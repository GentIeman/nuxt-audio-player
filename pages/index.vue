<template>
  <section class="page">
    <section class="base">
        <div class="base_circle circle" :class="{'slide-up' : isAnimate}"></div>
        <div class="base_circle circle " :class="{'slide-down' : isAnimate}"></div>
    </section>
<!--    <button @click="isAnimate = !isAnimate">Click me</button> Check button-->
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
    width 900px
    height 450px
    border-radius 18px
    border solid 1px #B7B3B3
    overflow hidden

    &:before {
      content ''
      position absolute
      top 0
      left 0
      width 100%
      height 100%
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
        transform translate(40%, 40%)
      }
    }
  }
}

</style>
