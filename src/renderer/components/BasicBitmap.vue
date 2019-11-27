<template>
  <div id="basic-bitmap">
    <el-container>
      <el-header class="header">
        <el-row gutter="10">
          <el-col :span="8" type="flex" justify="center" align="middle">
            <el-image
              style="width: 300px; height: auto; margin:10px;"
              :src="logo"
              fit="contain"
            ></el-image>
          </el-col>
          <el-col :span="10" justify="space-around" align="middle">
            <el-menu
              default-active="1"
              class="el-menu"
              mode="horizontal"
              @select="this.handleChangePage"
            >
              <el-menu-item index="1">{{ this.METHODS[0] }}</el-menu-item>
              <el-menu-item index="2">{{ this.METHODS[1] }}</el-menu-item>
              <el-menu-item index="3">{{ this.METHODS[2] }}</el-menu-item>
            </el-menu>
          </el-col>
          <el-col :span="6" justify="center" align="middle">
            <el-button
              plain
              type="plain"
              icon="el-icon-video-play"
              circle
              @click="handleStart"
            ></el-button>
            <el-button
              plain
              type="plain"
              icon="el-icon-refresh"
              circle
              @click="handleFresh"
            ></el-button>
            <el-button
              plain
              type="plain"
              icon="el-icon-more"
              circle
            ></el-button>
          </el-col>
        </el-row>
      </el-header>

      <el-main>
        <el-row type="flex" justify="center" align="middle">
          <div class="title">{{ this.getTitle(this.page) }}</div>
        </el-row>
        <div class="basic-bitmap-demo">
          <el-card class="box-card" shadow="hover">
            <el-row type="flex" justify="center" align="middle">
              <el-col class="el-col el-col-12">
                <div class="block">
                  <el-row type="flex" justify="center" align="middle">
                    <div class="sub-title">
                      1: Broadcast the Desire to Transmit
                    </div>
                  </el-row>
                  <span v-for="i in Math.ceil(slotNum / slotPerLine)" :key="i">
                    <el-row type="flex" justify="center" align="middle">
                      <span
                        v-for="j in Math.min(
                          slotPerLine,
                          slotNum - (i - 1) * slotPerLine
                        )"
                        :key="j"
                      >
                        <slot-button ref="slotbtn"></slot-button>
                      </span>
                    </el-row>
                  </span>
                </div>
              </el-col>
              <el-col class="el-col el-col-12">
                <slider
                  ref="sliders"
                  @getSlotNum="getSlotNum"
                  @getFrequency="getFrequency"
                ></slider>
                <div class="block">
                  <div class="sub-title">2: Transmit the Data</div>
                  <div class="text item">
                    <el-progress
                      type="circle"
                      :percentage="this.transmitPercentage"
                      width="200"
                    ></el-progress>
                  </div>
                </div>
              </el-col>
            </el-row>
          </el-card>
        </div>
      </el-main>
    </el-container>
  </div>
</template>
// ! TODOs：比特位，单步控制，时间可调
<script>
import SlotButton from './BasicBitmap/SlotButton'
import Slider from './BasicBitmap/Slider'
const STAT_IDLE = 0
const STAT_WAIT = 1
// eslint-disable-next-line no-unused-vars
const STAT_MARK = 2
// eslint-disable-next-line no-unused-vars
const STAT_DONE = 3
export default {
  name: 'basic-bitmap',
  components: { 'slot-button': SlotButton, slider: Slider },
  data () {
    return {
      slotNum: 64,
      slotPerLine: 8,
      frequency: 0.5,
      start: 0,
      isTransmitting: 0,
      transmitTime: 0,
      totalTime: 0,
      transmitPercentage: 0,
      METHODS: ['Basic Bitmap', 'Token Passing', 'Binary Countdown'],
      page: 0,
      logo: require('../assets/logo.png')
    }
  },
  methods: {
    reset () {
      this.start = 0
      this.isTransmitting = 0
      this.transmitTime = 0
      this.totalTime = 0
      this.transmitPercentage = 0
      this.slotNum = 64
      this.slotPerLine = 8
      this.frequency = 0.5
      this.page = 0
      this.$refs.sliders.value1 = 64
      this.$refs.sliders.value2 = 50
      this.$refs.sliders.percentage = 0
      for (let i = 0; i < this.slotNum; i++) {
        this.$refs.slotbtn[i].reset()
      }
    },
    getTitle (page) {
      return 'This is The ' + this.METHODS[page] + ' Method.'
    },
    handleChangePage (key, keypath) {
      this.page = key === '1' ? 0 : key === '2' ? 1 : 2
    },
    handleStart (tab, event) {
      this.start = 1
      this.mainLoop()
    },
    async handleFresh (tab, event) {
      this.reset()
      await this.sleep(1000)
      this.reset()
    },
    getSlotNum: function (value) {
      this.slotNum = value
    },
    getFrequency: function (value) {
      this.frequency = value
    },
    flipCoin () {
      return Math.random() <=
        1 - Math.pow(1 - this.frequency, 1.0 / this.slotNum)
        ? 1
        : 0
    },
    sleep (ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    },
    checkStat (i, pos, markList) {
      let coin = this.flipCoin()
      if (this.$refs.slotbtn[i].status === STAT_IDLE && coin === 1) {
        this.$refs.slotbtn[i].changeStatus(STAT_WAIT)
      } else if (this.$refs.slotbtn[i].status === STAT_WAIT && i === pos) {
        this.$refs.slotbtn[i].changeStatus(STAT_MARK)
        markList.push(i)
      } else if (this.$refs.slotbtn[i].status === STAT_DONE) {
        if (coin === 1) {
          this.$refs.slotbtn[i].changeStatus(STAT_WAIT)
        } else {
          this.$refs.slotbtn[i].changeStatus(STAT_IDLE)
        }
      }
    },
    async mainLoop () {
      let t = 0
      let pos
      // eslint-disable-next-line no-unused-vars
      let markList = []
      let curTransTime = 0
      while (this.start === 1) {
        this.isTransmitting = t < this.slotNum ? 0 : 1
        if (this.isTransmitting === 0) {
          pos = t
          this.$refs.slotbtn[pos].blink()
          this.checkStat(pos, pos, markList)
        } else {
          pos = markList.shift()
          this.$refs.slotbtn[pos].blink()
          this.$refs.slotbtn[pos].changeStatus(STAT_DONE)
        }
        for (let i = 0; i < this.slotNum; i++) {
          if (i === pos) {
            continue
          }
          this.checkStat(i, t, markList)
        }
        t++
        this.totalTime++
        if (this.isTransmitting === 1) {
          this.transmitTime++
          this.transmitPercentage = (
            ((t - this.slotNum) * 100) /
            curTransTime
          ).toFixed(2)
        } else {
          this.transmitPercentage = 0
        }
        this.$refs.sliders.percentage = (
          (this.transmitTime * 100) /
          this.totalTime
        ).toFixed(2)
        if (t === this.slotNum) {
          curTransTime = markList.length
        } else if (t === this.slotNum + curTransTime) {
          t = 0
        }
        await this.sleep(1000)
      }
    }
  }
}
</script>

<style scoped>
.header {
  height: 80px;
  background-color: #fff;
  color: #fff;
  top: 0;
  left: 0;
  width: 100%;
  line-height: 80px;
  z-index: 100;
  position: relative;
}
.container {
  height: 100%;
  box-sizing: border-box;
  border-bottom: 1px solid #dcdfe6;
}
.title {
  display: flex;
  flex-direction: column;
  color: #2c3e50;
  font-size: 20px;
  font-weight: bold;
  margin-top: 10px;
  margin-bottom: 20px;
}

.sub-title {
  margin-bottom: 10px;
  font-size: 14px;
  color: #8492a6;
}

.el-col:not(:first-child) {
  border-left: 1px solid rgba(224, 230, 237, 0.5);
}

.el-col-12 {
  width: 50%;
}

.basic-bitmap-demo {
  text-align: center;
}

.block {
  padding: 30px 24px;
  overflow: hidden;
  border-bottom: 1px solid #eff2f6;
}

.block:frist-child {
  border-bottom: none;
}
</style>
