<template>
  <div
    class="drag-item-content"
    :style="{ 'font-size': data.fontSize + 'px' }">
    {{ data.name }}
  </div>
</template>

<script>
export default {
  name: 'dragItemContent',
  props: {
    data: {
      require: true,
      default () {
        return {}
      }
    }
  },
  data () {
    return {

    }
  }
}
</script>

<style lang="less" scoped>
.drag-item-content {
  height: 100%;
  width: 100%;
}
</style>
