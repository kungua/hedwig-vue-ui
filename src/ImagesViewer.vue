<template>
  <transition name="hdw-fade" v-if="visible">
    <div class="hdw-picture-viewer__wrapper">
      <!--      close icon-->
      <hdw-icon @click="$emit('update:current-index', -1)" name="CLOSE" class="closeIcon hdw-picture-viewer__icon"/>
      <!--      arrow icon-->
      <hdw-icon :class="{disabled: currentIndex===0}"
                @click="onClickLeft"
                name="left"
                class="leftIcon hdw-picture-viewer__icon"/>
      <hdw-icon :class="{disabled: currentIndex===imageList.length - 1}"
                @click="onClickRight"
                name="right"
                class="rightIcon hdw-picture-viewer__icon"/>
      <!--      toolbar-->
      <div class="hdw-picture-viewer__toolbar">
        <div class="zoom-options">
          <hdw-icon @click="handleActions('makeBigger')" name="add1" class=""/>
          <span class="text">{{Math.floor(transformParams.scale * 100)}}%</span>
          <hdw-icon @click="handleActions('makeSmaller')" name="minus" class=""/>
        </div>
        <hdw-icon @click="onRotate()" name="xuanzhuan1"/>
        <hdw-icon v-if="canDelete"
                  @click="$emit('delete', currentIndex)"
                  name="shanchu"/>
      </div>
      <!--      image area-->
      <div class="hdw-picture-viewer__imageArea">
        <div v-if="loading">
          <hdw-loading/>
        </div>
        <div class="error disabled" v-else-if="error">
          <hdw-icon name="fail"></hdw-icon>
          <div>图片不见啦</div>
        </div>
        <img
          v-else
          v-bind="$attrs"
          :src="currentImage"
          :style="imageStyles"
          @mousedown="onMousedown"
          class="hdw-image"/>
      </div>
    </div>
  </transition>
</template>

<script>
import Icon from './Icon'
import Loading from './Loading'

export default {
  name: 'HedwigPictureViewer',
  components: {
    HdwIcon: Icon,
    HdwLoading: Loading
  },
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    imageList: {
      type: Array,
      default: () => []
    },
    currentIndex: {
      type: Number,
      default: 0
    },
    canDelete: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      loading: true,
      error: false,
      transformParams: {
        scale: 1,
        animating: true,
        offsetX: 0,
        offsetY: 0
      }
    }
  },
  computed: {
    currentImage() {
      return this.imageList[this.currentIndex]
    },
    imageStyles() {
      const { scale, animating, offsetX, offsetY } = this.transformParams
      const style = {
        transform: `scale(${scale})`,
        transition: animating ? 'transform .3s' : '',
        marginLeft: `${offsetX}px`,
        marginTop: `${offsetY}px`
      }
      style.maxWidth = style.maxHeight = '100%'
      return style
    }
  },
  watch: {
    visible(val) {
      this.toggleVisible(val)
    },
    currentIndex(val) {
      if (val === -1) {
        return
      }
      this.loadImage()
    }
  },
  methods: {
    reset() {
      this.transformParams = {
        scale: 1,
        animating: true,
        offsetX: 0,
        offsetY: 0
      }
    },
    onMousedown(e) {
      const { pageX: startX, pageY: startY } = e
      const { offsetX, offsetY } = this.transformParams

      if (this.loading) return

      this.fn = (ev) => {
        this.transformParams.offsetX = offsetX + (ev.pageX - startX)
        this.transformParams.offsetY = offsetY + (ev.pageY - startY)
      }

      document.addEventListener('mousemove', this.fn)
      document.addEventListener('mouseup', () => {
        document.removeEventListener('mousemove', this.fn)
      }, { once: true })

      e.preventDefault()
    },
    handleActions(action) {
      if (this.loading || this.error) {
        return
      }
      const { transformParams } = this
      const zoomRate = 0.2
      switch (action) {
        case 'makeBigger':
          transformParams.scale = parseFloat((transformParams.scale + zoomRate).toFixed(3))
          break
        case 'makeSmaller':
          if (transformParams.scale > 0.2) {
            transformParams.scale = parseFloat((transformParams.scale - zoomRate).toFixed(3))
          }
          break
      }
    },
    onLoad() {
      this.loading = false
      this.error = false
    },
    onError() {
      this.loading = false
      this.error = true
    },
    loadImage() {
      this.loading = true
      this.error = false

      const img = new Image()
      img.onload = e => this.onLoad(e, img)
      img.onerror = e => this.onError(e, img)

      Object.keys(this.$attrs)
        .forEach((key) => {
          const value = this.$attrs[key]
          img.setAttribute(key, value)
        })
      img.src = this.currentImage

      this.reset()
    },
    onClickLeft() {
      if (this.currentIndex !== 0) {
        this.$emit('update:current-index', this.currentIndex - 1)
        this.reset()
      }
    },
    onClickRight() {
      if (this.currentIndex !== this.imageList.length - 1) {
        this.$emit('update:current-index', this.currentIndex + 1)
        this.reset()
      }
    },
    toggleVisible(val) {
      if (val) {
        document.querySelector('html').style.overflow = 'hidden'
        this.reset()
      } else {
        setTimeout(() => {
          document.querySelector('html').removeAttribute('style')
        }, 500)
      }
    },
    onRotate() {
      this.reset()
      const { currentIndex, imageList } = this

      const oldHref = imageList[currentIndex]

      if (!oldHref.split('?')[1]) {
        window.alert('不能旋转外部图片')
        return
      }
      const base = oldHref.split('?')[0]
      const querys = oldHref.split('?')[1].split('&')
      if (querys.indexOf('imageView') > -1) {
      } else {
        querys.push('imageView')
      }
      let find = false
      for (let i = 0; i < querys.length; i++) {
        const c = querys[i]
        if (c.indexOf('rotate') > -1) {
          find = true
          const rotate = parseInt(c.split('=')[1]) + 90
          querys[i] = `rotate=${rotate % 360}`
        }
      }
      if (!find) {
        querys.push('rotate=90')
      }
      const newHref = [base, querys.join('&')].join('?')
      console.log('newHref')
      console.log(newHref)

      let copy = JSON.parse(JSON.stringify(imageList))
      copy[currentIndex] = newHref
      this.$emit('update:image-list', copy)
      this.loadImage(newHref)
    }
  }
}
</script>

<style lang="scss" scoped>
  .hdw-picture-viewer {
    &__icon {
      position: absolute; z-index: 1;

      &:hover {
        fill: white;
      }

      &.disabled { opacity: .4; pointer-events: none; }
    }

    &__wrapper {
      z-index: 1200;
      position: fixed; top: 0; right: 0; bottom: 0; left: 0;
      background: rgba(0, 0, 0, 0.40);
      font-size: 45px;

      svg { fill: #e8e8e8; cursor: pointer; }

      .closeIcon {
        top: 90px;
        right: 30px;
      }

      .rightIcon, .leftIcon {
        font-size: 30px;
        top: 50%;
        transform: translateY(-50%);
      }

      .rightIcon {
        right: 40px;
      }

      .leftIcon {
        left: 40px;
      }
    }

    &__toolbar {
      user-select: none; z-index: 1;
      position: absolute; bottom: 40px;
      width: 410px; height: 50px; left: 50%; transform: translateX(-50%);
      background: #171717; border-radius: 4px;
      font-size: 24px; color: #fff;
      padding: 0 50px;
      display: flex; justify-content: space-between; align-items: center;

      .zoom-options {
        .text {
          font-size: 14px;
          vertical-align: middle;
          display: inline-block; width: 56px; text-align: center;
        }
      }
    }

    &__imageArea {
      width: 100%; height: 100%;
      display: flex; justify-content: center; align-items: center;

      .error {
        font-size: 14px;
        text-align: center;
        color: #efefef;

        svg {
          font-size: 40px;
        }
      }
    }
  }

  .hdw-fade-enter-active {
    animation: hdw-fade-in .3s;
  }

  .hdw-fade-leave-active {
    animation: hdw-fade-out .3s;
  }

  @keyframes hdw-fade-in {
    0% {
      transform: translate3d(0, -20px, 0);
      opacity: 0;
    }
    100% {
      transform: translate3d(0, 0, 0);
      opacity: 1;
    }
  }

  @keyframes hdw-fade-out {
    0% {
      transform: translate3d(0, 0, 0);
      opacity: 1;
    }
    100% {
      transform: translate3d(0, -20px, 0);
      opacity: 0;
    }
  }
</style>
