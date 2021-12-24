<template>
  <div class="player">
    <video :src="src" ref="video" controls muted />
    <VideoPlayerProgress class="player__progress" @change="setCurrentTime" />
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import VideoPlayerProgress from './VideoPlayerProgress.vue'

export default Vue.extend({
  name: 'VideoPlayer',

  components: {
    VideoPlayerProgress
  },

  props: {
    src: String,
  },

  methods: {
    setCurrentTime(time: number) {
      const video = this.$refs.video as HTMLMediaElement

      video.currentTime = time
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
