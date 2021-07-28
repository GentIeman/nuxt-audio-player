<template>
  <div class="slider__body">
    <div class="slider__slide" v-for="slide in data" :key="slide.id" :class="{'active-slide' : activeSlide.id === slide.id }">
      <img v-if="slide" class="album" :src="`/albums/${slide.album}.jpg`" alt="album">
    </div>
  </div>
</template>

<script>
export default {
  props: ['data', 'activeSlide'],
  name: "v-slider",
  beforeMount() {
    console.log(this.data)
  }
}
</script>

<style scoped lang="stylus">
.slider__body {
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

    .album {
      position absolute
      top 0
      left 0
      width 100%
      height 100%

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

  .active-slide {
    width 200px
    height 200px
    z-index 3
  }
}
</style>
