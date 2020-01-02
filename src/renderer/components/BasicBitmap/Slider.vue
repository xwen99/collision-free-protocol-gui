<template>
  <div class="block">
    <el-row type="flex"
            align="middle">
      <el-col span="6"
              type="flex"
              align="start">
        <span class="demonstration">Efficiency</span>
      </el-col>
      <el-col span="24">
        <el-progress :text-inside="true"
                     :stroke-width="20"
                     :percentage="percentage"
                     :color="customColorMethod"
                     style="float: right; width: 90%;"></el-progress>
      </el-col>
    </el-row>
    <el-row type="flex"
            align="middle">
      <el-col span="6"
              type="flex"
              align="start">
        <span class="demonstration">Slot Number</span>
      </el-col>
      <el-col span="24">
        <el-slider v-model="value1"
                   max="64"
                   @input="value1Change"
                   style="float: right; width: 90%;">
        </el-slider>
      </el-col>
    </el-row>
    <el-row type="flex"
            align="middle">
      <el-col span="6"
              type="flex"
              align="start">
        <span class="demonstration">Frequency</span>
      </el-col>
      <el-col span="24">
        <el-slider v-model="value2"
                   :format-tooltip="percentFormat"
                   @input="value2Change"
                   style="float: right; width: 90%;">
        </el-slider>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'slider',
  data () {
    return {
      value1: 64,
      value2: 50,
      percentage: 0,
      customColor: '#409eff',
      customColors: [
        { color: '#f56c6c', percentage: 20 },
        { color: '#e6a23c', percentage: 40 },
        { color: '#5cb87a', percentage: 60 },
        { color: '#1989fa', percentage: 80 },
        { color: '#6f7ad3', percentage: 100 }
      ]
    }
  },
  methods: {
    percentFormat (val) {
      return val.toFixed(2) + '%'
    },
    customColorMethod (percentage) {
      if (percentage < 30) {
        return '#909399'
      } else if (percentage < 70) {
        return '#e6a23c'
      } else {
        return '#67c23a'
      }
    },
    value1Change () {
      this.$emit('getSlotNum', this.value1)
    },
    value2Change () {
      this.$emit('getFrequency', this.value2 / 100)
    }
  }
}
</script>

<style>
.block {
  padding: 30px 24px;
  overflow: hidden;
  border-bottom: 1px solid #eff2f6;
}
.block:last-child {
  border-bottom: none;
}
.demonstration {
  font-size: 14px;
  color: #8492a6;
  line-height: 44px;
}
</style>
