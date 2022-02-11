<template>
  <div
    class="drag-item"
    @mousedown.stop.prevent="handleMousedown"
    :style="{
      'z-index': isActived ? 10 : 1,
      top: data.initialY + 'px',
      left: data.initialX + 'px',
      width: data.width + 'px',
      height: data.height + 'px'
    }">
    <slot></slot>
    <div v-if="isActived">
      <span class="el-icon-error icon-delete" @click.stop="deleteItem"></span>
      <div
        v-for="(stick, index) in sticks"
        :key="index"
        class="vdr-stick"
        :class="['vdr-stick-' + stick]"
        @mousedown.stop.prevent="stickDown(stick, $event)">
    </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'dragItem',
  props: {
    data: {
      required: true,
      default () {
        return {}
      }
    },
    isActived: {
      type: Boolean,
      default: false
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  mounted () {
    // 组件渲染完，绑定事件
    document.addEventListener('mousemove', this.handleMousemove)
    document.addEventListener('mouseup', this.handleMouseup)
  },
  data () {
    return {
      dragging: false,
      startX: 0,
      startY: 0,
      maxLeft: 0,
      maxTop: 0,
      maxWidth: 0,
      maxHeight: 0,
      isResizing: false,
      sticks: ['mr'],
      resizingStick: ''
    }
  },
  methods: {
    // 鼠标点击， 开始拖拽
    handleMousedown (e) {
      if (this.disabled) return false
      this.dragging = true
      this.startX = e.clientX
      this.startY = e.clientY
      const parent = this.$el.parentElement // 父元素
      const curItem = this.$el // 当前元素
      this.maxLeft = parent.offsetWidth - curItem.offsetWidth // x轴可移动最远距离
      this.maxTop = parent.offsetHeight - curItem.offsetHeight // y轴可移动最远距离
    },
    stickDown (stick, e) { // 开始resize
      this.isResizing = true
      this.resizingStick = stick
      this.startX = e.clientX
      this.startY = e.clientY
      const parent = this.$el.parentElement
      this.maxWidth = parent.offsetWidth - this.data.initialX
    },
    handleMousemove (e) {
      e.preventDefault()
      if (this.isResizing) {
        let width = this.data.width + (e.clientX - this.startX)
        width = width >= 0 ? width : 0
        const parentBounding = this.$el.parentElement.getBoundingClientRect()
        if (e.clientX > parentBounding.right) {
          this.data.width = this.maxWidth
        } else if (width <= this.maxWidth) {
          this.data.width = width
        }
        this.startX = e.clientX
        this.startY = e.clientY
      } else if (this.dragging) {
        const left = this.data.initialX + (e.clientX - this.startX) // x轴移动的距离
        const top = this.data.initialY + (e.clientY - this.startY) // y轴移动的距离
        const parent = this.$el.parentElement
        const parentBounding = parent.getBoundingClientRect() // 方法返回元素的大小及其相对于视口的位置。
        if (e.clientX < parentBounding.left) { // 鼠标X坐标不在父元素范围内（在父元素左侧外），left置0
          this.data.initialX = 0
        } else if (e.clientX > parentBounding.right) { // 鼠标X坐标不在父元素范围内（在父元素右侧外），left设置为可移动最远距离
          this.data.initialx = this.maxLeft
        } else if (left >= 0 && left <= this.maxLeft) { // 鼠标x坐标在父元素范围内
          this.data.initialX = left
        }

        // 鼠标移动时y坐标的判断 同x坐标判断逻辑一样
        if (e.clientY < parentBounding.top) {
          this.data.initialY = 0
        } else if (e.clientY > parentBounding.bottom) {
          this.data.initialY = this.maxTop
        } else if (top >= 0 && top <= this.maxTop) {
          this.data.initialY = top
        }

        // 将鼠标移动后的坐标赋值给开始坐标
        this.startX = e.clientX
        this.startY = e.clientY
      }
    },
    handleMouseup (e) {
      e.preventDefault()
      e.stopPropagation()
      this.dragging = false
      this.isResizing = false
    },
    deleteItem () {
      this.$emit('delete', this.data)
    }
  }
}
</script>

<style lang="less" scoped>
  .drag-item {
    position: absolute;
    font-size: 13px;
    color: #222222;
    line-height: 1;
    cursor: pointer;
    box-sizing: border-box;

    .vdr-stick {
        cursor: ew-resize;
        box-sizing: border-box;
        position: absolute;
        font-size: 1px;
        background: #ffffff;
        border: 1px solid #6c6c6c;
        box-shadow: 0 0 2px #bbb;
        width: 8px;
        height: 8px;
    }

    .vdr-stick-mr {
        top: 50%;
        right: 0;
        transform: translate(50%, -50%);
    }

    .icon-delete {
      position: absolute;
      top: -8px;
      right: -8px;
      font-size: 16px;
      background: #FFF;
      border-radius: 50%;
    }
  }
</style>
