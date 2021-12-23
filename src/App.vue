<template>
  <div id="app">
    <VideoPlayer :src="src2"/>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import VideoPlayer from './components/VideoPlayer.vue';

export default Vue.extend({
  name: 'App',
  components: {
    VideoPlayer
  },
  data: () => ({
    src1: require('./assets/one.mp4'),
    src2: require('./assets/two.mp4'),
  }),
});
</script>
