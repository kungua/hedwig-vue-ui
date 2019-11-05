<template>
  <div class="hdw-layout-double-flying-wing">
    <div :class="getClass('content-box')">
      <div :class="getClass('content')">
        <slot></slot>
      </div>
    </div>
    <div :class="getClass('left')">
      导航栏
    </div>
    <div :class="getClass('right')">
      配置栏
    </div>
  </div>
</template>

<script>
export default {
  name: 'LayoutDoubleFlyingWing',
  methods: {
    getClass(str) {
      return `hdw-layout-double-flying-wing__${str}`
    }
  }
}
</script>

<style lang="scss" scoped>

.hdw-layout-double-flying-wing {
  height: 100vh;
  min-width: 1200px;
  color: white;

  > div {
    height: 100vh;
    float: left;
    position: relative;
  }

  &__content-box {
    width: 100%;
    background: #F2F0D5;
    overflow: auto;
  }

  &__content {
    margin: 0 200px;
    color: #999;
    overflow-x: auto;
  }

  &__left {
    width: 200px;
    margin-left: -100%;
    background: #03658C;
  }

  &__right {
    width: 200px;
    background: #5EA4BF;
    margin-left: -200px;
  }
}
</style>