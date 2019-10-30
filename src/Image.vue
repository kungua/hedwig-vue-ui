<template>
  <div class="hdw-image">
    <div v-if="loading">
      <hdw-loading/>
    </div>
    <div v-else-if="error">
      <hdw-icon name="fail"></hdw-icon>
    </div>
    <img v-else :src="src" :style="imageStyles" alt="这是一张图片">
  </div>
</template>

<script>
import Loading from './Loading'

export default {
  name: 'HedwigImage',
  components: {
    HdwLoading: Loading
  },
  props: {
    src: {
      type: String,
      default: ''
    },
    imageLoading: {
      type: Boolean,
      default: true
    },
    imageStyles: {
      type: Object,
      default: () => ({
        width: '100%',
        height: '100%'
      })
    }
  },
  data() {
    return {
      loading: true,
      error: false
    }
  },
  watch: {
    src() {
      this.loadImage()
    }
  },
  methods: {
    onLoad() {
      this.loading = false
      this.$emit('update:image-loading', this.loading)
    },
    onError() {
      this.loading = false
      this.$emit('update:image-loading', this.loading)
      this.error = true
    },
    loadImage() {
      this.loading = true
      this.$emit('update:image-loading', this.loading)
      this.error = false

      const img = new Image()
      img.onload = e => this.onLoad(e, img)
      img.onerror = e => this.onError(e, img)
      img.src = this.src
    }
  },
  mounted() {
    this.loadImage()
  }
}
</script>

<style lang="scss" scoped>
  .hdw-image {
    width: 100%; height: 100%;

    img {
      object-fit: fill;
    }
  }
</style>
