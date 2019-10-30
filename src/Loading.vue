<template>
  <div :class="{'hdw-loading-small': size === 'small'}" class="load-container">
    <div v-for="i in 3"
         :key="i"
         :class="`container${i}`"
         class="container">
      <div v-for="j in 4"
           :key="`c-${j}`"
           :style="{'background-color': bgColor}"
           :class="`circle${j}`"
           class="circle"></div>
    </div>
  </div>
</template>

<script>
    export default {
        name: 'HedwigLoading',
        props: {
            size: {
                type: String,
                default: 'normal'
            },
            bgColor: {
                type: String,
                default: '#e8e8e8'
            }
        }
    }
</script>

<style lang="scss" scoped>
  .load-container {
    margin: 0 auto;
    width: 48px;
    height: 48px;
    position: relative;

    .container {
      position: absolute;
      width: 100%;
      height: 100%;

      .circle {
        width: 12px;
        height: 12px;
        background-color: #e8e8e8;

        border-radius: 100%;
        position: absolute;
        -webkit-animation: bouncedelay 1.2s infinite ease-in-out;
        animation: bouncedelay 1.2s infinite ease-in-out;
        -webkit-animation-fill-mode: both;
        animation-fill-mode: both;
      }

      .circle1 {
        top: 0;
        left: 0;
      }

      .circle2 {
        top: 0;
        right: 0;
      }

      .circle3 {
        right: 0;
        bottom: 0;
      }

      .circle4 {
        left: 0;
        bottom: 0;
      }
    }

    .container1 {
      .circle2 {
        -webkit-animation-delay: -0.9s;
        animation-delay: -0.9s;
      }

      .circle3 {
        -webkit-animation-delay: -0.6s;
        animation-delay: -0.6s;
      }

      .circle4 {
        -webkit-animation-delay: -0.3s;
        animation-delay: -0.3s;
      }
    }

    .container2 {
      -webkit-transform: rotateZ(45deg);
      transform: rotateZ(45deg);

      .circle1 {
        -webkit-animation-delay: -1.1s;
        animation-delay: -1.1s;
      }

      .circle2 {
        -webkit-animation-delay: -0.8s;
        animation-delay: -0.8s;
      }

      .circle3 {
        -webkit-animation-delay: -0.5s;
        animation-delay: -0.5s;
      }

      .circle4 {
        -webkit-animation-delay: -0.2s;
        animation-delay: -0.2s;
      }
    }

    .container3 {
      -webkit-transform: rotateZ(90deg);
      transform: rotateZ(90deg);

      .circle1 {
        -webkit-animation-delay: -1.0s;
        animation-delay: -1.0s;
      }

      .circle2 {
        -webkit-animation-delay: -0.7s;
        animation-delay: -0.7s;
      }

      .circle3 {
        -webkit-animation-delay: -0.4s;
        animation-delay: -0.4s;
      }

      .circle4 {
        -webkit-animation-delay: -0.1s;
        animation-delay: -0.1s;
      }
    }

    &.hdw-loading-small {
      width: 24px;
      height: 24px;

      .container {
        .circle {
          width: 6px;
          height: 6px;
        }
      }
    }
  }

  @-webkit-keyframes bouncedelay {
    0%, 80%, 100% {
      -webkit-transform: scale(0.0)
    }
    40% {
      -webkit-transform: scale(1.0)
    }
  }

  @keyframes bouncedelay {
    0%, 80%, 100% {
      transform: scale(0.0);
      -webkit-transform: scale(0.0);
    }
    40% {
      transform: scale(1.0);
      -webkit-transform: scale(1.0);
    }
  }
</style>
