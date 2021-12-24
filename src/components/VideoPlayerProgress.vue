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
    @click="updateProgress"
  />

  <div class="progress__bar">
    <div class="progress__elapsed" :style="`width: ${progress}px`"></div>
  </div>
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
      this.max = this.range.clientWidth

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
$height: 8px;
$bar-color: #B2B0B1;
$elapsed-color: #FF031C;

.progress {
  position: relative;

  &__range,
  &__bar {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    margin: 0;
    padding: 0;
    height: $height;
    width: 100%;
  }

  &__range {
    cursor: pointer;
    opacity: 0;
  }

  &__bar {
    pointer-events: none;
    background-color: $bar-color;
    border-radius: 4px;
    overflow: hidden;
  }

  &__elapsed {
    height: 100%;
    background-color: $elapsed-color;
    border-radius: 4px 0 0 4px;
  }
}
</style>
