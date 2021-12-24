<template>
  <div id="app">
    <button @click="src = short">Short video</button>
    <button @click="src = long">Long video</button>

    <VideoPlayer :src="src"/>
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
    src: require('./assets/one.mp4'),
    short: require('./assets/one.mp4'),
    long: 'http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/Sintel.mp4',
  }),
});
</script>
