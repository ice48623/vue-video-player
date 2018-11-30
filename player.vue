<template>
  <div class="video-player">
    <video class="video-js vjs-custom-skin" :class="{ 'live': options.live }"></video>
  </div>
</template>

<script>
  var languages = require('./languages.js')
  export default {
    name: 'video-player',
    data: function () {
      return {
        customEventName: 'player-state-changed'
      }
    },
    props: {
      configs: {
        type: Object,
        default: function () {
          return {
            youtube: false,
            vimeo: false,
            switcher: true,
            hls: true
          }
        }
      },
      options: {
        type: Object,
        required: true
      },
    },

    created: function () {
      if (this.$parent) {
        var _this = this
        this.$parent.$on('playerAction', function (action, options){
          _this.doAction(action, options);
        })
      }
    },
    ready: function() {
      if (this.options) { this.initialize() }
    },
    mounted: function() {
      if (this.options) { this.initialize() }
    },
    beforeDestroy: function() {
      this.dispose()
    },
    methods: {
      initialize: function() {

        // console.log('init build player')

        var configs = this.configs
        if (configs.hls) require('videojs-contrib-hls/dist/videojs-contrib-hls.js')
        if (configs.youtube) require('videojs-youtube')
        if (configs.vimeo) require('videojs-vimeo')
        if (configs.switcher) require('videojs-resolution-switcher')

        // init
        var options = this.options
        // start_time
        options.start = options.start || 0
        // is_live?
        options.live  = options.live || false
        // player_src
        options.source  = options.source || false
        // playbackRates [0.7, 1.0, 1.5, 2.0]
        options.playbackRates = options.playbackRates || false
        // player defaultSrcReId
        options.defaultSrcReId = options.defaultSrcReId || 1
        // default muted
        options.muted = options.muted || false
        // playsinline
        options.playsinline = options.playsinline !== undefined ? options.playsinline : true;

        var customEventName = options.customEventName || this.customEventName

        if (typeof options.source !== 'object') {
          this.dispose()
          return console.error('video resource must be a object or array')
        } else {
          if (options.source instanceof Array) {
            for (var i = 0, length = options.source.length; i < length; i++) {
              var item = options.source[i];
              if (!item.src) {
                this.dispose()
                return console.warn('video resource is illegitimate')
              }
            }
          } else {
            if (!options.source.src) {
              this.dispose()
              return console.warn('video resource is illegitimate')
            }
          }
        }

        var controlBar = {
          remainingTimeDisplay: false,
          playToggle: {},
          progressControl: {},
          fullscreenToggle: {},
          volumeMenuButton: {
            inline: false,
            vertical: true
          }
        }

        if (options.live) {
          controlBar.timeDivider = false
          controlBar.durationDisplay = false
          controlBar.currentTimeDisplay = false
        }

        // build player config
        var video_options = {
          'controls': options.controls !== undefined ? options.controls : true,
          'autoplay': options.autoplay !== undefined ? options.autoplay : true,
          'preload': options.preload || 'auto',
          'poster': options.poster ||  '',
          'width': options.width || '100%',
          'height': options.height || 360,
          'fluid': options.fluid || false,
          'controlBar': options.controlBar || controlBar,
          'language': options.language || 'en',
          'techOrder': options.techOrder || ['html5', 'flash'],
          'flash': { hls: { withCredentials: false }},
          'html5': { hls: { withCredentials: false }},
          'youtube': { "ytControls": options.ytControls ? Number(options.ytControls) : 0 }
        }

        var language = video_options.language
        videojs.addLanguage(language, languages[language])

        var playsinline = options.playsinline
        playsinline && (this.$el.children[0].setAttribute('playsinline', playsinline),this.$el.children[0].setAttribute('webkit-playsinline', playsinline))

        if (!options.live) {

          if (!!options.source.src) {
            video_options.sources = [options.source]

          } else {
            video_options.plugins = { videoJsResolutionSwitcher: { default: options.defaultSrcReId, dynamicLabel: true }}
          }

          var playbackRates = options.playbackRates
          if (!!playbackRates && !!playbackRates.length) video_options.playbackRates = playbackRates
        }

        var _this = this
        this.player = null
        this.player = videojs(this.$el.children[0], video_options, function() {

          if (!options.live) {
            if (!!options.source.length) {
              this.updateSrc(options.source)
              this.on('resolutionchange', function(){
                _this.$emit && _this.$emit(customEventName, { resolutionchange: this.src() })
                _this.$dispatch && _this.$dispatch(customEventName, { resolutionchange: this.src() })
              })
            }
          }

          if (options.live) {
            this.src(options.source)
          }

          this.on('play', function() {
            _this.$emit && _this.$emit(customEventName, { play: true })
            _this.$dispatch && _this.$dispatch(customEventName, { play: true })
          })

          this.on('pause', function() {
            _this.$emit && _this.$emit(customEventName, { pause: true })
            _this.$dispatch && _this.$dispatch(customEventName, { pause: true })
          })

          this.on('ended', function() {
            _this.$emit && _this.$emit(customEventName, { ended: true })
            _this.$dispatch && _this.$dispatch(customEventName, { ended: true })
          })

          this.on('loadeddata', function() {
            if (!options.live && !!options.start) this.currentTime(options.start)
            this.muted(_this.options.muted)
            _this.$emit && _this.$emit(customEventName, { loadeddata: true })
            _this.$dispatch && _this.$dispatch(customEventName, { loadeddata: true })
          })

          this.on('waiting', function() {
            _this.$emit && _this.$emit(customEventName, { waiting: true })
            _this.$dispatch && _this.$dispatch(customEventName, { waiting: true })
          })

          this.on('playing', function() {
            _this.$emit && _this.$emit(customEventName, { playing: true })
            _this.$dispatch && _this.$dispatch(customEventName, { playing: true })
          })

          this.on('timeupdate', function() {
            _this.$emit && _this.$emit(customEventName, { currentTime: this.currentTime() })
            _this.$dispatch && _this.$dispatch(customEventName, { currentTime: this.currentTime() })
          })

          this.on('canplay', function() {
            _this.$emit && _this.$emit(customEventName, { canplay: true })
            _this.$dispatch && _this.$dispatch(customEventName, { canplay: true })
          })

          this.on('canplaythrough', function() {
            _this.$emit && _this.$emit(customEventName, { canplaythrough: true })
            _this.$dispatch && _this.$dispatch(customEventName, { canplaythrough: true })
          })
        })
      },

      dispose: function() {
        if (!!this.player && !!videojs) {
          // this.player.dispose()
          this.player.pause && this.player.pause()
          videojs(this.$el).dispose()
          delete this.player
        }
      },

      doAction: function(action, options) {
        if (!this.player) return
        if (action == 'sliderDrag') this.player.currentTime(options.currentTime);
        if (action == 'play') this.player.play()
        if (action == 'pause') this.player.pause()
        if (action == 'refresh') {
          if (!this.options.live) {
            this.player.currentTime(0)
            this.player.play()
          }
        }
      }
    },

    events: {
      'playerAction': function(action) {
        this.doAction(action)
      }
    },

    watch: {
      options: {
        handler: function (newVal, oldVal) {
          var options = newVal
          if (typeof options.source !== 'object') {
            this.dispose()
            return console.error('video resource must be a object or array')
          } else {
            if (options.source instanceof Array) {
              for (var i = 0, length = options.source.length; i < length; i++) {
                var item = options.source[i]
                if (!item.src) {
                  this.dispose()
                  return console.warn('video resource is illegitimate')
                }
              }
            } else {
              if (!options.source.src) {
                this.dispose()
                return console.warn('video resource is illegitimate')
              }
            }
          }
          if (this.player) this.player.src(this.options.source)
          if (!this.player) this.initialize()
        },
        deep: true
      }
    }
  }
</script>

<style src="./player.css"></style>
