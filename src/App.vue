<template lang='pug'>
main(@mousemove='mouseMove')
  .stage
    Loader(:value='loadedImgCount' :total='length')
    .glow(:key='count')
    .view
      .band(:style='bandStyle')
        img(v-for='img in imgs' :src='img' @load='loaded')
  .count {{ count }}

  iframe(src='https://ghbtns.com/github-btn.html?user=ngseke&repo=han-fans-eye-poke&type=star&count=false' frameborder='0' scrolling='0' width='54' height='20' title='GitHub')
</template>

<script>
import Loader from '~/components/Loader.vue'
import imgs from './img/*.jpg'
const localStorageKey = 'han-fans-eye-poke-count'

export default {
  data: () => ({
    percentage: 0,
    popup: false,
    count: localStorage.getItem(localStorageKey) || 0,
    loadedImgCount: 0,
  }),
  components: {
    Loader,
  },
  methods: {
    mouseMove (e) {
      this.percentage = e.clientX / window.innerWidth
    },
    loaded () {
      this.loadedImgCount++
    },
  },
  computed: {
    imgs () {
      return Object.keys(imgs)
        .map(_ => +_)
        .sort((a, b) => a - b)
        .map(key => imgs[`${key}`.padStart(3, 0)])
    },
    length () {
      return this.imgs.length
    },
    current () {
      return ~~(this.length * this.percentage) + 1
    },
    bandStyle () {
      const x = -1040 * this.current
      return {
        transform: `translateX(${x}px)`,
      }
    },
  },
  watch: {
    current (value, oldValue) {
      if (oldValue > value) return

      const timeCodes = [50, 101, 134, 202, 268, 303, 354, 396, 459, 497, 529, 581, 620]
      timeCodes.some(code => {
        const tolerance = 3
        if ((code - tolerance <= value) && (value <= code + tolerance)) {
          if (code !== this.previousMatch) {
            this.previousMatch = code
            this.count++
            localStorage.setItem(localStorageKey, this.count)
            return true
          }
        }
      })
    },
  },
}
</script>

<style lang='sass'>
$width: 1040px
$height: 480px
$shadow-color: white

@keyframes poke
  from
    box-shadow: 0 0 30px 0px $shadow-color
  to
    box-shadow: 0 0 30px 0px rgba($shadow-color, 0)

=flex-center
  display: flex
  justify-content: center
  align-items: center

body
  +flex-center
  margin: 0
  height: 100vh
  background-color: #1e1e1e
  user-select: none

main
  +flex-center
  width: 100%
  flex-direction: column

.stage
  position: relative
  width: $width
  height: $height
  margin-bottom: 2rem
  .glow
    position: absolute
    animation: poke .15s
    border-radius: 5px
    width: 100%
    height: 100%
    z-index: 100

.view
  overflow: hidden
  border-radius: 5px
  width: $width
  height: $height
  .band
    display: flex
    flex-direction: row
    transform-origin: left center

.count
  color: white
  font-size: 2.5rem
  width: 100%
  text-align: center
  margin-bottom: 1rem
</style>
