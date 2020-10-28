<template lang="pug">
.score(:key='value')
  span.digit(
    v-for='(i, index) in digits'
    :key='index'
    :style='{ animationDelay: `${index * .1}s` }'
  ) {{ i }}
</template>

<script>
export default {
  props: {
    value: Number
  },
  computed: {
    digits () {
      return [...new Intl.NumberFormat('en-US').format(this.value)]
    }
  }
}
</script>

<style lang="sass" scoped>
@keyframes digit
  0%
    opacity: 0
    transform: translateY(-50%)
  50%
    opacity: .7
    transform: translateY(20%)
  100%
    opacity: 1
    transform: none

.score
  font-size: 2.5rem
  font-weight: bold
  font-family: Verdana, Geneva, sans-serif
  color: white
  .digit
    display: inline-block
    transform-origin: center center
    animation: digit .3s both
</style>
