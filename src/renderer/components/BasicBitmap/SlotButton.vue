<template>
  <div>
    <el-button
      class="bigger-button"
      :plain="this.isplain"
      :circle="this.iscircle"
      :type="this.type"
      :autofocus="this.isfocus"
      @click="handleClick"
      >{{ text }}</el-button
    >
  </div>
</template>

<script>
const STAT_IDLE = 0
const STAT_WAIT = 1
// eslint-disable-next-line no-unused-vars
const STAT_DONE = 2
const TEXT_IDLE = '🤦'
const TEXT_WAIT = '🙋'
const TEXT_DONE = '‍🙆‍'
export default {
  name: 'slot-button',
  data () {
    return {
      status: STAT_IDLE,
      text: TEXT_IDLE,
      isplain: true,
      iscircle: true,
      type: 'plain',
      isfocus: false
    }
  },
  methods: {
    // actually, make sure it is not clickable
    handleClick (tab, event) {
      console.log(tab, event)
      this.changeStatus((this.status + 1) % 3)
    },
    changeStatus (status) {
      this.status = status
      this.text =
        status === STAT_IDLE
          ? TEXT_IDLE
          : status === STAT_WAIT
            ? TEXT_WAIT
            : TEXT_DONE
      this.type =
        status === STAT_IDLE
          ? 'plain'
          : status === STAT_WAIT
            ? 'primary'
            : 'success'
    },
    blink () {
      this.isfocus = true
      clearTimeout(this.timer)
      this.timer = setTimeout(() => {
        console.log('ok')
      }, 1000)
      this.isfocus = false
    }
  }
}
</script>

<style>
.bigger-button {
  margin: 5px;
  font-size: 20px;
}
</style>
