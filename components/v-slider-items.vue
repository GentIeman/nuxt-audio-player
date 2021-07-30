<template>
  <div class="slider__slide" :class="{'slider__slide_active': active}">
    <img class="album" :src="`/albums/${data.album}.jpg`" alt="album">
  </div>
</template>

<script>
export default {
  props: ['data', 'active'],
  name: "v-slider"
}
</script>

<style scoped lang="stylus">
.slider__slide {
  position relative
  border-radius 18px
  width 150px
  height 150px
  overflow hidden
  transition all .5s

  &:first-child {
    left 50px
  }

  &:last-child {
    right 50px
  }

  .album {
    position absolute
    top 0
    left 0
    width 100%
    height 100%
  }

  &:after {
    content ''
    position absolute
    top 0
    left 0
    width 100%
    height 100%
    background-color rgba(0, 0, 0, 0.6)
  }
}

.slider__slide_active {
  width 200px
  height 200px
  z-index 3

  &:after{
    display none
  }
}
</style>
