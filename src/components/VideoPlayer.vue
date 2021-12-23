<template>
  <div class="player">
    <video
      :src="src"
      ref="video"
      controls
      muted
      @play="handleStart"
      @pause="handleStop"
    />
    <input
        v-model="progress"
        ref="range"
        type="range"
        class="player__progress"
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
  props: {
    src: String,
  },

  data: () => ({
    progress: 0,
    interval: undefined as number | undefined,
  }),

  methods: {
    setCurrentTime() {
      const range = this.$refs.range as HTMLInputElement
      const video = this.$refs.video as HTMLMediaElement
      const { duration } = video

      video.currentTime = +range.value * duration / range.clientWidth
    },
    updateProgress() {
      const range = this.$refs.range as HTMLInputElement
      const video = this.$refs.video as HTMLMediaElement
      const { currentTime, duration } = video

      this.progress = range.clientWidth * currentTime / duration
    },
    handleStart(event: { target: HTMLMediaElement }) {
      const { duration } = event.target
      const range = this.$refs.range as HTMLInputElement
      const frameTime = 1000 / (range.clientWidth / duration)

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
.player {
  max-width: 528px;
  margin: 32px auto;

  &__progress {
    width: calc(100% - 28px);
    margin: 0 14px;
  }
}

video {
  width: 100%;
}
</style>
