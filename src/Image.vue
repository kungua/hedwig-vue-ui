<template>
  <div @click="$emit('click')" class="hdw-image">
    <div v-if="loading">
      <hdw-loading size="small" bg-color="#f36258"/>
    </div>
    <div v-else-if="error" class="hdw-image__error">
      加载失败
    </div>
    <img v-else :src="src" :style="imageStyles">
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
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;

    &__error {
      width: 100%;
      height: 100%;
      background-color: #f4f6f9;
      color: #d9dce1;
      font-size: 12px;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    img {
      object-fit: fill;
    }
  }
</style>
