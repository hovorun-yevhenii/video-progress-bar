<template>
<div class="progress">
  <input
    ref="range"
    type="range"
    class="progress__range"
    step="0.001"
    :value="progress"
    :max="max"
    @input="change"
  />
</div>
</template>

<script lang="ts">
import Vue from 'vue'

const name = 'VideoPlayerProgress'
const SECOND = 1000

export default Vue.extend({
  name,

  data: () => ({
    progress: 0,
    max: 0,
    interval: undefined as number | undefined,
  }),

  computed: {
    video(): HTMLVideoElement {
      return this.$parent.$el.querySelector('video') as HTMLVideoElement
    },
    range(): HTMLInputElement {
      return this.$refs.range as HTMLInputElement
    },
  },

  mounted() {
    if (this.video) {
      this.video.addEventListener('play', this.start)
      this.video.addEventListener('pause', this.stop)

      this.$watch(() => this.video.src, (src) => {
        // todo find out why it doesn't work and reset the progress
        console.log(src)
      });
    } else {
      console.warn('No video element found!')
    }
  },

  beforeDestroy() {
    if (this.video) {
      this.video.removeEventListener('play', this.start)
      this.video.removeEventListener('pause', this.stop)
      clearInterval(this.interval)
    }
  },

  methods: {
    change() {
      const { value, clientWidth } = this.range
      const currentTime = Number(value) * this.video.duration / clientWidth

      this.$emit('change', currentTime)
    },
    updateProgress() {
      const { currentTime, duration } = this.video
      const { clientWidth } = this.range

      this.max = clientWidth
      this.progress = clientWidth * currentTime / duration
    },
    start() {
      /**
        'frameTime' is optimal interval for range value updating:
        the longer video - the lowest frequency
      */
      const frameTime = SECOND / (this.range.clientWidth / this.video.duration)

      this.stop()
      this.updateProgress()
      this.interval = setInterval(this.updateProgress, frameTime)
    },
    stop() {
      clearInterval(this.interval)
    },
  }
});
</script>

<style scoped lang="scss">
.progress {
  &__range {
    width: 100%;
  }
}
</style>
