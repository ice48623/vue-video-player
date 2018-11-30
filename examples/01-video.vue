<template>
  <v-container>
    <h3>vue-video-player</h3>
    <video-player :options="videoOptions" ref="videoPlayer"></video-player>
  </v-container>
</template>

<script>
  export default {
    name: '',
    data() {
      return {
        videoOptions: {
          source: [
            {
              src: 'https://vjs.zencdn.net/v/oceans.mp4?SD',
              type: 'video/mp4',
              label: 'SD',
              res: 360
            },
            {
              src: 'https://vjs.zencdn.net/v/oceans.mp4?HD',
              type: 'video/mp4',
              label: 'HD',
              res: 720
            }
          ],
          playbackRates: [0.7, 1.0, 1.3, 1.5, 1.7],
          poster: 'http://cn.vuejs.org/images/logo.png',
          defaultSrcReId: 2
        }
      }
    },
    computed: {
      player() {
        return this.$refs.videoPlayer.player
      },
    },
  }
</script>
