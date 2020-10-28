<template lang='pug'>
main(@mousemove='mouseMove' ref='main')
  .stage
    Loader(:value='loadedImgCount' :total='length')
    .glow(:key='score')
    .view
      .band(:style='bandStyle')
        img(v-for='img in imgs' :src='img' @load='loaded')
  Score.score(:value='score')

  iframe(src='https://ghbtns.com/github-btn.html?user=ngseke&repo=han-fans-eye-poke&type=star&count=false' frameborder='0' scrolling='0' width='54' height='20' title='GitHub')
</template>

<script>
import Loader from '~/components/Loader.vue'
import Score from '~/components/Score.vue'
import imgs from './img/*.jpg'
const localStorageKey = 'han-fans-eye-poke-score'

export default {
  data () {
    return {
      percentage: 0,
      score: this.loadScore(),
      loadedImgCount: 0,
      isMounted: false,
    }
  },
  components: {
    Loader,
    Score,
  },
  methods: {
    mouseMove (e) {
      this.percentage = e.clientX / window.innerWidth
    },
    loaded () {
      this.loadedImgCount++
    },
    loadScore () {
      const score = +localStorage.getItem(localStorageKey)
      return (Number.isNaN(score)) ? 0 : score
    },
    saveScore (score) {
      localStorage.setItem(localStorageKey, score)
    },
    resizeHeight () {
      document.body.style.height = window.innerHeight + 'px'
    }
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
      const width = (this.isMounted && this.$refs.main.offsetWidth < 720) ? this.$refs.main.offsetWidth : 720
      const x = width * -1 * this.current
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
            this.score++
            return true
          }
        }
      })
    },
    score (score) {
      this.saveScore(score)
    },
  },
  mounted () {
    this.isMounted = true
    this.resizeHeight()
    window.addEventListener("resize",  this.resizeHeight)
    this.$refs.main.ontouchstart = () => {
      const onTouchMove = (e) => {
        this.percentage = Math.round(e.touches[0].clientX) / window.innerWidth
      }
      const onTouchEnd = () => {
        this.$refs.main.ontouchmove = null
        this.$refs.main.ontouchend = null
      }
      this.$refs.main.ontouchmove = onTouchMove
      this.$refs.main.ontouchend = onTouchEnd
    }
  }
}
</script>

<style lang='sass'>
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
  margin-bottom: 2rem
  width: 100%
  max-width: 720px
  .glow
    position: absolute
    animation: poke .15s
    border-radius: 5px
    width: 100%
    height: 100%
    max-width: 720px
    z-index: 100

.view
  overflow: hidden
  border-radius: 5px
  width: 100%
  max-width: 720px
  .band
    display: flex
    flex-direction: row
    transform-origin: left center
    img
      width: 100%

.score
  color: white
  font-size: 2.5rem
  width: 100%
  text-align: center
  margin-bottom: 1rem
</style>
