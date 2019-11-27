<template>
  <div>
    <el-button
      class="bigger-button"
      :plain="this.isplain"
      :circle="this.iscircle"
      :type="this.type"
      @click="handleClick"
      >{{ text }}</el-button
    >
  </div>
</template>

<script>
const STAT_IDLE = 0
const STAT_WAIT = 1
const STAT_MARK = 2
// eslint-disable-next-line no-unused-vars
const STAT_DONE = 3
const TEXT_IDLE = '🤦'
const TEXT_WAIT = '🙋'
const TEXT_MARK = '🙋'
const TEXT_DONE = '‍🙆‍'
export default {
  name: 'slot-button',
  data () {
    return {
      status: STAT_IDLE,
      text: TEXT_IDLE,
      isplain: true,
      iscircle: true,
      type: 'plain'
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
            : status === STAT_MARK
              ? TEXT_MARK
              : TEXT_DONE
      this.type =
        status === STAT_IDLE
          ? 'plain'
          : status === STAT_WAIT
            ? 'warning'
            : status === STAT_MARK
              ? 'primary'
              : 'success'
    },
    fliphilt () {
      this.iscircle = !this.iscircle
    },
    blink () {
      this.fliphilt()
      setTimeout(this.fliphilt, 1000)
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
