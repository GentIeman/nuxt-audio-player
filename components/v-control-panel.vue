<template>
  <section class="panel">
    <div class="panel__shuffle">
      <img src="@/static/icons/shuffle.svg" alt="shuffle-icon" width="30px">
    </div>
    <div class="main-btns">
      <div class="main-btns__previous-song">
        <img src="@/static/icons/previous-song.svg" alt="previous-song" width="30px">
      </div>
      <div class="main-btns__play-song" v-if="isPlayed === true" @click="played">
        <img src="@/static/icons/play.svg" alt="play-btn" width="40px">
      </div>
      <div class="main-btns__pause-song" v-else @click="stop">
        <img src="@/static/icons/pause.svg" alt="pause-btn" width="40px">
      </div>
      <div class="main-btns__next-song">
        <img src="@/static/icons/next-song.svg" alt="next-song" width="30px">
      </div>
    </div>
    <div class="panel__repeat">
      <img src="@/static/icons/repeat.svg" alt="repeat" width="30px">
    </div>
  </section>
</template>

<script>
export default {
  name: "v-control-panel",
  data: () => ({
    isPlayed: true // Music playback and background animation
  }),
  methods: {
    // Start animation background
    played() {
      this.isPlayed = false
      this.$emit('playAnimation')
    },
    // Stop animation background
    stop() {
      this.isPlayed = true
      this.$emit('stopAnimation')
    }
  }
}
</script>

<style scoped lang="stylus">
.panel {
  display flex
  justify-content space-between
  align-items center
  position absolute
  bottom 0
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
</style>
