<template>
  <!-- 如果要修改本组件的template、css，需要仔细看下mouseoverFn函数！！！ -->
  <div
    ref="templateRuler"
    class="template-ruler-container"
    :class="{ 'vertical': isVertical }"
    :style="containerStyle"
    @mousemove="handleMouseOver"
    @mouseleave="handleMouseLeave"
  >
    <div
      v-if="isVertical"
      v-show="vernierVisible"
      ref="verticalRulerLine"
      @mousemove.stop
      class="vertical-ruler-line"
      :style="{
        width: vernierLength * scaleRate + 26 + 'px',
      }"
    ></div>
    <div
      v-else
      v-show="vernierVisible"
      ref="horizontalRulerLine"
      @mousemove.stop
      class="horizontal-ruler-line"
      :style="{
        height: vernierLength * scaleRate + 26 + 'px',
      }"
    ></div>
    <div
      v-for="(item, index) in rulerLength"
      :key="index"
      class="mark"
      :class="{ longer: !(index % 5) }"
      :style="markStyle"
    >
      <span v-if="index && !(index % 5)" class="text">
        {{ index }}<template v-if="index === 5">mm</template>
      </span>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'

const mouseoverFn = _.throttle((vm, e) => {
  const target = e.target
  const className = target.className
  // 算出鼠标点击位置，相对于标尺最左/上侧的偏移量
  if (vm.isVertical) {
    // 竖直方向的标尺
    let top = 0
    if (className.includes('template-ruler-container')) {
      // 容器自身， 取鼠标事件的e.offsetY
      top = e.offsetY
    } else if (className.includes('mark')) {
      // 刻度，则取相对于父元素的相对偏移
      top = target.offsetTop
    } else if (className.includes('text')) {
      // 数字，取 刻度的相对偏移 - 鼠标在数字内的偏移
      top = target.parentNode.offsetTop - e.offsetY
    }
    vm.$refs.verticalRulerLine.style.top = top + 'px' // :style更新太慢，直接操作dom
  } else {
    let left = 0
    if (className.includes('template-ruler-container')) {
      left = e.offsetX
    } else if (className.includes('mark')) {
      left = target.offsetLeft
    } else if (className.includes('text')) {
      // 数字，取 刻度的相对偏移 + 鼠标在数字内的偏移 - 固定数值(调试确定)
      left = target.parentNode.offsetLeft + e.offsetX - 12
    }
    vm.$refs.horizontalRulerLine.style.left = left + 'px'
  }
}, 50)

export default {
  props: {
    // 标尺的长度，单位mm
    rulerLength: {
      default: 100,
      type: Number
    },
    // 游标的长度
    vernierLength: {
      default: 100,
      type: Number
    },
    // 1mm对应的px值，默认1mm对应4px
    scaleRate: {
      default: 4,
      type: Number
    },
    // 标尺方方向是否为垂直方向
    isVertical: {
      default: false,
      type: Boolean
    }
  },
  data () {
    return {
      rulerStyle: {},
      vernierVisible: false // 游标是否可见
    }
  },
  mounted () {
    this.rulerStyle = this.$refs.templateRuler.getBoundingClientRect()
  },
  methods: {
    handleMouseOver (e) {
      this.vernierVisible = true
      mouseoverFn(this, e)
    },
    handleMouseLeave (e) {
      this.vernierVisible = false
    }
  },
  computed: {
    containerStyle () {
      const style = {}
      const length = this.rulerLength * this.scaleRate + 'px'
      if (this.isVertical) {
        style.height = length
      } else {
        style.width = length
      }
      return style
    },
    markStyle () {
      const style = {}
      const space = this.scaleRate - 1 + 'px'
      if (this.isVertical) {
        style['margin-top'] = space
      } else {
        style['margin-left'] = space
      }
      return style
    }
  }
}
</script>

<style scoped lang="less">
.template-ruler-container {
  background: #fff;
  height: 24px;
  border: 1px solid #777;
  display: flex;
  cursor: default;
  position: relative;
  padding-top: 4px;

  &.vertical {
    padding-top: 0;
    padding-left: 4px;
    display: inline-block;

    .mark {
      width: 12px;
      height: 1px;

      &.longer {
        width: 24px;
        height: 1px;
      }

      .text {
        right: -1px;
        bottom: 2px;
        transform: rotate(180deg);
        writing-mode: vertical-rl;
      }
    }
  }

  .mark {
    width: 1px;
    height: 12px;
    background: #777;
    position: relative;

    &.longer {
      height: 24px;
      width: 1px;
    }

    .text {
      font-size: 10px;
      color: #222222;
      line-height: 12px;
      white-space: nowrap;
      zoom: 0.7;
      position: absolute;
      right: 2px;
      bottom: 0;
    }
  }

  .horizontal-ruler-line {
    // 水平标尺上的线
    width: 1px;
    background: red;
    position: absolute;
    top: 0;
    left: 24px;
    z-index: 100;
  }

  .vertical-ruler-line {
    // 垂直标尺上的线
    height: 1px;
    background: red;
    position: absolute;
    top: 24px;
    left: 0;
    z-index: 100;
  }
}
</style>
