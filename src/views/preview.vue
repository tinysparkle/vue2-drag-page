<template>
  <div class="preview-area">
    <h3>Preview</h3>
    <div class="preview-tool">
      <el-tooltip effect="dark" content="10px-72px" placement="top" :open-delay="500" >
          <el-select
              :disabled="!currDraggingItem.name"
              v-model="currDraggingItem.fontSize"
              filterable
              size="small">
              <el-option
                  v-for="i in 72"
                  :key="i"
                  :label="i + 'px'"
                  :value="i">
              </el-option>
          </el-select>
      </el-tooltip>
    </div>
    <div class="preview-block" :style="{ height: model.headerSpace + 'px' }">
      <div class="area-title">Header</div>
      <div v-if="currentFocus === 'headerSpace'" class="border-text-left">
        <span class="text">HeaderSpace</span>
      </div>
      <el-tooltip
        v-for="(item, i) in headerTitleList"
        :key="i"
        effect="dark"
        :content="item.name"
        placement="bottom"
        :open-delay="500">
        <DragItem
            v-if="item.display"
            :data="item"
            :isActived="currDraggingItem === item"
            class="common-drag-item"
            @delete="deleteItem"
            @click.native.stop="currDraggingItem = item">
            <template slot="default">
                <DragItemContent :data="item"/>
            </template>
        </DragItem>
    </el-tooltip>
    </div>
  </div>
</template>

<script>
import DragItem from './dragItem.vue'
import DragItemContent from './dragItemContent.vue'

export default {
  components: {
    DragItem,
    DragItemContent
  },
  props: {
    model: {
      required: true,
      default () {
        return {}
      }
    },
    currentFocus: {
      tyep: String,
      default: ''
    }
  },
  data () {
    return {
      currDraggingItem: {},
      headerTitleList: [
        {
          name: 'Invoce',
          width: 100,
          height: 30,
          initialX: 0, // 初始left
          initialY: 0, // 初始Top
          fontSize: 16,
          display: true
        }
      ]
    }
  },
  methods: {
    deleteItem (data) {
      data.display = false
    }
  }
}
</script>

<style lang="less" scoped>
.preview-area {
  margin-left: 50px;
  width: 100%;
  .preview-tool {
    padding: 10px;
    background: #eee;
  }
  .preview-block {
    position: relative;
    border-top: 2px solid #fff;
    background: papayawhip;
    .area-title {
      font-size: 60px;
      color: #000;
      text-align: center;
    }
    .border-text-left {
      position: absolute;
      top: 0;
      writing-mode: vertical-lr;
      background: orangered;
      left: -1px;
      width: 1px;
      height: 100%;
      display: flex;
      justify-content: center;
      z-index: 10;
      .text {
        color: orange;
        position: absolute;
        background: #fff;
        font-size: 12px;
        left: -9px;
      }
    }
  }
  .common-drag-item {
    background: #FFF;
    border-radius: 2px;
    border: 1px dashed #222222;
    padding: 6px 8px;
}
}

</style>
