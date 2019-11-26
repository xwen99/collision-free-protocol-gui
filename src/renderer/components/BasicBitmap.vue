<template>
  <div id="basic-bitmap">
    <el-container style="border: 1px solid #eee">
      <el-header>
        <el-row type="flex" justify="center" align="middle" gutter="24">
          <el-col :span="6">
            <el-image
              style="width: 200px; height: auto"
              :src="logo"
              fit="contain"
            ></el-image>
          </el-col>
          <el-col :span="10">
            <el-menu default-active="1" class="el-menu" mode="horizontal">
              <el-menu-item index="1">Basic Bitmap</el-menu-item>
              <el-menu-item index="2">Token Passing</el-menu-item>
              <el-menu-item index="3">Binary Countdown</el-menu-item>
            </el-menu>
          </el-col>
          <el-col :span="6">
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
          <div class="title">This is the Basic Bitmap Method.</div>
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
                      :percentage="30"
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

<script>
import SlotButton from './BasicBitmap/SlotButton'
import Slider from './BasicBitmap/Slider'
export default {
  name: 'basic-bitmap',
  components: { 'slot-button': SlotButton, slider: Slider },
  data () {
    return {
      slotNum: 64,
      slotPerLine: 8,
      frequency: 0.0,
      start: 0,
      isTransmitting: 0,
      logo: require('../assets/logo.png')
    }
  },
  methods: {
    handleStart (tab, event) {
      this.start = 1
      this.mainLoop()
    },
    handleFresh (tab, event) {
      this.start = 0
      this.isTransmitting = 0
      this.slotNum = 64
      this.slotPerLine = 8
      this.frequency = 0.0
      this.$refs.sliders.value1 = 64
      this.$refs.sliders.value2 = 0
      for (let i = 0; i < Math.ceil(this.slotNum / this.slotPerLine); i++) {
        for (
          let j = 0;
          j <
          Math.min(this.slotPerLine, this.slotNum - (i - 1) * this.slotPerLine);
          j++
        ) {
          this.$refs.slotbtn[i * this.slotPerLine + j].changeStatus(0)
        }
      }
    },
    getSlotNum: function (value) {
      this.slotNum = value
    },
    getFrequency: function (value) {
      this.frequency = value
    },
    flipCoin () {
      return Math.random() <= this.frequency ? 1 : 0
    },
    mainLoop () {
      let t = 0
      // eslint-disable-next-line no-unused-vars
      let totalT = 0
      let curWaitting = 0
      let waitList = []
      let transmitTime = 0
      while (this.start) {
        this.isTransmitting = t < this.slotNum ? 0 : 1
        if (t === this.slotNum) {
          transmitTime = curWaitting
        }
        let i, j
        if (this.isTransmitting === 0) {
          i = t / this.slotNum
          j = t % this.slotNum
          this.$refs.slotbtn[i * this.slotPerLine + j].blink()
        } else {
          i = waitList[0][0]
          j = waitList[0][1]
          this.$refs.slotbtn[i * this.slotPerLine + j].blink()
          waitList.shift()
          this.$refs.slotbtn[i * this.slotPerLine + j].changeStatus(2)
          curWaitting--
        }
        for (let i = 0; i < Math.ceil(this.slotNum / this.slotPerLine); i++) {
          for (
            let j = 0;
            j < Math.min(this.slotPerLine, this.slotNum - i * this.slotPerLine);
            j++
          ) {
            var coin = this.flipCoin()

            if (
              this.$refs.slotbtn[i * this.slotPerLine + j].status === 0 &&
              coin === 1
            ) {
              this.$refs.slotbtn[i * this.slotPerLine + j].changeStatus(1)
              waitList.push([i, j])
              curWaitting++
            } else if (
              this.$refs.slotbtn[i * this.slotPerLine + j].status === 2
            ) {
              if (coin === 1) {
                this.$refs.slotbtn[i * this.slotPerLine + j].changeStatus(1)
                waitList.push([i, j])
                curWaitting++
              } else {
                this.$refs.slotbtn[i * this.slotPerLine + j].changeStatus(0)
              }
            }
          }
        }
        t++
        if (t === this.slotNum + transmitTime) {
          totalT += t
          t = 0
        }
      }
    }
  }
}
</script>

<style>
.title {
  display: flex;
  flex-direction: column;
  color: #2c3e50;
  font-size: 20px;
  font-weight: bold;
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
