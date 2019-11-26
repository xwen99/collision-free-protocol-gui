<template>
  <el-button class="bigger-button" plain circle disabled type="primary"
    >🙋‍</el-button
  >
</template>

<script>
export default {
  name: 'slot-button',
  data () {
    return {}
  },
  methods: {
    // actually, make sure it is not clickable
    handleClick (tab, event) {
      console.log(tab, event)
    }
  }
}
</script>

<style>
.bigger-button {
  font-size: 20px;
}
</style>
