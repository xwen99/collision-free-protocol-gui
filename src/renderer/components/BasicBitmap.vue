<template>
  <div id="basic-bitmap">
    <el-container style="height: 500px; border: 1px solid #eee">
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
            <el-menu
              default-active="1"
              class="el-menu"
              mode="horizontal"
              @select="handleClick"
            >
              <el-menu-item index="1">Basic Bitmap</el-menu-item>
              <el-menu-item index="2">Token Passing</el-menu-item>
              <el-menu-item index="3">Binary Countdown</el-menu-item>
            </el-menu>
          </el-col>
          <el-col :span="6">
            <el-button
              plain
              type="plain"
              icon="el-icon-refresh"
              circle
            ></el-button>
            <el-button
              plain
              type="plain"
              icon="el-icon-setting"
              circle
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
            <el-row type="flex" justify="center">
              <el-col class="el-col el-col-12">
                <el-row type="flex" justify="center" align="middle">
                  <div class="sub-title">Broadcast the Desire to Transmit</div>
                </el-row>
                <el-row type="flex" justify="center" align="middle">
                  <el-button
                    class="bigger-button"
                    plain
                    circle
                    disabled
                    type="primary"
                    >🙋‍</el-button
                  >
                  <el-button
                    class="bigger-button"
                    plain
                    circle
                    disabled
                    type="primary"
                    >🙋‍</el-button
                  >
                  <el-button
                    class="bigger-button"
                    plain
                    circle
                    disabled
                    type="plain"
                    >🤦‍</el-button
                  >
                </el-row>
              </el-col>
              <el-col class="el-col el-col-12">
                <div class="sub-title">Transmit the Data</div>
                <div class="text item">
                  <el-progress type="circle" :percentage="30"></el-progress>
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
export default {
  data () {
    return {
      active: true,
      logo: require('../assets/logo.png')
    }
  },
  methods: {
    handleClick (tab, event) {
      console.log(tab, event)
    }
  },
  name: 'basic-bitmap'
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

.el-col:not(:last-child) {
  border-right: 1px solid rgba(224, 230, 237, 0.5);
}

.el-col-12 {
  width: 50%;
}

.basic-bitmap-demo {
  text-align: center;
}

.bigger-button {
  font-size: 20px;
}
</style>
