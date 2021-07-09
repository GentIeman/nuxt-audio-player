<template>
  <section class="slider">
    <div class="slider__slide" v-for="slide in sliderList" :key="slide.img">
      <img class="album" :src="`/albums/${slide.album}.jpg`" alt="slide">
      {{ slide.album }}
    </div>
  </section>
</template>

<script>
export default {
  name: "v-slider",
  data: () => ({
    sliderList: [
      {album: 'hate_me'},
      {album: 'shockwave'},
      {album: 'the_people_1991'}
    ]
  })
}
</script>

<style scoped lang="stylus">
.slider {
  display flex
  justify-content center
  align-items center
  position absolute
  top 45%
  left 50%
  transform translate(-50%, -50%)
  width 500px
  height 200px
  z-index 2

  &__slide {
    position relative
    width 200px
    height 200px
    border-radius 18px
    overflow hidden

    .album {
      position absolute
      top 0
      left 0
      width 100%
      height 100%
    }


    &:first-child,
    &:last-child {
      width 150px
      height 150px

      &:after {
        content ''
        position absolute
        top: 0
        left: 0
        width 100%
        height 100%
        background-color rgba(0,0,0,0.8)
      }
    }

    &:nth-child(2) {
      filter drop-shadow(0px 4px 4px rgba(0, 0, 0, 0.25))
      z-index 1
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
</style>
