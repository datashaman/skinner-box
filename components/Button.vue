<template>
  <div ref="button" class="button">
    <fa :icon="button.icon.type" :style="button.icon.style" />
    <div class="font-bold text-base">{{ count }} / {{ cps }}</div>
  </div>
</template>
<script>
import kefir from '~/mixins/kefir'

export default {
  mixins: [kefir],
  props: {
    room: {
      type: Object,
      default: () => {},
    },
    button: {
      type: Object,
      default: () => ({
        icon: {
          style: '',
        },
      }),
    },
  },
  data: () => ({
    count: 0,
    cps: 0,
  }),
  mounted() {
    const clicks = this.streamClickEvents(this.$refs.button, e => ({
      room: this.room,
      button: this.button,
    }))

    this.subscribe(
      clicks
        .map(e => 1)
        .scan((prev, next) => prev + next, 0)
        .observe(count => {
          this.count = count
        })
    )

    this.subscribe(
      this.bufferBySecond(clicks).observe(clicks => {
        this.cps = clicks.length
      })
    )
  },
}
</script>
<style scoped>
.button {
  @apply rounded-full h-48 w-48 cursor-pointer text-6xl text-center shadow-lg p-12 m-2 inline-block;
}
</style>
