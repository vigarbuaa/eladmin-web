<template>
  <el-row class="home" :gutter="20">
    <el-col :span="8">
      <el-card shadow="hover">
        <div class="user">
          <img :src="userImg">
          <div class="userinfo">
            <p class="name">Nick</p>
            <p class="access">管理员</p>
          </div>
        </div>
        <div class="login-info">
          <p>上次登入时间<span>2019-12-20</span></p>
          <p>上次登入时间<span>2019-12-20</span></p>
        </div>
      </el-card>
      <el-card shadow="hover" style="height:522px; margin-top:20px">
        mouse悬浮时显示
      </el-card>
    </el-col>
    <el-col :span="16">
      <div class="num">
        <el-card v-for="item in 6" :key="item" shadow="hover">
          <i class="icon" />
          <div class="detail">
            <p class="num">1234</p>
            <p class="txt">今日支付订单</p>
          </div>
        </el-card>
      </div>
      <el-card shadow="hover">
        <div style="height: 280px;" />
      </el-card>
      <div class="graph">
        <el-card shadow="hover">
          <div style="height: 260px;" />
        </el-card>
        <el-card shadow="hover">
          <div style="height: 260px;" />
        </el-card>
        <div />
      </div>
    </el-col>
  </el-row>
</template>
<script>
export default {
  name: 'Debug',
  components: {},
  data() {
    return {
      userImg: require('../../assets/images/logo.png')
    }
  }
}
</script>
<style lang="scss" scoped>
  @import '~@/assets/scss/debug'
</style>
