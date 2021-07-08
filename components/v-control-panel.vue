<template>
  <section class="panel">
    <div class="panel__shuffle">
      <img src="@/static/icons/shuffle.svg" alt="shuffle-icon">
    </div>
    <div class="main-btns">
      <div class="main-btns__previous-song">
        <img src="@/static/icons/previous-song.svg" alt="previous-song">
      </div>
      <div class="main-btns__play-song" v-if="isPlayed === true" @click="played">
        <img src="@/static/icons/play.svg" alt="play-btn">
      </div>
      <div class="main-btns__pause-song" v-else @click="stop">
        <img src="@/static/icons/pause.svg" alt="pause-btn">
      </div>
      <div class="main-btns__next-song">
        <img src="@/static/icons/next-song.svg" alt="next-song">
      </div>
    </div>
    <div class="panel__repeat">
      <img src="@/static/icons/repeat.svg" alt="repeat">
    </div>
  </section>
</template>

<script>
export default {
  name: "v-control-panel",
  data: () => ({
    isPlayed: true
  }),
  methods: {
    played() {
      this.isPlayed = false
      this.$emit('playAnimation')
    },
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
  width 400px
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
