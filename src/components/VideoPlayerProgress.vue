<template>
<div class="progress">
  <input
      v-model="progress"
      ref="range"
      type="range"
      class="progress__range"
      step="0.001"
      max="500"
      @input="setCurrentTime"
  />
</div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  name: 'VideoPlayer',

  data: () => ({
    progress: 0,
    interval: undefined as number | undefined,
  }),

  computed: {
    video(): HTMLVideoElement {
      return this.$parent.$refs.video as HTMLVideoElement
    }
  },

  mounted() {
    this.video.addEventListener('play', e => this.handleStart(e as any))
    this.video.addEventListener('pause', () => this.handleStop())
  },

  methods: {
    setCurrentTime() {
      const range = this.$refs.range as HTMLInputElement
      const { duration } = this.video

      this.$emit('change', +range.value * duration / range.clientWidth)
    },
    updateProgress() {
      const range = this.$refs.range as HTMLInputElement
      const { currentTime, duration } = this.video

      this.progress = range.clientWidth * currentTime / duration
    },
    handleStart(event: { target: HTMLMediaElement }) {
      const { duration } = event.target
      const range = this.$refs.range as HTMLInputElement
      const frameTime = 1000 / (range.clientWidth / duration)

      this.handleStop()
      this.updateProgress()
      this.interval = setInterval(this.updateProgress, frameTime)
    },
    handleStop() {
      clearInterval(this.interval)
    },
  }
});
</script>

<style scoped lang="scss">
.progress {
  &__range {
    width: 100%;
    outline: 1px dashed;
  }
}
</style>
