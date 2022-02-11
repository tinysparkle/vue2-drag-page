<template>
  <div class="main">
    <div class="paper-size-area">
      <h3>Paper Size</h3>
      <el-form label-position="top" label-width="80px" :model="sizeModel">
        <el-row :gutter="12">
          <el-col :span="12">
            <el-form-item label="Width">
              <el-input
                @focus="handlefocus('width')"
                @blur="handleBlur"
                v-model="sizeModel.width">
                <div slot="suffix">mm</div>
              </el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="Length">
              <el-input
                @focus="handlefocus('length')"
                @blur="handleBlur"
                v-model="sizeModel.length">
                <div slot="suffix">mm</div>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="12">
          <el-col :span="12">
            <el-form-item label="Header Spacing">
              <el-input
                @focus="handlefocus('headerSpace')"
                @blur="handleBlur"
                v-model="sizeModel.headerSpace">
                <div slot="suffix">mm</div>
              </el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="Summary Spacing">
              <el-input
                @focus="handlefocus('summarySpace')"
                @blur="handleBlur"
                v-model="sizeModel.summarySpace">
                <div slot="suffix">mm</div>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>
    <Preview :model="sizeModel" :currentFocus="currentFocus"/>
  </div>
</template>

<script>
import Preview from './preview.vue'
export default {
  name: 'index',
  components: {
    Preview
  },
  data () {
    return {
      sizeModel: {
        width: '',
        length: '',
        headerSpace: '',
        summarySpace: ''
      },
      currentFocus: ''
    }
  },
  methods: {
    handlefocus (str) {
      this.currentFocus = str
    },
    handleBlur () {
      this.currentFocus = ''
    }
  }
}
</script>

<style scoped>
.main {
  height: 100%;
  width: 100%;
  display: flex;
}
.paper-size-area {
  width: 50%;
}
</style>
