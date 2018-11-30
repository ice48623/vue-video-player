# Vue-Video-Player
Video.js for Vue.js

# Dependency
- [video.js](https://github.com/videojs/video.js)
- [videojs-resolution-switcher](https://github.com/kmoskwiak/videojs-resolution-switcher)
- [videojs-contrib-hls](https://github.com/videojs/videojs-contrib-hls)
- [videojs-youtube](https://github.com/videojs/videojs-youtube)
- [videojs-vimeo](https://github.com/videojs/videojs-vimeo)

# Example
Demo page from original [vue-video-player](https://github.com/surmon-china/vue-video-player)

[Demo Page](https://surmon-china.github.io/vue-video-player)


# Use Setup

### Install vue-video-player

``` bash
npm install ice48623/vue-video-player --save
```

``` javascript
// import with ES6
import Vue from 'vue'
import VideoPlayer from 'vue-video-player'

// require with Node.js/Webpack
var Vue = require('vue')
var VideoPlayer = require('vue-video-player')

// The default is to turn off some of the features, you can choose according to their use of certain features enabled, do not enable the introduction will not require the corresponding file.

// You can configure the global function switch (of course, will be covered by local switches), where non-mandatory

VideoPlayer.config({
  youtube: true,  // default false
  switcher: true, // default true
  hls: true       // default true
})

// use
Vue.use(VideoPlayer)

// --------------------------------------

// or use with component(ES6)
import Vue from 'vue'
import { videoPlayer } from 'vue-video-player'

// use
export default {
  components: {
    videoPlayer
  }
}
```

``` html
<!-- Use in component(Vue.js1.X && Vue.js2.X) -->
<video-player :options="videoOptions"></video-player>

<!-- Use in component(Vue.js1.X && Vue.js2.X && function switch config) -->
<video-player :options="videoOptions" :configs="{ youtube: true }"></video-player>

<!-- Use in component(Vue.js2.X) && event callback -->
<video-player :options="videoOptions" @player-state-changed="playerStateChanged"></video-player>

<!-- Use in component(Vue.js2.X) && custom event name && ref property-->
<video-player :options="videoOptions" @my-player-state-changed-event-custom-name="playerStateChanged" ref="myPlayer"></video-player>
```

``` javascript
// base - player config example
export default {
  data () {
    return {
       videoOptions: {
        source: {
          type: "video/webm",
          src: 'https://cdn.theguardian.tv/webM/2015/07/20/150716YesMen_synd_768k_vp8.webm',
          // if you need custom player state changed event name, you can config it like this
          customEventName: 'my-player-state-changed-event-custom-name'
        }
      }
    }
  }
}

// playbackRates switch and sources switch - player config example
export default {
  data () {
    return {
      videoOptions: {
        source: [
          { type: "video/mp4", src: 'http://example.com/sample_video_H.mp4', label: '原画', res: 1 },
          { type: "video/mp4", src: 'http://example.com/sample_video_M.mp4', label: '高清', res: 2 },
          { type: "video/mp4", src: 'http://example.com/sample_video_L.mp4', label: '流畅', res: 3 }
        ],
        language: 'zh-CN',
        playbackRates: [0.7, 1.0, 1.3, 1.5, 1.7],
        poster: 'http://cn.vuejs.org/images/logo.png',
        defaultSrcReId: 2
      }
    }
  }
}

// live - player config example
export default {
  data () {
    return {
       videoOptions: {
        source: {
          type: 'application/x-mpegURL',
          src: 'https://example.net/live/playlist.m3u8',
          withCredentials: false
        },
        live: true
      }
    }
  }
}

// youtube - player config example
export default {
  data () {
    return {
      videoOptions: {
        source: {
          type: "video/youtube",
          src: "https://www.youtube.com/watch?v=iD_MyDbP_ZE"
        },
        techOrder: ["youtube"],
        autoplay: false,
        controls: false,
        ytControls: true
      }
    }
  }
}

// vimeo - player config example
export default {
  data () {
    return {
      videoOptions: {
        source: {
          type: "video/vimeo",
          src: "https://vimeo.com/167054481"
        },
        techOrder: ["vimeo"],
        autoplay: false,
        controls: false
      }
    }
  }
}

//-------------------------------------------------------------
// player state changed callback event

// events with Vue.js1.x
export default {
  events: {
    playerStateChanged(playerCurrentState) {
      console.log(playerCurrentState)
    }
  }
}

// methods with Vue.js2.x
export default {
  methods: {
    playerStateChanged(playerCurrentState) {
      console.log(playerCurrentState)
    }
  }
}

//-------------------------------------------------------------
// get current player object in parent component

export default {
  computed: {
    player() {
      return this.$refs.myPlayer.player
    }
  },
  mounted: {
    console.log('this is current player object', this.player)
    this.player.pause()
    // and do something...
  }
}
```

[More Code Example](https://surmon-china.github.io/vue-video-player)


# API

| protype        | type | description | example |
| :------------- |:---------------|:---------------| :------ |
| playsinline    | Boolean      |  the player default play auto fullscreen in IOS(!safari) (Y/n) default: true |       |
| source         | Object/Array |  the player source src(required) |       |
| muted          | Boolean      |  default: false                     |       |
| autoplay       | Boolean      |  default: true                    |       |
| start          | Number       |  player start time(default: 0)  |       |
| live           | Boolean      |  player is live? |
| playbackRates  | Array        |  player play backrates | [0.7, 1.0, 1.3, 1.5, 1.7] |
| defaultSrcReId | Number       |  When there are multiple sources, used to specify the default source (default:　1) |
| controls       | Boolean      |  player controls display or hidden |
| preload        | Boolean      |  player preload ? |
| poster         | String       |  player poster(default: '') | 'http://adasd.jpg' / 'data:image/png;base64,iVB...' |
| width          | Number       |  player width (default: 100%) |
| height         | Number       |  player height (default: 360) |
| controlBar     | Object       |  player controlBar dsipaly config | need to video.js api doc
| language       | String       |  player language(default: 'en') |
| techOrder      | Array        |  player support video type (default: example) | ['html5', 'flash', 'youtube', 'vimeo'] |
| customEventName| String       |  player state changed event name (default: example) | 'player-state-changed' |


# Credits

[video.js](https://github.com/videojs/video.js)

[video.js api](http://docs.videojs.com/docs/api/player.html#Methodsmuted)

[videojs-resolution-switcher](https://github.com/kmoskwiak/videojs-resolution-switcher)

[videojs-contrib-hls](https://github.com/videojs/videojs-contrib-hls)

[videojs-youtube](https://github.com/videojs/videojs-youtube)

[videojs-vimeo](https://github.com/videojs/videojs-vimeo)

[vue-video-player](https://github.com/surmon-china/vue-video-player)
