<template>
  <div ref="range">
    <CustomRange
      :value="progress"
      :max="max"
      :step="0.001"
      @input="change"
    />
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import CustomRange from './CustomRange.vue'

const name = 'VideoPlayerProgress'
const SECOND = 1000

export default Vue.extend({
  name,

  components: {
    CustomRange,
  },

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
      this.video.addEventListener('loadstart', this.reset)
      this.max = this.range.clientWidth
    } else {
      console.warn('No video element found!')
    }
  },

  beforeDestroy() {
    if (this.video) {
      this.video.removeEventListener('play', this.start)
      this.video.removeEventListener('pause', this.stop)
      this.video.removeEventListener('loadstart', this.reset)
      clearInterval(this.interval)
    }
  },

  methods: {
    change(value: number) {
      const { clientWidth } = this.range
      const currentTime = Number(value) * this.video.duration / clientWidth

      this.$emit('change', currentTime)
      this.start()
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
    reset() {
      this.progress = 0

      this.$emit('change', 0)
      this.stop()
    },
  }
});
</script>
