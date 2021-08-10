<template>
  <div class="slider__slide">
    <img class="album" :src="`/albums/${data.album}.jpg`" alt="album">
  </div>
</template>

<script>
export default {
  props: ['data'],
  name: "v-slider"
}
</script>

<style scoped lang="stylus">
.slider__slide {
  position relative
  border-radius 18px
  width 200px
  height 200px
  overflow hidden

  .album {
    position absolute
    top 0
    left 0
    width 100%
    height 100%
  }
}

</style>
