<template lang="pug">
  .slider.triangle.wrap(v-bind:style="attributes")
    .slide(v-for="slide in attr.slides", v-bind:style="'background-size:' + slide.imgWidth * attr.scaleX + 'px ' + slide.imgHeight * attr.scaleY + 'px' + ';background-image: url(' + slide.url + '); background-position:' + slide.offsetX + 'px ' + slide.offsetY + 'px; opacity: 0;'")
      .video-wrapper(v-if="slide.video")
        video(loop, v-bind:id="'video-' + slide.id")
          source(v-bind:src="slide.video")
</template>

<script>
import Css from 'object-to-css'
export default {
  data () {
    return {
      timer: null
    }
  },
  props: ['attr'],
  created () {
  },
  beforeDestroy () {
    if (this.timer) {
      clearTimeout(this.timer)
    }
  },
  mounted () {
    if (this.attr.slides) {
      this.slider(this.attr.slides)
    }
  },
  methods: {
    slider (object) { // 從 0 開始
      var context = this.$el.children
      // 全部設定
      for (var index = 0; index < object.length; index++) {
        context[index].style.transitionProperty = 'all'
        context[index].style.transitionDuration = object[index].transitionTime + 's'
        context[index].style.transitionTimingFunction = 'linear'
        context[index].style.opacity = 0
      }
      var i = 0
      this.timer = setTimeout(() => {
        // 顯示第一張
        if (object[0].leastTime === 0) {
          if (object[0].video) {
            document.getElementById('video-' + object[i].id).play()
          }
          context[0].style.opacity = 1
        } else {
          this.exeSlide(i, context, object)
        }
      }, 1100)
    },
    exeSlide (i, context, object) {
      context[i].style.opacity = 1
      // 如果是影片
      if (object[i].video) {
        document.getElementById('video-' + object[i].id).load()
        document.getElementById('video-' + object[i].id).play()
      }
      this.timer = setTimeout(() => {
        // 準備淡出
        context[i].style.opacity = 0
        if (object[i].video) {
          document.getElementById('video-' + object[i].id).pause()
        }
        // 淡出完畢跳下一則
        if (object[i].leastTime === 0) {
          context[i].style.opacity = 1
          if (object[i].video) {
            document.getElementById('video-' + object[i].id).play()
          }
        } else {
          this.timer = setTimeout(() => {
            if (i === context.length - 1) {
              i = 0
            } else {
              i++
            }
            this.exeSlide(i, context, object)
          }, object[i].transitionTime * 1000)
        }
      }, object[i].leastTime * 1000 + object[i].transitionTime * 1000)
    }
  },
  computed: {
    attributes () {
      var style = {
        width: this.attr.width * this.attr.scaleX + 'px',
        height: this.attr.height * this.attr.scaleY + 'px',
        top: this.attr.top + 'px',
        left: this.attr.left + 'px',
        transform: 'rotate(' + this.attr.angle + 'deg)',
        'background-color': this.attr.fill,
        'clip-path': 'polygon(50% 0%, 0% 100%, 100% 100%)',
        '-webkit-clip-path': 'polygon(50% 0%, 0% 100%, 100% 100%)'
      }
      return Css(style)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
@import "../../../node_modules/susy/sass/susy";
@import "../../../node_modules/breakpoint-sass/stylesheets/breakpoint";
@import "../../../node_modules/bourbon/app/assets/stylesheets/bourbon";
@import "../../assets/scss/var";
</style>
