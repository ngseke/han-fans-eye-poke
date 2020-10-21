<template lang='pug'>
.loader(:style='loaderStyle' v-if='isShow')
  .progress(:style='progressStyle')
    .bar(:style='barStyle')
</template>

<script>
export default {
  data: () => ({
    isShow: true,
  }),
  props: {
    value: Number,
    total: Number,
  },
  computed: {
    percentage () {
      return this.value / this.total
    },
    loaderStyle () {
      const alpha = 1 - this.percentage
      return {
        backgroundColor: `rgba(0, 0, 0, ${alpha})`,
      }
    },
    progressStyle () {
      return {
        opacity: 1 - this.percentage,
      }
    },
    barStyle () {
      return {
        width: `${this.percentage * 100}%`,
      }
    },
  },
  watch: {
    percentage (value) {
      if (value === 1) setTimeout(() => this.isShow = false, 1000)
    },
  },
}
</script>

<style lang='sass' scoped>
.loader
  position: absolute
  z-index: 1
  width: 100%
  height: 100%
  display: flex
  justify-content: center
  align-items: center
  transition: background-color 1s
  .progress
    height: 3px
    width: 50px
    background-color: rgba(white, .5)
    border-radius: 5px
    overflow: hidden
    transition: opacity .2s
    .bar
      height: 100%
      background-color: rgba(white, 1)
      transition: width .2s
</style>
