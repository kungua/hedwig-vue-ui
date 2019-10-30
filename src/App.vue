<template>
  <div id="app">
    <div class="wrapper">
      <hdw-image :src="src"/>
    </div>

    <div class="wrapper">
      <hdw-loading size="small" bg-color="#cd0000"/>
    </div>

    <button @click="toggleSrc">toggle Src</button>
  </div>
</template>

<script>
    import Image from "./Image"
    import Loading from "./Loading"

    export default {
        name: 'app',
        components: {
            HdwImage: Image,
            HdwLoading: Loading
        },
        data() {
            return {
                sourceSrc: 'http://portal-bucket.nos.netease.com/b36362a77a80995be46bf2497d0cc2d3.jpg?Signature=j0nKGnGMQr5dqn0Cb39d%2F3lTq7Wkw4A8w3XdrLaMJSA%3D&Expires=1886398041&NOSAccessKeyId=7ff9674bca824697ad7c0b8e5a3b074d',
                src: ''
            }
        },
        methods: {
            toggleSrc() {
                this.src = this.src ? '' : this.sourceSrc
                console.log(1)
                console.log(this.src)
            }
        }
    }
</script>

<style lang="scss" scoped>
  .wrapper {
    width: 50px;
    height: 50px;

    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
